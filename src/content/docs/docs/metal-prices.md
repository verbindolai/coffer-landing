---
title: Metal Prices
description: How Coffer tracks live precious metal prices.
---

Coffer automatically fetches live precious metal prices to calculate the melt value of coins in your collection.

## Data source

Metal prices are sourced from [Swissquote](https://www.swissquote.com/), a Swiss online bank that provides a free forex data feed. The feed includes real-time spot prices for:

| Metal | Code | Symbol |
|-------|------|--------|
| Gold | XAU | Au |
| Silver | XAG | Ag |
| Platinum | XPT | Pt |

Prices are quoted in USD per troy ounce and converted to price-per-gram for valuation calculations.

## Update schedule

By default, metal prices are fetched **every 5 minutes on weekdays** (Monday through Friday). This matches forex market hours — precious metal spot prices don't change on weekends.

The schedule is configurable via the `METAL_QUOTES_CRON` environment variable:

```bash
# Default: every 5 minutes, weekdays only
METAL_QUOTES_CRON=0 */5 * * * 1-5

# Every minute on weekdays (more granular)
METAL_QUOTES_CRON=0 * * * * 1-5

# Every 15 minutes, all days
METAL_QUOTES_CRON=0 */15 * * * *
```

## How valuation works

For each coin with a metal type and known weight/purity, Coffer calculates:

```
metal_value = weight_grams × (purity / 1000) × price_per_gram
```

For example, a 1 oz American Gold Eagle (31.1g, 916.7 purity):

```
metal_value = 31.1 × (916.7 / 1000) × current_gold_price_per_gram
```

## Historical data

Every price fetch is stored in the database, building a historical record. This powers the valuation charts in the UI, which support these timeframes:

| Timeframe | Data resolution |
|-----------|----------------|
| 1 hour | 5-minute intervals |
| 1 day | 5-minute intervals |
| 1 week | Daily aggregation |
| 1 month | Daily aggregation |
| 1 year | Daily aggregation |
| Max | Daily aggregation |

## Portfolio snapshots

In addition to individual coin valuations, Coffer takes portfolio-level snapshots on a schedule (default: 3x daily at 9 AM, 3 PM, and 10 PM). These snapshots aggregate the total value across all coins and power the portfolio overview chart.

The snapshot schedule is configurable:

```bash
# Default: 3x daily
PORTFOLIO_SNAPSHOT_CRON=0 0 9,15,22 * * *

# Hourly
PORTFOLIO_SNAPSHOT_CRON=0 0 * * * *
```

## No API key required

Metal price fetching works out of the box with no API key or account. The Swissquote forex data feed is publicly accessible.
