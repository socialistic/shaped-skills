---
name: amazon-buddy-scraper
description: >
  Scrape Amazon product search results, reviews, and single-product details
  across all Amazon marketplaces using the amazon-buddy CLI and Node.js library.
  No API key or login required. Supports filtering by rating, category, country,
  and discount. Outputs CSV or JSON.
---

# Amazon Buddy Scraper

Use [amazon-buddy](https://github.com/drawrowfly/amazon-product-api) (760+
GitHub stars, published on npm) to extract Amazon product data, reviews, and
ASIN details on demand across every Amazon marketplace.

**Provenance**: public tool at <https://github.com/drawrowfly/amazon-product-api>,
npm package `amazon-buddy`. This skill was shaped from that source; the original
repository does not contain a SKILL.md.

## When to use

- User wants to search Amazon for products by keyword and get structured results
- User wants to scrape reviews for a specific product by ASIN
- User wants detailed product data (price, rating, variants, images) by ASIN
- User wants to compare products across Amazon country marketplaces
- User needs to filter by discount, rating range, category, or sponsored status
- User wants results saved to CSV or JSON for further analysis

## Required environment

```
npm i -g amazon-buddy
```

Node.js required. No Amazon account or API key needed.

## Inputs

| Input | Required | Description |
|---|---|---|
| Search keyword | Either this or ASIN | Amazon product search query |
| ASIN | Either this or keyword | Amazon product identifier for reviews or details |
| Country code | No (default: US) | ISO 3166 Alpha-2 code (US, GB, DE, NL, JP, etc.) |
| Category | No (default: aps/All) | Amazon category slug from `amazon-buddy categories` |
| Number of results | No (default: 20) | Max products (up to 500) or reviews (up to 1000) |
| Rating range | No | Min and max star rating to filter results |
| Output format | No (default: csv) | `csv`, `json`, `all`, or empty for terminal display |

## Workflow

### 1. Determine the task type

Identify which operation the user needs:

- **Product search**: keyword-based search across Amazon listings
- **Review scrape**: collect reviews for a specific ASIN
- **ASIN lookup**: get full details for a single product
- **Category/country listing**: show available categories or marketplaces

### 2. Execute the scrape

#### Product search by keyword

```bash
amazon-buddy products -k 'wireless earbuds' -n 50 --country US --filetype json
```

Key flags:
- `-k` / `--keyword`: search term
- `-n` / `--number`: result count (max ~500)
- `--country`: marketplace (US, GB, DE, FR, ES, IT, NL, AU, CA, JP, IN, etc.)
- `--category`: category slug from `amazon-buddy categories`
- `--discount` / `-d`: only discounted products
- `--sponsored`: only sponsored listings
- `--min-rating` / `--max-rating`: filter by star rating (1-5)
- `--sort`: sort by score (reviews * rating)
- `--random-ua`: randomize user agent to reduce blocking

#### Review scrape by ASIN

```bash
amazon-buddy reviews B01GW3H3U8 -n 100 --filetype json
```

Supports `--min-rating` and `--max-rating` for filtering reviews by stars.

#### Single product details by ASIN

```bash
amazon-buddy asin B01GW3H3U8
```

Returns: title, description, feature bullets, price (current/before/savings),
rating, total reviews, variants with images, availability, and dimensions.

#### List available categories and countries

```bash
amazon-buddy categories --country US
amazon-buddy countries
```

### 3. Use as Node.js module (alternative)

```javascript
const amazonScraper = require('amazon-buddy');

// Search products
const products = await amazonScraper.products({
  keyword: 'Xbox One', number: 50, country: 'US'
});

// Scrape reviews
const reviews = await amazonScraper.reviews({
  asin: 'B01GW3H3U8', number: 50, rating: [3, 5]
});

// Single product detail
const product = await amazonScraper.asin({ asin: 'B01GW3H3U8' });

// List categories for a marketplace
const cats = await amazonScraper.categories({ country: 'AU' });
```

### 4. Present results

Format the scraped data for the user's goal:

- **Product search**: comparison table with title, ASIN, price, discount %,
  rating, review count, Prime eligibility, Best Seller / Amazon Choice badges
- **Reviews**: summary of sentiment distribution, highlight top positive and
  negative reviews, extract recurring themes
- **ASIN detail**: structured product card with price history context, feature
  bullets, variant options, and availability
- **Cross-marketplace**: side-by-side price comparison across countries for the
  same ASIN

## Output

Return structured data matching the user's request. For searches, present a
ranked comparison. For reviews, summarize sentiment and surface actionable
insights. For ASIN lookups, present a complete product profile.

## Limitations

- Scraping depends on Amazon's current page structure; may break on layout
  changes
- No access to seller-only data, advertising metrics, or historical pricing
- Use `--random-ua` and reasonable `-n` values to avoid rate limiting
- Results are point-in-time snapshots; prices and stock change frequently
- Maximum ~500 products per search or ~1000 reviews per ASIN per run
