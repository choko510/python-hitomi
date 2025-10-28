# Usage guide

This document provides an overview of the high-level helpers exposed at the
package root. Each helper has both synchronous and asynchronous variants to
match different application architectures.

> **Note**
> All examples require Python 3.11 or newer.

## Galleries

```python
from hitomi import get_gallery, async_get_gallery

gallery = get_gallery(123456)
print(gallery.title.display)

async def fetch_async_gallery(gallery_id: int):
    gallery = await async_get_gallery(gallery_id)
    return gallery.title.display
```

The returned `Gallery` object mirrors the structure provided by the Hitomi.la
API. Refer to `hitomi/types.py` for the fully typed data model.

## Gallery identifiers

```python
from hitomi import get_gallery_ids, async_get_gallery_ids, get_parsed_tags

query = {
    "tags": get_parsed_tags("language:english female:sole female"),
}
ids = get_gallery_ids(query)

async def search_async():
    return await async_get_gallery_ids(query)
```

Each result is an integer ID. For incremental syncing you can pass a
`"before"` or `"after"` key with a gallery ID to page through the index. The
helpers internally cache gallery index nodes to avoid re-fetching data for
adjacent lookups.

## Tag listings

```python
from hitomi import get_tags, async_get_tags

tag_listing = get_tags({"type": "artist"})
for tag in tag_listing["list"][:5]:
    print(tag["tag"])
```

When `startsWith` is provided, the helper will fetch the appropriate index page
(e.g. `tagss-a.html`). For language tags the filter must be omitted to match the
behaviour of the upstream API.

## URI helpers

Synchronous and asynchronous helpers exist for every network-heavy operation in
`hitomi.uri`. For example, `async_get_gallery_uri` returns the same string as
`get_gallery_uri` but can run concurrently with other fetches when used with
`asyncio.gather`.

The [`ImageUriResolver`](./uri-resolution.md) class requires a one-time
synchronisation call before resolving image URLs. Reuse the same process or
application instance to benefit from its in-memory cache.

## Error handling

All helpers raise `HitomiError` with a typed `ErrorCode` when the request cannot
be fulfilled. You can inspect the `code` attribute to customise retry logic or
surfaced messages:

```python
from hitomi import HitomiError
from hitomi.constants import ErrorCode

try:
    get_gallery(42)
except HitomiError as error:
    if error.code is ErrorCode.REQUEST_REJECTED:
        print("Request was blocked; retrying later")
    else:
        raise
```

## Testing your installation

When contributing patches, run the lightweight compilation check to verify the
project imports cleanly:

```bash
python -m compileall hitomi
```
