<div align="center">
  <img src="https://cdn.h2owr.xyz/images/node-hitomi/banner.png" alt="banner" />
  <h1>python-hitomi</h1>
  <p>Fully-typed Hitomi.la client inspired by the original Node.js implementation.</p>
</div>

---

## Overview

`python-hitomi` is a batteries-included port of the popular `node-hitomi` project.
It focuses on correctness, predictable error handling, and first-class support for
asynchronous workloads. The library offers helpers to browse gallery indexes,
retrieve metadata, and construct canonical resource URIs used by Hitomi.la.

| Resource | Description |
| --- | --- |
| [Usage guide](./docs/usage.md) | Covers synchronous and asynchronous helpers for galleries, tags, and search utilities. |
| [Image URI resolution](./docs/uri-resolution.md) | Explains how URI priming works and how to fetch thumbnails, webp/avif/jxl sources, and small images. |

## Installation

The project targets **Python 3.11+** and has no runtime dependencies. Install it
directly from the repository or from a local checkout:

```bash
pip install .
```

If you prefer editable development installs:

```bash
pip install -e .
```

## Quickstart

```python
from hitomi import (
    ImageUriResolver,
    async_get_gallery,
    get_gallery,
    get_gallery_ids,
    get_gallery_uri,
    get_parsed_tags,
)

# Synchronous gallery metadata lookup
gallery = get_gallery(123456)
print(gallery.title.display)

# Filter gallery IDs via parsed tag expressions
ids = get_gallery_ids({
    "tags": get_parsed_tags("language:korean -female:netorare"),
})
print(f"Found {len(ids)} matching galleries")

# Prime image metadata once per session and resolve thumbnails
ImageUriResolver.synchronize()
thumbnail_uri = ImageUriResolver.get_image_uri(
    gallery.files[0],
    "webp",
    is_thumbnail=True,
)
print(thumbnail_uri)

# Async helpers mirror the synchronous API surface
async def preview_cover(gallery_id: int) -> str:
    gallery = await async_get_gallery(gallery_id)
    await ImageUriResolver.async_synchronize()
    return ImageUriResolver.get_image_uri(gallery.files[0], "avif")
```

## Contributing

Contributions, issues, and feature requests are welcome! Feel free to open a
discussion or report a bug on the
[issue tracker](https://github.com/H2Owater425/node-hitomi/issues).

Before submitting a pull request, please ensure:

- New modules and public helpers are accompanied by docstrings.
- Added functionality is covered by tests or verified through `python -m compileall hitomi`.
- Documentation pages under [`docs/`](./docs/) are updated when public behaviour changes.
