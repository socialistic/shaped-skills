---
name: ebay-avg-price
description: |
  Calculate the average sold price or list active products on eBay across
  20 country marketplaces. Filter by condition (new, used, refurbished,
  opened) and listing type (auction, buy-it-now, offers). Uses statistical
  outlier removal (1-StDev) for reliable price estimates.
---

# eBay Average Price Calculator

Compute average sold prices and browse active listings on eBay using the
[Ebay-Scraper](https://github.com/alexis-brosseau/Ebay-Scraper) Python
library. Supports 20 eBay country domains with condition and listing-type
filters.

## Provenance

- **Source repository**: https://github.com/alexis-brosseau/Ebay-Scraper
- **Skill URL**: https://github.com/alexis-brosseau/Ebay-Scraper
- **Language**: Python 3 + BeautifulSoup4
- **License**: see repository

## Supported Marketplaces

AU, AT, BE, CA, CH, DE, ES, FR, HK, IE, IT, MY, NL, NZ, PH, PL, SG, UK, US.

## Inputs

| Input | Description |
|---|---|
| **Product query** | Search term describing the item (e.g. "Nintendo Switch", "RTX 3060"). |
| **Country code** | Two-letter eBay marketplace code (default: `us`). |
| **Condition** | `all`, `new`, `opened`, `refurbished`, or `used` (default: `all`). |
| **Listing type** | `all`, `auction`, `bin` (Buy It Now), or `offers` (default: `all`). Only applies to active listings. |
| **Goal** | What the user wants: average sold price, browse active listings, or compare across conditions/countries. |

## Workflow

### 1. Identify the user goal

Map the request to one of:

- **Average sold price** — compute the mean sold price for a product query,
  with statistical outlier filtering. Returns average item price, average
  shipping, and average total.
- **Browse active listings** — retrieve current listings sorted by
  price + shipping (cheapest first). Each item includes title, price,
  shipping, time left, bid count, review count, and direct eBay URL.
- **Cross-country comparison** — run the average or listing query across
  multiple country codes and rank by total price.
- **Condition comparison** — compare average prices across new, used,
  refurbished for the same product and marketplace.

### 2. Execute via Python API

All operations use `EbayScraper.py`:

```python
import EbayScraper

# Average sold price
avg = EbayScraper.Average(query='Nintendo Switch', country='ca', condition='new')
# Returns: {'price': 326.2, 'shipping': 39.67, 'total': 365.87}

# Active listings (sorted cheapest first)
items = EbayScraper.Items(query='RTX 3060', country='us', condition='new', type='auction')
# Returns list of dicts: title, price, shipping, time-left, time-end,
#   bid-count, reviews-count, url
```

Parameters:
- `query` (str, required): product search term
- `country` (str): marketplace code from supported list
- `condition` (str): `all` | `new` | `opened` | `refurbished` | `used`
- `type` (str, Items only): `all` | `auction` | `bin` | `offers`

### 3. Interpret and present results

- For **average price** queries: state the average item price, shipping cost,
  and total. Note the country and condition. Compare to the user's budget or
  expectations if provided.
- For **listing** queries: present the top results in a table (title, price,
  shipping, total, time left, bids, URL). Highlight the cheapest option and
  any ending-soon auctions.
- For **comparison** queries: build a comparison table across
  countries/conditions. Recommend the best value option.
- Flag that outlier prices (beyond 1 standard deviation) are automatically
  excluded, so averages reflect typical market prices.

### 4. Important notes

- The scraper queries eBay directly via HTML parsing; no API key is required.
- The maintainer notes CSS selectors may need updating per-country. If a query
  returns empty results, suggest trying a different country code or checking
  whether eBay changed their page structure.
- Respect eBay's terms of service; avoid high-frequency automated queries.

## Output

Return a structured summary including:

- The computed average price or listing table.
- Country, condition, and listing-type context.
- A practical recommendation (e.g. "average sold price for used RTX 3060 in
  the US is $215 — current cheapest BIN is $199, which is below average").
