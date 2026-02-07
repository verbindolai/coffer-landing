---
title: Architecture
description: Technical architecture overview of Coffer.
---

Coffer follows a standard three-tier architecture with a clear separation between the frontend, backend, and database.

## Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Frontend | Angular (standalone components, signals) | 18 |
| Styling | Tailwind CSS | 3.4 |
| Charts | Lightweight Charts (TradingView) | 5.1 |
| Backend | Spring Boot (Kotlin) | 4.0 |
| Database | PostgreSQL | 16 |
| Migrations | Liquibase | — |
| Runtime | Java (Eclipse Temurin) | 21 |
| Build | Gradle (Kotlin DSL) | 8+ |
| API Docs | SpringDoc OpenAPI (Swagger UI) | 2.8 |
| Deployment | Docker Compose | — |

## Backend

The backend is a Spring Boot application written in Kotlin. It exposes a REST API consumed by the Angular frontend.

### Key components

- **REST controllers** — CRUD operations for coins, catalog search, portfolio analytics, and valuation history
- **Scheduled jobs** — Background tasks for fetching metal prices, collector values, and portfolio snapshots
- **Numista client** — Feign-based HTTP client for the Numista API (catalog search, type details, image fetch, OAuth)
- **Swissquote client** — Fetches live precious metal spot prices
- **Image storage** — File-based storage for coin images (obverse/reverse), stored in a Docker volume
- **Liquibase migrations** — Database schema managed via changelogs, applied automatically on startup

### Event-driven flow

When a coin is created, a `CoinCreatedEvent` triggers asynchronous processing:

1. **Issue fetch** — If the coin has a Numista ID, fetch available issues and collector prices
2. **Image fetch** — Download obverse/reverse images from Numista
3. **Portfolio snapshot** — Recalculate the aggregate portfolio value

This keeps the coin creation endpoint fast while background tasks handle the heavier integration work.

## Frontend

The frontend is an Angular 18 single-page application using standalone components and signals for reactivity. It's styled entirely with Tailwind CSS — no component library.

### Key features

- **Lazy-loaded routes** — Code splitting for collection, portfolio, coin detail, and import views
- **Signal-based state** — Reactive computed values using Angular signals
- **Financial charts** — TradingView's Lightweight Charts library for valuation history
- **Dark theme** — Custom Tailwind theme with the Coffer color palette (dark backgrounds, teal accent, gold highlights)
- **Responsive** — Mobile-first layout with breakpoints at 640px and 1024px

### Route structure

| Route | View |
|-------|------|
| `/` | Portfolio overview — stats, value chart, metal breakdown |
| `/coins` | Paginated collection list with filters and search |
| `/coins/:id` | Coin detail — images, metadata, valuation charts |
| `/coins/new` | Add coin form with Numista catalog search |
| `/coins/:id/edit` | Edit coin form |
| `/import/numista` | Numista OAuth import flow |

## Database

PostgreSQL with Liquibase-managed schema. Key tables:

- **coin** — Core coin metadata (title, year, country, metal, weight, purity, grade, etc.)
- **coin_image** — Obverse/reverse image file references
- **metal_quote** — Historical precious metal prices (timestamped)
- **issue_price** — Collector market values from Numista (tied to issue + grade)
- **portfolio_snapshot** — Aggregated portfolio value at a point in time

## Docker deployment

The production deployment consists of three containers orchestrated by Docker Compose:

| Container | Image | Port |
|-----------|-------|------|
| `coffer-db` | `postgres:16-alpine` | 5432 (internal) |
| `coffer-backend` | Multi-stage build (Gradle + JDK 21 → JRE 21 Alpine) | 8080 (internal) |
| `coffer-frontend` | Multi-stage build (Node 20 → Nginx Alpine) | 80 (exposed) |

All containers communicate over an internal Docker bridge network. Only the frontend port is exposed to the host. Health checks ensure proper startup ordering:

1. Database must be healthy (`pg_isready`)
2. Backend starts after database, must pass health check (Swagger UI accessible)
3. Frontend starts after backend is healthy

Data is persisted in two Docker volumes:
- `coffer-db-data` — PostgreSQL data directory
- `coffer-images` — Uploaded and fetched coin images
