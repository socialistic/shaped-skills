---
name: amazon-review-scraper
description: >
  Scrape Amazon product reviews by ASIN across any Amazon locale using
  the amazon-product-review-scraper Python package. Returns a structured
  pandas DataFrame of all reviews for a given product. Supports pagination
  control, locale switching, and anti-CAPTCHA sleep tuning. No API key
  or Amazon account required.
---

# Amazon Product Review Scraper

Extract all customer reviews for any Amazon product by ASIN, across any
Amazon marketplace (amazon.com, amazon.co.uk, amazon.de, amazon.in,
amazon.co.jp, etc.). Uses the
[amazon-product-review-scraper](https://github.com/SinghalHarsh/amazon-product-review-scraper)
pip package (32 stars) under the hood.

**Provenance**: public tool at
<https://github.com/SinghalHarsh/amazon-product-review-scraper>.
This skill was shaped from that source; the original repository does not
contain a SKILL.md.

## When to use

- User has an Amazon ASIN and wants all customer reviews scraped
- User wants to analyze product sentiment across review pages
- User needs review data exported as a table for downstream analysis
- User wants reviews from a specific Amazon country marketplace
- User wants to scrape reviews in bulk for competitive intelligence

## Required environment

```
pip install amazon-product-review-scraper pandas
```

Python 3 required. No Amazon account, login, or API key needed.

## Inputs

| Input | Required | Description |
|---|---|---|
| ASIN | Yes | 10-character Amazon product identifier (e.g. B07X6V2FR3) |
| Amazon site | No (default: amazon.com) | Amazon domain to scrape (amazon.in, amazon.co.uk, amazon.de, amazon.co.jp, etc.) |
| Sleep time | No (default: 1) | Seconds between page requests to reduce CAPTCHA risk |
| Start page | No (default: 1) | First review page to scrape |
| End page | No | Last review page to scrape (omit to scrape all pages) |

## Workflow

### 1. Identify the product

Extract the 10-character ASIN from the user's input. The ASIN appears in
Amazon product URLs after `/dp/` (e.g.
`https://amazon.com/dp/B07X6V2FR3`). If the user provides a full URL,
parse the ASIN from it.

### 2. Determine the marketplace

Default to `amazon.com` unless the user specifies a country or locale.
Map common requests:

| User says | amazon_site value |
|---|---|
| US / United States | amazon.com |
| UK / Britain | amazon.co.uk |
| Germany | amazon.de |
| France | amazon.fr |
| Japan | amazon.co.jp |
| India | amazon.in |
| Canada | amazon.ca |
| Australia | amazon.com.au |
| Spain | amazon.es |
| Italy | amazon.it |

### 3. Scrape reviews

```python
from amazon_product_review_scraper import amazon_product_review_scraper

scraper = amazon_product_review_scraper(
    amazon_site="amazon.com",
    product_asin="B07X6V2FR3",
    sleep_time=2     # increase if hitting CAPTCHAs
)

reviews_df = scraper.scrape()
```

Optional pagination control:

```python
scraper = amazon_product_review_scraper(
    amazon_site="amazon.in",
    product_asin="B07X6V2FR3",
    start_page=1,
    end_page=5
)
reviews_df = scraper.scrape()
```

### 4. Analyze and present results

The returned DataFrame contains review text, star rating, reviewer name,
review date, and review title. Present results according to user goals:

- **Raw export**: save as CSV or display as a table
- **Sentiment summary**: count reviews by star rating, compute average,
  identify sentiment distribution
- **Theme extraction**: surface recurring praise and complaint themes
  from review text
- **Competitive comparison**: scrape reviews for multiple ASINs and
  compare satisfaction metrics side by side

### 5. Handle common issues

- **CAPTCHA blocking**: increase `sleep_time` to 3-5 seconds
- **No reviews found**: verify the ASIN is valid and the marketplace has
  listings for that product
- **Partial results**: use `start_page`/`end_page` to scrape in batches
  if the product has hundreds of review pages

## Output

Return scraped review data in the format the user requested. Default to a
summary table with star rating distribution and top positive/negative
excerpts, plus the option to export the full DataFrame as CSV.

## Limitations

- Scraping depends on Amazon's current page structure; layout changes may
  break extraction
- High-volume scraping risks temporary IP blocks; use reasonable sleep
  values
- No access to verified-purchase filtering or reviewer profile data beyond
  what appears on the review page
- Results are point-in-time; reviews and ratings change continuously
