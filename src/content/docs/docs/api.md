---
title: API Reference
description: REST API endpoints provided by the Coffer backend.
---

The Coffer backend exposes a REST API at `/api/v1`. When running locally, interactive API documentation is available via Swagger UI at [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html).

## Coins

### List coins

```http
GET /api/v1/coins?page=0&size=20&sort=createdAt,desc
```

Query parameters for filtering:

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | integer | Page number (0-indexed) |
| `size` | integer | Page size (default: 20) |
| `sort` | string | Sort field and direction |
| `search` | string | Search by title |
| `country` | string | Filter by ISO 3166-1 country code |
| `metalType` | string | `GOLD`, `SILVER`, `PLATINUM`, `NICKEL` |
| `grade` | string | `GOOD` through `PROOF` |
| `type` | string | `BULLION`, `COMMEMORATIVE_CIRCULATION`, etc. |
| `yearFrom` | integer | Minimum year |
| `yearTo` | integer | Maximum year |

### Create coin

```http
POST /api/v1/coins
Content-Type: application/json
```

```json
{
  "title": "American Gold Eagle 1 oz",
  "year": 2024,
  "countryCode": "US",
  "currency": "USD",
  "denomination": 50,
  "metalType": "GOLD",
  "weight": 31.1,
  "purity": 917,
  "grade": "UNCIRCULATED",
  "type": "BULLION",
  "numistaId": "12345",
  "quantity": 1
}
```

### Update coin

```http
PUT /api/v1/coins/{id}
Content-Type: application/json
```

Same body format as create.

### Delete coin

```http
DELETE /api/v1/coins/{id}
```

## Valuations

### Coin valuation history

```http
GET /api/v1/coins/{id}/valuation?timeframe=1d
```

Returns historical metal and collector valuations for a specific coin.

| Timeframe | Resolution |
|-----------|------------|
| `1h` | 5-minute intervals |
| `1d` | 5-minute intervals |
| `1w` | Daily |
| `1m` | Daily |
| `1y` | Daily |
| `max` | Daily |

### Portfolio valuation

```http
GET /api/v1/portfolio/valuation?timeframe=1d
```

Returns aggregate portfolio value over time.

## Catalog

### Search Numista catalog

```http
GET /api/v1/catalog/search?query=gold+eagle
```

Searches the Numista catalog. Returns matching coin types with thumbnails.

### Get coin type details

```http
GET /api/v1/catalog/types/{typeId}
```

Returns full details for a Numista coin type, including issues, images, and physical properties.

## Validation rules

| Field | Constraint |
|-------|-----------|
| `countryCode` | Exactly 2 characters (ISO 3166-1) |
| `currency` | Exactly 3 characters (ISO 4217) |
| `year` | 1–2100 |
| `purity` | 1–1000 (representing 0.1%–100%) |
| `weight` | Greater than 0 |
| `quantity` | Minimum 1 (default: 1) |
| `numistaId` | Max 50 characters |

## Enums

### MetalType

`GOLD`, `SILVER`, `PLATINUM`, `NICKEL`

### Grade

`GOOD`, `VERY_GOOD`, `FINE`, `VERY_FINE`, `EXTREMELY_FINE`, `ABOUT_UNCIRCULATED`, `UNCIRCULATED`, `PROOF`

### CoinType

`BULLION`, `COMMEMORATIVE_CIRCULATION`, `STANDARD_CIRCULATION`, `COMMEMORATIVE_NON_CIRCULATION`, `OTHER`
