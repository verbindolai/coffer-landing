---
title: Installation
description: How to deploy Coffer with Docker.
---

Coffer is deployed as three Docker containers: a PostgreSQL database, a Spring Boot backend, and an Nginx-served Angular frontend.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) installed
- Git
- At least 1 GB of available RAM (512 MB allocated to the JVM by default)

## Quick start

### 1. Clone the repositories

You need all three repositories in the same parent directory:

```bash
mkdir coffer && cd coffer
git clone https://github.com/nickvdw/coffer2.git
git clone https://github.com/nickvdw/coffer2-ui.git
git clone https://github.com/nickvdw/coffer-deploy.git
```

### 2. Configure environment

```bash
cd coffer-deploy
cp .env.example .env
```

Edit `.env` and set at minimum:

```bash
# Required — pick a strong password
DB_PASSWORD=your_secure_database_password

# Recommended — enables Numista catalog integration
NUMISTA_API_KEY=your_numista_api_key
```

See [Configuration](/coffer-landing/docs/configuration/) for all available options.

### 3. Build and start

```bash
docker compose up -d --build
```

This builds both the backend and frontend from source, starts PostgreSQL, runs Liquibase migrations automatically, and starts the application.

### 4. Access Coffer

Open [http://localhost](http://localhost) (or whatever port you configured via `FRONTEND_PORT`).

The API documentation is available at [http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html) through the backend container.

## Updating

Pull the latest changes and rebuild:

```bash
cd coffer-deploy
git -C ../coffer2 pull
git -C ../coffer2-ui pull
docker compose up -d --build
```

Database migrations are applied automatically on startup via Liquibase.

## Stopping

```bash
docker compose down
```

Your data is preserved in Docker volumes (`coffer-db-data` and `coffer-images`). To remove all data:

```bash
docker compose down -v
```

:::caution
`docker compose down -v` permanently deletes your database and uploaded images.
:::

## Architecture overview

```
┌──────────────┐     ┌──────────────────┐     ┌─────────────┐
│   Frontend   │────▶│     Backend      │────▶│  PostgreSQL  │
│  (Nginx:80)  │     │  (Spring:8080)   │     │   (:5432)    │
└──────────────┘     └──────────────────┘     └─────────────┘
                             │
                      ┌──────┴──────┐
                      │  External   │
                      │    APIs     │
                      ├─────────────┤
                      │  Numista    │
                      │  Swissquote │
                      └─────────────┘
```

The frontend Nginx server handles static file serving and proxies `/api` requests to the backend. All three services communicate over an internal Docker network (`coffer-network`).
