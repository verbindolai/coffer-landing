---
title: Introduction
description: What Coffer is and why it exists.
---

**Coffer** is a self-hosted coin collection management application. It tracks your physical coin holdings, calculates real-time precious metal valuations, and integrates with the Numista catalog for collector market prices.

## Why Coffer?

Most coin collection tools are either paid SaaS platforms, outdated desktop apps, or simple spreadsheets. Coffer fills the gap as a modern, self-hosted web application that gives you:

- **Real-time metal valuations** — Gold, silver, and platinum prices updated every 5 minutes from live forex data
- **Collector value tracking** — Market prices pulled from Numista for graded coins
- **Portfolio-level analytics** — Aggregate value, historical charts, and metal composition breakdowns
- **Full data ownership** — Everything runs on your hardware via Docker

## What it tracks

Each coin in your collection stores:

| Field | Description |
|-------|-------------|
| Title | Coin name or description |
| Year | Minting year |
| Country | ISO 3166-1 country code |
| Currency | ISO 4217 currency code |
| Denomination | Face value |
| Metal type | Gold, silver, platinum, or nickel |
| Weight & purity | For metal value calculation |
| Grade | GOOD through PROOF (8 grades) |
| Type | Bullion, commemorative, standard circulation, etc. |
| Numista ID | Links to the Numista catalog for images and prices |
| Images | Obverse and reverse photos (auto-fetched or manual) |

## How valuations work

Coffer calculates two types of value for each coin:

1. **Metal valuation** — `weight × (purity / 1000) × current price per gram`. Updated from live forex data (Swissquote) every 5 minutes on weekdays.

2. **Collector valuation** — Market price from Numista, tied to the specific issue (year + mint mark) and grade. Can be an exact price or a min/max range.

Both are tracked historically, so you can see how your collection's value has changed over any timeframe.

## Project structure

Coffer has a Kotlin/Spring Boot backend, an Angular frontend, and a Docker Compose deployment configuration. Pre-built container images are published to GitHub Container Registry — to self-host Coffer, see the [coffer-deploy](https://github.com/verbindolai/coffer-deploy) repository.
