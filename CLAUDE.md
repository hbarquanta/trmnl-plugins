# TRMNL Plugins — Project Context

Fabian's TRMNL e-ink display (800×480px, black & white). Building a suite of beautiful, data-rich plugins.

## Plugin Roadmap

| # | Plugin | Strategy | Status |
|---|--------|----------|--------|
| 1 | Planes over Vienna | None (client-side JS) | 🚧 In progress |
| 2 | Vienna Transit (Wiener Linien) | Polling or Webhook | Planned |
| 3 | OSM Spot of the Day | Polling | Planned |
| 4 | Danube / ship tracker | TBD | Planned |

---

## APIs & Data Sources

### ✈️ OpenSky Network (Planes)
- **Endpoint**: `https://opensky-network.org/api/states/all?lamin=48.05&lomin=16.15&lamax=48.35&lomax=16.55`
- **Auth**: None required for anonymous (10 req/10s rate limit). Register free account for higher limits.
- **TRMNL strategy**: **None** (client-side JS inside plugin HTML, fetch every 20–30s)
- **Response field order**: `[icao24, callsign, origin_country, time_position, last_contact, longitude, latitude, baro_altitude, on_ground, velocity, true_track, vertical_rate, sensors, geo_altitude, squawk, spi, position_source]`
- Vienna bounding box: lat 48.05–48.35, lon 16.15–16.55

### 🚌 Wiener Linien Realtime
- **Confirmed**: Free, no API key needed
- **Echtzeit monitor endpoint**: `https://www.wienerlinien.at/ogd_realtime/monitor?stopId=XXXX&activateTrafficInfo=stoerungkurz,stoerunglong`
- **Stop IDs**: Need to look up IDs for 1090 Wien stops (Alser Strasse, Währinger Strasse, etc.)
  - Find stop IDs from: `https://data.wien.gv.at/csv/wienerlinien-ogd-haltestellen.csv`
- **Multiple stops**: `?stopId=1234&stopId=5678` (comma or repeated param)
- **TRMNL strategy**: Polling (short interval) or Webhook push from a small server
- **Source**: https://www.wienerlinien.at/open-data

### 🗺️ OpenStreetMap / Overpass API (Spots)
- **Endpoint**: `https://overpass-api.de/api/interpreter`
- **Free, no key needed**
- Query POIs by category (tourism, historic, amenity) within a bounding box
- Can use for "Spot of the Day" — pull random interesting place in Vienna

### 🚢 Ships (Danube)
- AIS data mostly paywalled (MarineTraffic, VesselFinder)
- Free alternative: aisstream.io (WebSocket, free tier)
- Or: scrape marinetraffic with care, or skip for now

---

## TRMNL Plugin Development Notes

### Strategies
- **None**: Plugin is self-contained HTML with client-side JS. Best for real-time data (planes).
- **Polling**: TRMNL fetches your server URL. Need a public endpoint. Min interval ~15min on free plan.
- **Webhook**: You push data to TRMNL (max 2kB payload). Triggered by your own server/cron.

### Display specs
- Resolution: 800×480px
- Colors: Black & white (e-ink)
- Font rendering: crisp at larger sizes, avoid very small text
- TRMNL injects your markup into their base template with CSS variables

### Plugin HTML tips
- Use TRMNL's CSS variables for theming (they handle light/dark)
- Keep it dense but legible — e-ink rewards good contrast and clear hierarchy
- Monospace fonts work beautifully for data (altitude, speed, times)
- Avoid gradients — stick to solid fills and borders

---

## Project Structure

```
TRMNL/
├── CLAUDE.md              ← This file (context for Claude Code)
├── plugins/
│   ├── planes-vienna/
│   │   └── plugin.html    ← Complete self-contained plugin HTML
│   ├── wiener-linien/
│   │   └── plugin.html
│   └── osm-spots/
│       └── plugin.html
└── README.md
```

---

## Conversation Context

Started in Cowork mode. Brainstormed plugin ideas. Decided to build:
1. Planes over Vienna first (real-time, visually striking, OpenSky API)
2. Then Wiener Linien transit departures for 1090 Wien
3. TRMNL "None" strategy = client-side JS = live updates without server

Fabian's location: Wien 1090 (for transit stop lookups).

## Next Steps for Claude Code
1. Finish `plugins/planes-vienna/plugin.html`
2. Look up Wiener Linien stop IDs for 1090 (Alser Str, Währinger Str, Nussdorfer Str stops)
3. Build `plugins/wiener-linien/plugin.html`
4. Test both by pasting HTML into TRMNL's private plugin editor
