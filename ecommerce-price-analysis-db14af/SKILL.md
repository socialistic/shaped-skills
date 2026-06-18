---
name: ecommerce-price-analysis
description: |
  Multi-platform e-commerce price monitoring and analysis skill.
  Collects product prices from Chinese platforms (JD, Taobao, Xiaohongshu,
  Douyin) and international platforms (Amazon, eBay, Walmart), performs
  trend/volatility/comparison analysis, and generates visual reports with
  purchasing recommendations.
category: data-analysis
tags:
  - ecommerce
  - price-monitoring
  - price-comparison
  - data-analysis
  - visualization
  - web-scraping
---

# E-commerce Price Monitoring & Analysis / 电商价格监控分析

> Provenance: shaped from [wangdw495/ecommerce-price-analysis](https://github.com/wangdw495/ecommerce-price-analysis) (MIT, 32 stars).
> The original repository is a Python CLI tool with no SKILL.md; this skill
> wraps its workflow for conversational use.

## Job

Help the user monitor, compare, and analyze product prices across multiple
e-commerce platforms. The user provides a product query (name, category, or
URL list) and optionally specifies target platforms and analysis goals. The
skill returns structured price data, trend analysis, cross-platform
comparisons, and actionable purchasing recommendations.

## Supported Platforms

| Region | Platforms |
|---|---|
| International | Amazon, eBay, Walmart |
| China | 京东 (JD.com), 淘宝 (Taobao), 小红书 (Xiaohongshu), 抖音电商 (Douyin) |

## Inputs

The user provides one or more of:

- **Product query**: a product name, keyword, or category in any language
  (e.g. "iPhone 15 Pro Max", "华为Mate60", "gaming laptop under $1000")
- **Platform selection** (optional): which platforms to search. Default: all
  available platforms for the query language
- **Analysis goal** (optional): what the user wants to learn — price trend,
  cheapest option, volatility, cross-platform comparison, or general market
  overview
- **Price data file** (optional): a CSV/Excel of previously collected price
  data to analyze directly without fresh collection

## Workflow

### Step 1 — Clarify scope

If the user's query is ambiguous, ask:
- Which specific product or product category?
- Which platforms to include? (suggest based on language)
- What time range for trend analysis?
- Is this a one-time comparison or ongoing monitoring setup?

### Step 2 — Collect price data

Use `ecommerce-monitor search` to collect data:

```bash
# International platforms
ecommerce-monitor search -q "<query>" -p amazon -p ebay -p walmart -n 20 -o results.csv

# Chinese platforms
ecommerce-monitor search -q "<query>" -p jd -p taobao -p xiaohongshu -n 20 -o results.csv
```

Key collection parameters:
- `--max-results` / `-n`: results per platform (default 20)
- `--platforms` / `-p`: target platforms
- `--format` / `-f`: output format (csv, json, excel)

### Step 3 — Analyze collected data

Run analysis on the collected data:

```bash
ecommerce-monitor analyze results.csv -o reports/ -f html markdown -c
```

Analysis capabilities from `PriceAnalyzer`:
- **Price trend analysis**: identify rising, falling, and stable price
  patterns over configurable windows
- **Volatility calculation**: assess price stability using rolling-window
  standard deviation
- **Cross-platform comparison**: rank platforms by price, availability,
  and seller rating for the same product
- **Statistical analysis**: descriptive statistics, distribution shape,
  outlier detection (IQR-based)
- **Correlation analysis**: price vs. rating, price vs. sales volume

### Step 4 — Visualize results

Generate charts using the visualization module:

```bash
ecommerce-monitor visualize results.csv -t price_distribution -o chart.html
```

Available chart types:
- `price_distribution` — histogram of price spread
- `platform_comparison` — bar chart comparing platforms
- `price_trends` — time-series line chart
- `scatter_analysis` — price vs. rating scatter
- `correlation_heatmap` — variable correlation matrix
- `box_plots` — distribution and outliers per platform

### Step 5 — Generate report and recommendations

Synthesize findings into a structured report:

1. **Market overview**: price range, median, dominant platform
2. **Platform ranking**: best price, best ratings, best availability
3. **Trend insight**: is the price rising, falling, or stable?
4. **Purchase recommendation**: when and where to buy based on analysis
5. **Risk flags**: high volatility, suspicious pricing, limited stock

Export in the user's preferred format:
- CSV / Excel for data teams
- Markdown / HTML for sharing
- JSON for API integration

## Output Contract

Return a structured response containing:

```
- summary: 1-2 sentence overview of findings
- platforms_searched: list of platforms queried
- total_products_found: count
- price_range: {min, max, median, currency}
- best_deal: {product, platform, price, url}
- analysis: {trend, volatility_score, recommendation}
- charts: list of generated visualization files (if any)
- raw_data_path: path to exported data file
```

## Configuration Reference

Key settings from `config/config.yaml`:
- `scraping.request_delay`: 1.0s between requests (respect rate limits)
- `analysis.price_change_threshold`: 5% change triggers alert
- `analysis.trend_window`: 30-day default for trend calculation
- `rate_limiting.requests_per_minute`: 30 per platform
- `performance.max_concurrent_requests`: 5 parallel requests

## Source Repository

- **GitHub**: https://github.com/wangdw495/ecommerce-price-analysis
- **License**: MIT
- **Language**: Python 3.8+
- **Install**: `pip install -e .` from cloned repo
- **CLI entry point**: `ecommerce-monitor`
- **Python API**: `from ecommerce_price_monitor import PriceCollector, PriceAnalyzer, PriceVisualizer, DataExporter`
