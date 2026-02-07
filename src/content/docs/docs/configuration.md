---
title: Configuration
description: All environment variables and configuration options for Coffer.
---

Coffer is configured through environment variables in the `.env` file within the `coffer-deploy` directory.

## Environment variables

### Database

| Variable | Default | Description |
|----------|---------|-------------|
| `DB_NAME` | `coffer` | PostgreSQL database name |
| `DB_USER` | `coffer` | PostgreSQL username |
| `DB_PASSWORD` | **(required)** | PostgreSQL password — no default for security |

### External APIs

| Variable | Default | Description |
|----------|---------|-------------|
| `NUMISTA_API_KEY` | *(empty)* | Numista API key for catalog lookups. Optional but recommended. See [Numista integration](/coffer/docs/numista/). |

### Application

| Variable | Default | Description |
|----------|---------|-------------|
| `FRONTEND_PORT` | `80` | Port to expose the frontend on the host |
| `JAVA_OPTS` | `-Xmx512m` | JVM options for the backend. Increase memory for larger collections. |

### Schedulers

These control the background jobs that fetch live data. The defaults work well for most users.

| Variable | Default | Description |
|----------|---------|-------------|
| `METAL_QUOTES_CRON` | `0 */5 * * * 1-5` | Metal price update frequency (every 5 min, weekdays) |
| `ISSUE_PRICES_CRON` | `0 0 2 * * *` | Numista collector price refresh (daily at 2 AM) |
| `PORTFOLIO_SNAPSHOT_CRON` | `0 0 9,15,22 * * *` | Portfolio value snapshots (3x daily) |

:::tip
Cron expressions use the Spring 6-field format: `second minute hour day month weekday`. The metal quotes scheduler only runs on weekdays (Mon–Fri) because forex markets are closed on weekends.
:::

## Example `.env` file

```bash
# Database
DB_NAME=coffer
DB_USER=coffer
DB_PASSWORD=changeme_use_a_strong_password

# Numista API key (optional, enables catalog features)
NUMISTA_API_KEY=your_numista_api_key_here

# Frontend port
FRONTEND_PORT=80

# JVM memory (increase for large collections)
JAVA_OPTS=-Xmx512m
```

## Image storage

Coin images are stored in the `coffer-images` Docker volume, mounted at `/app/data/images` inside the backend container. The default limits are:

- **Max file size**: 10 MB
- **Allowed types**: JPEG, PNG, WebP

These are configured in the backend's `application.yml` and don't typically need to be changed.
