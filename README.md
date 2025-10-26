<div align="center">
  <img src="https://cdn.h2owr.xyz/images/node-hitomi/banner.png" alt="banner" />
  <h3>Hitomi.la API for Python</h3>
  <sup>Would you call me a gentleman?</sup>
</div>

---

## Installation

```bash
$ pip install .
```

The project ships without runtime dependencies and targets Python 3.11+.

## Features

- Retrieve gallery IDs filtered by title, tags, or popularity
- Fetch full gallery metadata by ID
- Parse textual tag expressions and query tag listings
- Construct the various Hitomi.la resource URIs, including image URLs via `ImageUriResolver`

## Usage

```python
from hitomi import (
    ImageUriResolver,
    get_gallery,
    get_gallery_ids,
    get_parsed_tags,
    get_tags,
    get_gallery_uri,
)

# Fetch a gallery by ID
print(get_gallery(123456).title.display)

# Retrieve gallery IDs using parsed tags
ids = get_gallery_ids({
    "tags": get_parsed_tags("language:korean -female:netorare"),
})
print(len(ids))

# Synchronise image URI parts once before resolving image URLs
ImageUriResolver.synchronize()
```

## Contribution

Contributions, issues, and feature requests are welcome! Feel free to check the
[issues page](https://github.com/H2Owater425/node-hitomi/issues).
