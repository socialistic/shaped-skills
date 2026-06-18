---
name: amazon-scraper-csv
description: >
  Search Amazon products by keyword or URL and export structured results
  (title, rating, review count, ASIN, image, product URL) to CSV. Uses the
  amazonscraper Python library with rotating user agents and anti-scraping
  retry logic. No API key or Amazon account required.
---

# Amazon Product Search to CSV

Search Amazon for products and extract structured data using the
[amazonscraper](https://pypi.org/project/amazonscraper/) Python library
(published as `amazonscraper` on PyPI, CLI as `amazon2csv.py`).

**Provenance**: <https://github.com/tducret/amazon-scraper-python>
(881+ GitHub stars, MIT license, Docker image at `thibdct/amazon2csv`).

## When to use

- User wants to search Amazon by keyword and get a CSV of matching products
- User wants to export product ratings, review counts, and ASINs for comparison
- User has an Amazon search URL with specific filters and wants the results as data
- User needs to compare products in a spreadsheet by rating and review volume
- User wants a quick product research export without an Amazon API key

## Required environment

```bash
pip install amazonscraper
```

Python 3. No Amazon account or API key needed.

## Inputs

| Input | Required | Description |
|---|---|---|
| Search keywords | Either this or a search URL | Amazon search query (e.g. `"wireless earbuds"`) |
| Amazon search URL | Either this or keywords | Full Amazon search URL with filters already applied |
| Max product count | No (default: all) | Cap the number of products returned |

## Workflow

### 1. Accept the user's search intent

Determine whether the user provided keywords or a full Amazon search URL.
If keywords, pass them to `amazonscraper.search()`. If a URL, pass it as
the `search_url` parameter.

### 2. Run the search

```python
import amazonscraper

# By keyword:
results = amazonscraper.search("wireless earbuds", max_product_nb=20)

# Or by URL:
results = amazonscraper.search(
    search_url="https://www.amazon.com/s?k=wireless+earbuds&rh=p_72%3A1248879011",
    max_product_nb=20,
)
```

Each result object exposes:

| Attribute | Description |
|---|---|
| `title` | Product title |
| `rating` | Star rating (0-5, `False` if missing) |
| `review_nb` | Number of customer reviews (`False` if missing) |
| `url` | Product page URL |
| `img` | Product image URL |
| `asin` | Amazon Standard Identification Number |

### 3. Handle pagination and retries

The library automatically:
- Paginates through search result pages
- Rotates user agents (mobile and desktop) on anti-scraping blocks
- Retries up to 5 times per page with 1-second delays
- Parses multiple Amazon page layouts (mobile list, mobile grid, two desktop variants)

### 4. Format output as CSV

Present results as CSV with columns:
`Product title, Rating, Number of customer reviews, Product URL, Image URL, ASIN`

For the user's convenience, also provide:
- Total product count
- A summary of the rating distribution (how many 4+ star products, average rating)
- Recommendation of top products by rating-to-review ratio

### 5. Alternative: CLI usage

If the user prefers command-line execution:

```bash
amazon2csv.py --keywords="wireless earbuds" --maxproductnb=20
amazon2csv.py --url="https://www.amazon.com/s?k=..." > results.csv
```

Or via Docker:

```bash
docker run -it --rm thibdct/amazon2csv --keywords="wireless earbuds"
```

## Output

Return the product data as CSV text. When presenting to the user, include:
- The CSV data (or a formatted table for readability)
- Total number of products found
- A brief comparison highlight (top-rated, most-reviewed, best value)

## Limitations

- Scraping depends on Amazon's current HTML structure; the library handles
  four known page layouts but may break on future Amazon redesigns
- Only Amazon.com domain by default (the library hardcodes `www.amazon.com`)
- Rate-limited by built-in 1-second delays between page requests
- Returns point-in-time data; prices and availability change frequently
- No access to seller-only analytics, advertising data, or Buy Box details
