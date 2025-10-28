# Image URI resolution

`ImageUriResolver` mirrors the logic found in the original Node.js library. It
retrieves the dynamic components required to construct canonical image URLs and
stores them in process-wide state so subsequent lookups are instantaneous.

## Synchronising resolver state

Before resolving any image URLs, call one of the synchronisation helpers:

```python
from hitomi import ImageUriResolver

ImageUriResolver.synchronize()
# or
await ImageUriResolver.async_synchronize()
```

Both methods fetch `https://ltn.hitomi.la/gg.js`, extract the path code, the
`startsWithA` flag, and the set of valid subdomain codes, then cache the values
in `hitomi.constants.IMAGE_URI_PARTS`.

Use `ImageUriResolver.clear_cache()` to reset the cache. The next call to either
synchronisation helper will re-fetch the metadata.

## Resolving image URLs

```python
from hitomi import ImageUriResolver, get_gallery

ImageUriResolver.synchronize()
gallery = get_gallery(123456)
file = gallery.files[0]

uri = ImageUriResolver.get_image_uri(file, "webp")
print(uri)
```

Available keyword parameters:

- `extension`: one of `"webp"`, `"avif"`, or `"jxl"`.
- `is_thumbnail`: request a thumbnail-sized asset.
- `is_small`: request the `small` resolution variant when available.

The helper validates file capabilities (e.g. whether a `webp` derivative exists)
and raises `HitomiError` with `ErrorCode.INVALID_VALUE` if the combination is
unsupported.

## Thread safety and asyncio usage

Synchronisation is safe to call from multiple threads; only the first call will
trigger a network request. The asynchronous variant uses the same lock to avoid
races when awaited concurrently. After priming the cache you can use
`ImageUriResolver.get_image_uri` freely from synchronous or asynchronous code.
