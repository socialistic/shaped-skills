---
name: flipkart-scraper-api
description: |
  Scrape Flipkart product details and search results via a self-hosted Rust API.
  Search by keyword to get pricing, discounts, and thumbnails for up to 24 results.
  Query any Flipkart product URL to retrieve full specs, seller info, ratings,
  offers, and stock status. No API key required. All tracker links are stripped
  from the output for clean, unbiased data.
---

# Flipkart Scraper API Skill

Use this skill when the user wants to look up product details or search for
products on Flipkart (India's largest e-commerce platform). The underlying
API is a Rust-based self-hosted scraper that requires no authentication or
API key.

Provenance: this skill wraps [flipkart-scraper-api](https://github.com/dvishal485/flipkart-scraper-api)
by dvishal485 (67 stars, Apache-2.0 license). The original repo provides a
Docker-deployable REST API written in Rust; it has no SKILL.md. This skill
was shaped from the repo's public documentation and sample responses.

## Capabilities

1. **Search products** -- given a keyword query, return up to 24 matching
   Flipkart listings with name, current price, original price, discount
   flag, and thumbnail image.

2. **Get product details** -- given a Flipkart product URL, return the full
   product card: name, pricing, discount percentage, rating, stock status,
   seller name and rating, available offers, highlights, and complete
   specifications.

## Inputs

- **search query** (string): a product name, brand, or category to search
  on Flipkart (e.g. "realme 11x", "samsung galaxy s24", "sony headphones").
- **product URL** (string): a full Flipkart product page URL to scrape
  detailed information from.

The user may provide one or both. If only a search query is given, perform
the search and present results. If the user then asks about a specific
result, use its product URL for the detail lookup.

## Workflow

1. Parse the user's request to determine whether they want a search, a
   product detail lookup, or both.
2. For **search**: call the scraper's search endpoint with the query.
   Present results as a comparison table with columns: Product Name,
   Current Price (INR), Original Price, Discount, Thumbnail.
3. For **product details**: call the scraper's product endpoint with the
   Flipkart URL. Present a structured summary covering pricing, rating,
   seller, offers, highlights, and key specifications.
4. When comparing products, highlight price differences, discount depth,
   rating gaps, and specification differences relevant to the user's
   stated needs.

## Output

Return a structured summary. For searches, use a markdown table. For
product details, use labeled sections. Always show prices in INR (Indian
Rupees). Flag any out-of-stock items clearly.

## Source Links

- Repository: https://github.com/dvishal485/flipkart-scraper-api
- Docker image: `ghcr.io/dvishal485/flipkart-scraper-api`
- Sample search response: https://github.com/dvishal485/flipkart-scraper-api/blob/main/sample-search.json
- Sample product response: https://github.com/dvishal485/flipkart-scraper-api/blob/main/sample-product.json
