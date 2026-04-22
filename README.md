# \*arr Calendar

A self-hosted media calendar that aggregates iCal feeds from Sonarr, Radarr, Lidarr, and any other \*arr app into a single month view. Click any event for a rich content card with poster, trailer, rating, and overview via TMDB.

![Docker](https://img.shields.io/badge/docker-ready-blue?logo=docker)
![nginx](https://img.shields.io/badge/served_by-nginx-green?logo=nginx)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Features

- Month calendar with colour-coded events per feed
- Click any event → TMDB content card (poster · trailer · rating · overview)
- Built-in CORS proxy — paste your `.ics` URL as-is, no \*arr config changes needed
- Auto-sync on a configurable interval (15 min – 24 hr)
- Custom server name and logo
- Fully static — single `index.html` served by nginx, zero backend or build step
- Demo mode with sample events when no feeds are configured

---

## Quick start

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose (v2 — `docker compose`)

### 1. Clone the repo

```bash
git clone https://github.com/mrainone7p/media-preview.git
cd media-preview
```

### 2. Start the container

```bash
docker compose up -d
```

Open **http://localhost:8096** in your browser (or replace `localhost` with your server's IP).

### 3. Add your \*arr feeds

Go to **Settings → iCal Feeds** and paste your feed URLs:

| App    | Default port | URL pattern                                                                    |
|--------|-------------|---------------------------------------------------------------------------------|
| Sonarr | 8989        | `http://192.168.1.x:8989/sonarr/feed/v3/calendar/Sonarr.ics?apikey=YOUR_KEY`  |
| Radarr | 7878        | `http://192.168.1.x:7878/radarr/feed/v3/calendar/Radarr.ics?apikey=YOUR_KEY`  |
| Lidarr | 8686        | `http://192.168.1.x:8686/lidarr/feed/v3/calendar/Lidarr.ics?apikey=YOUR_KEY`  |

Find your API key in each app under **Settings → General → Security → API Key**.

### 4. Enable TMDB (optional but recommended)

1. Create a free account at [themoviedb.org](https://www.themoviedb.org)
2. Go to **Settings → API** and copy your v3 API key
3. Paste it into **Settings → Integrations → TMDB Key**

Without a TMDB key the calendar still works — you just won't get posters, trailers, or ratings.

---

## Configuration

### Change the port

Edit `docker-compose.yml`:

```yaml
ports:
  - "8096:80"   # change 8096 to any free port on your host
```

Then apply the change:

```bash
docker compose up -d
```

### All settings are persisted in the browser

Server name, logo, feed URLs, TMDB key, and sync interval are all saved to `localStorage` — nothing is written to disk on the server. Clearing browser data resets them.

---

## Updating

```bash
git pull
docker compose up -d --build
```

---

## How the CORS proxy works

Browsers block requests to a different origin (different host or port counts as a different origin). The nginx config exposes a `/proxy?url=` endpoint that fetches iCal feeds server-side and relays them back, bypassing the browser restriction entirely.

```
Browser → GET /proxy?url=http%3A%2F%2F192.168.1.x%3A8989%2F...
              ↓
         nginx (same origin as the page)
              ↓
         Sonarr on your LAN
```

Your \*arr API keys travel only between your server and your \*arr apps — they never leave your network.

---

## Project structure

```
media-preview/
├── index.html          # Full React app — no build step
├── nginx/
│   └── default.conf    # Static file serving + /proxy CORS endpoint + /health
├── Dockerfile          # nginx:alpine image
└── docker-compose.yml  # One-command deployment
```

---

## License

MIT
