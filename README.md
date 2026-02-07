# Coffer Landing Page & Documentation

Landing page and documentation site for [Coffer](https://github.com/nickvdw/coffer) — a self-hosted coin collection manager.

Built with [Astro](https://astro.build) and [Starlight](https://starlight.astro.build). Deployed to GitHub Pages.

## Development

```bash
npm install
npm run dev
```

Opens at `http://localhost:4321/coffer/`.

## Build

```bash
npm run build
npm run preview
```

## Deployment

Pushes to `main` automatically deploy to GitHub Pages via the included GitHub Actions workflow (`.github/workflows/deploy.yml`).

To enable: go to repo Settings > Pages > Source > select "GitHub Actions".
