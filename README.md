# mona-llms-txt

Generates an `llms.txt` (and an expanded `llms-full.txt`) for a website from its sitemap, following the [llmstxt.org](https://llmstxt.org) format.

The tool reads the sitemap, extracts each page's title, meta description and `h1`, groups pages by their first URL path segment and renders the result. It is part of [MONA GEO OS](https://mona.media/mona-geo-os/). A few generated labels are in Vietnamese (the `Trang chính` section for the home page, the `Nội dung:` prefix in full mode, and the fallback site description).

## Install

Requires Python 3.9+. Standard library only.

```bash
git clone https://github.com/mona-software/mona-llms-txt
cd mona-llms-txt
pip install -e .
```

## Quick start

```bash
python examples/demo.py    # offline demo using the bundled fixtures
```

```
# MONA Demo

> Website minh hoạ GEO.

## Trang chính

- [MONA Demo](https://example.test/): Website minh hoạ GEO.

## dich-vu

- [Dịch vụ SEO](https://example.test/dich-vu/seo): SEO bền vững

## blog

- [Hướng dẫn llms.txt](https://example.test/blog/llms-txt): Cách tạo tệp cho AI.
```

## Usage

```bash
mona-llms-txt https://your-site.com/sitemap.xml -o llms.txt
mona-llms-txt https://your-site.com/sitemap.xml --full -o llms-full.txt
python -m mona_llms_txt ./sitemap.xml          # local file, print to stdout
```

| Option | Description |
| --- | --- |
| `sitemap_source` | Sitemap URL, `file://` URL or local XML path |
| `--full` | Add each page's extracted text content (up to 2,000 characters) under its link |
| `--max-pages N` | Maximum number of pages to fetch (default `200`) |
| `-o`, `--output` | Output file; prints to stdout when omitted |

Behavior:

- Sitemap indexes are followed, including nested indexes.
- HTML is parsed with the standard library; no BeautifulSoup needed.
- If the sitemap contains the home page, its title and description become the file's `#` heading and `>` summary; otherwise the hostname is used.
- Output structure: `# Title`, `> description`, then one `## section` per path segment with `- [Page](URL): description` entries.

## Library use

```python
from mona_llms_txt import generate, build_llms_txt

# End to end from a sitemap
txt = generate("https://your-site.com/sitemap.xml", full=False, max_pages=200)

# Or render from your own page list (pure function, no network)
txt = build_llms_txt("Site name", "Site description", pages=[
    {"url": "https://s.com/dich-vu/seo", "title": "Dịch vụ SEO", "desc": "...", "section": "dich-vu"},
])
```

`generate()` also accepts a `loader` callable (`str -> str`) to supply cached or test content instead of fetching.

## Development

```bash
pip install -e ".[dev]"
pytest -q
```

Tests run offline against hand-written HTML and sitemap fixtures in `fixtures/`.

## License

MIT, see [LICENSE](LICENSE).

**`mona-llms-txt` is a product of MONA Software, a member of The MONA Group.**
