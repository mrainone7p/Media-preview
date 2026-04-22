# *arr Calendar

A self-hosted web calendar that aggregates iCal feeds from Sonarr, Radarr, Lidarr, and any other \*arr app. Click any item for a rich content card with poster, trailer, and description via TMDB.

![Preview](https://img.shields.io/badge/docker-ready-blue?logo=docker)
![License](https://img.shields.io/badge/license-MIT-green)

---

## Features

- Month calendar with colour-coded feeds per service
- Click any event → TMDB content card (poster · trailer · rating · overview)
- Configurable auto-sync (15 min – 24 hr, default 24 hr)
- Custom server name + logo
- Built-in CORS proxy — no changes needed to your \*arr config
- Fully static — served by nginx, zero backend

---

## Quick start (Docker Compose)

### 1 · Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/arr-calendar.git
cd arr-calendar
```

### 2 · Start the container

```bash
docker compose up -d
```

Open **http://localhost:8096** (or replace `localhost` with your server IP).

### 3 · Add your feeds

Go to **Settings → iCal Feeds** and paste your \*arr URLs, e.g.:

| App     | URL pattern |
|---------|-------------|
| Sonarr  | `http://192.168.1.x:8989/sonarr/feed/v3/calendar/Sonarr.ics?apikey=YOUR_KEY` |
| Radarr  | `http://192.168.1.x:7878/radarr/feed/v3/calendar/Radarr.ics?apikey=YOUR_KEY` |
| Lidarr  | `http://192.168.1.x:8686/lidarr/feed/v3/calendar/Lidarr.ics?apikey=YOUR_KEY` |

Find your API key in each app under **Settings → General**.

### 4 · Enable TMDB (optional but recommended)

1. Create a free account at [themoviedb.org](https://www.themoviedb.org)
2. Go to **Settings → API** and copy your API key (v3 auth)
3. Paste it into **Settings → Integrations → TMDB Key**

---

## Changing the port

Edit `docker-compose.yml`:

```yaml
ports:
  - "8096:80"   # change 8096 to any free port
```

Then restart:

```bash
docker compose up -d
```

---

## Updating

### If building locally

```bash
git pull
docker compose up -d --build
```

### If using the GHCR image

```yaml
# docker-compose.yml
image: ghcr.io/YOUR_USERNAME/arr-calendar:latest
```

```bash
docker compose pull
docker compose up -d
```

---

## GitHub Actions — auto-build on push

The included workflow (`.github/workflows/docker.yml`) automatically builds and pushes a Docker image to GitHub Container Registry whenever you push to `main`.

To enable it:

1. Push this repo to GitHub
2. The action runs automatically — no secrets needed (uses `GITHUB_TOKEN`)
3. The image is published at `ghcr.io/YOUR_USERNAME/arr-calendar:latest`
4. Switch `docker-compose.yml` to use the image instead of `build: .`

---

## How the CORS proxy works

Browsers block cross-origin requests (different port = different origin). The nginx config includes a `/proxy?url=` endpoint that forwards iCal requests server-side, sidestepping CORS entirely.

Your \*arr API keys are sent **only** from your server to your \*arr apps — they never leave your network.

---

## Project structure

```
arr-calendar/
├── index.html              # The full app (single file, no build step)
├── nginx/
│   └── default.conf        # nginx config with CORS proxy
├── Dockerfile
├── docker-compose.yml
└── .github/
    └── workflows/
        └── docker.yml      # Auto-build + push to GHCR
```

---

## License

MIT
