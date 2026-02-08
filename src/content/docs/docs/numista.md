---
title: Numista Integration
description: How Coffer uses the Numista API and how to get your API key.
---

[Numista](https://en.numista.com/) is the world's largest online coin catalog with over 800,000 coin types. Coffer integrates with the Numista API to provide catalog search, automatic image fetching, and collector market valuations.

## What the integration provides

- **Catalog search** — Search the Numista database directly from Coffer's "Add Coin" form to auto-fill coin details (title, year, country, metal, weight, dimensions)
- **Image auto-fetch** — When a coin has a Numista ID, obverse and reverse images are automatically downloaded
- **Collector valuations** — Market prices for specific issues (year + mint mark) and grades, updated daily
- **Collection import** — Import your entire Numista collection into Coffer with a single OAuth flow

## Getting your API key

### 1. Create a Numista account

If you don't already have one, sign up at [numista.com](https://en.numista.com/).

### 2. Request API access

1. Go to the [Numista API page](https://en.numista.com/api/)
2. Log in and navigate to **"API keys"**
3. Click **"Create a new API key"**
4. Fill in the application details:
   - **Name**: e.g., "Coffer"
   - **Description**: e.g., "Self-hosted coin collection manager"
   - **Type**: Select "Web application"
5. Submit the request

### 3. Get your credentials

Once approved, you'll receive:

- **API key** — Used for catalog lookups (coin types, search, images)
- **Client ID** — Used for the OAuth collection import flow

Add both to your `.env` file:

```bash
NUMISTA_API_KEY=your_api_key_here
NUMISTA_CLIENT_ID=your_client_id_here
```

## How Coffer uses the API

### Coin type lookups

When you enter a Numista ID or search the catalog, Coffer calls the Numista API to fetch:

- Coin title and description
- Country and currency
- Physical properties (weight, diameter, thickness, metal composition)
- Available issues (year + mint mark combinations)
- Obverse and reverse images

### Collector price tracking

For coins with a Numista ID, Coffer periodically fetches market prices tied to the specific issue and grade. This runs as a scheduled background job (default: daily at 2 AM).

Prices can be:
- **Exact** — A single market price
- **Range** — A minimum and maximum price estimate

### Collection import

The import feature uses OAuth to access your Numista collection:

1. You click "Import from Numista" in the Coffer UI
2. You're redirected to Numista to authorize access
3. Coffer fetches your collected items and creates coins for each one
4. Images and metadata are pulled automatically

:::tip
The import deduplicates using the `(numistaId, year, mintMark)` tuple — not just the Numista type ID. This means you can safely re-import without creating duplicates, even if you have multiple coins of the same type with different issues.
:::

## Rate limits

The Numista API has rate limits. Coffer handles this by:

- Batching price update requests (default: 50 per batch)
- Adding a configurable delay between API calls (default: 100ms)
- Caching coin type lookups to avoid redundant calls for the same type ID

## Without the API key

Coffer works without a Numista API key — you just won't have access to:

- Catalog search and auto-fill
- Automatic image fetching
- Collector market valuations
- Collection import

You can still manually add coins with all metadata and upload your own images.
