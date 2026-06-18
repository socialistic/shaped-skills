---
name: crinibus-price-tracker
description: |
  Track and compare product prices across 16+ e-commerce retailers (Amazon,
  eBay, Newegg, Shein, Komplett, Proshop, Elgiganten, and more). Add products
  by URL, scrape current prices, visualize price history, and identify the
  best time to buy.
---

# Crinibus Price Tracker

A CLI-driven price-tracking workflow built on
[Crinibus/scraper](https://github.com/Crinibus/scraper) — a Python tool that
scrapes, stores, and visualizes product prices from 16+ online retailers
without requiring login credentials.

## Provenance

- **Source repository**: https://github.com/Crinibus/scraper
- **Skill URL**: https://github.com/Crinibus/scraper
- **Language**: Python 3.10+
- **Storage**: SQLite (v3.0.0+)
- **License**: see repository

## Supported Retailers

Amazon (.com, .ca, .es, .fr, .de, .it), eBay, Komplett.dk, Proshop.dk,
Computersalg.dk, Elgiganten (DK/SE), AvXperten, Av-Cables, Power.dk,
Expert.dk, MM-Vision, Coolshop, Sharkgaming, Newegg (US/CA), HifiKlubben,
Shein.

## Inputs

The user provides one or more of:

| Input | Description |
|---|---|
| **Product URL(s)** | One or more product page URLs from supported retailers. |
| **Category name** | A label to group tracked products (e.g. `gpu`, `headphones`). |
| **Tracking goal** | What the user wants: add a product, check current prices, visualize trends, compare across retailers, find the cheapest option, or clean stale data. |

## Workflow

### 1. Identify the user goal

Map the request to one of these tasks:

- **Add & track** — register new product URLs under a category.
- **Scrape now** — fetch latest prices for all or selected tracked products.
- **Visualize** — generate price-over-time graphs for specific products, categories, or all tracked items.
- **Compare** — show side-by-side pricing across retailers for the same or similar products.
- **Search** — find tracked products by keyword or category.
- **Clean** — remove duplicate datapoints or delete stale products.
- **Latest price** — show the most recent scraped price for a product.

### 2. Execute via CLI

All operations go through `main.py`:

```
# Add a product
python3 main.py -a -c <category> -u <url>

# Scrape all tracked products
python3 main.py -s
python3 main.py -s --threads    # parallel scraping

# Visualize price history
python3 main.py -v --all
python3 main.py -v --id <id>
python3 main.py -v --name <name>
python3 main.py -v --category <cat>

# List tracked products
python3 main.py --list-products

# Latest datapoint
python3 main.py --name <product> --latest-datapoint

# Search
python3 main.py --search <keyword>

# Delete
python3 main.py --delete --id <id>
python3 main.py --delete --name <name>
python3 main.py --delete --category <cat>

# Clean duplicate datapoints
python3 main.py --clean
```

### 3. Interpret and present results

- For **visualize** tasks, describe the price trend: peaks, drops, stable periods, and whether the current price is near a historical low.
- For **compare** tasks, rank retailers by price and note shipping/availability differences if visible.
- For **add** tasks, confirm the product was registered and suggest running a first scrape.
- For **scrape** tasks, summarize which products had price changes since last scrape.

### 4. Configuration notes

The tool reads `scraper/settings.ini`:

- `request_delay`: seconds between HTTP requests (default 0; recommend 5+ to avoid rate limiting).
- `ChangeName`: keyword-based product name normalization rules.

## Output

Return a concise summary of the action taken and its result. Include:

- Product names and current prices when relevant.
- Price trend direction (rising / falling / stable) when visualization data is available.
- Actionable recommendation (e.g. "price is near 90-day low — good time to buy").
