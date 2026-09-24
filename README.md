# geo-viewer — see any geometry on a map, right inside Claude

[![CI](https://github.com/YOUR-GITHUB-USERNAME/geo-viewer/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR-GITHUB-USERNAME/geo-viewer/actions)
![License: MIT](https://img.shields.io/badge/license-MIT-blue)

Paste or upload coordinates in almost any format and Claude draws them as an interactive,
pan-and-zoom map in the chat — with attribute pop-ups, colour-by-field and an open-data basemap.
Built for GIS, telecom and utility people who spend their day squinting at WKT.

**Live demo (static):** open [`examples/demo_map.html`](examples/demo_map.html) in a browser
(or enable GitHub Pages and browse to `/examples/demo_map.html`).

## Supported input

| Format | Notes |
|---|---|
| GeoJSON, Esri JSON | incl. legacy `crs` member (ArcGIS exports) |
| WKT / EWKT | many geometries at once, Z/M, `SRID=` prefix, WKT buried in SQL |
| WKB / EWKB hex | PostGIS output, either byte order |
| KML / KMZ, GPX, GML | placemarks, tracks, `posList` |
| CSV / TSV | lat/lon, x/y or a WKT/WKB column |
| Shapefile | `.shp` + `.dbf` or a zip of several layers; `.prj` detected |
| Coordinate lists | `x y`, `x,y`, `[x, y]` per line |

Projected data (State Plane, British National Grid, UTM, …) is drawn as-is, or reprojected to
WGS84 (`--to-wgs84`, uses `pyproj`) when you want a basemap.

## Basemap

Chat widgets can't load map tiles, so the skill downloads open vector data at build time,
clips it to your extent and embeds it:

- **Natural Earth** (public domain) — borders, roads, rail, rivers, places. Works everywhere.
- **OpenStreetMap** via Overpass (© OpenStreetMap contributors, ODbL) — street level. In claude.ai,
  add `overpass-api.de` to the allowed network domains.

## Install

**claude.ai** — download `geo-viewer.skill` from [Releases](https://github.com/YOUR-GITHUB-USERNAME/geo-viewer/releases)
(or run `python tools/build_skill.py`) and upload it in *Settings → Capabilities → Skills*.

**Claude Code** — this repo is a plugin marketplace:

```
/plugin marketplace add YOUR-GITHUB-USERNAME/geo-viewer
/plugin install geo-viewer@geo-viewer
```

**Plain Python** (no Claude needed):

```bash
python skills/geo-viewer/scripts/normalize.py examples/backbone_wkt.csv --out /tmp/data.geojson
python skills/geo-viewer/scripts/basemap.py /tmp/data.geojson --out /tmp/bm.geojson
python skills/geo-viewer/scripts/build_viewer.py /tmp/data.geojson --basemap /tmp/bm.geojson --standalone --out map.html
```

## Usage in Claude

Just paste or upload data — the skill triggers on its own:

> *"POLYGON((…))  — does this look right?"*
> *"Plot these points: 12.97,77.59 / 12.98,77.60"* (lat,lon is detected and swapped)
> *(upload a zipped shapefile)* *"show me this"*

## Limits

- No raster/satellite imagery (tiles are blocked inside chat widgets).
- Large datasets are thinned (every Nth vertex) for the inline view; use `--standalone` for full detail.

## Development

```bash
pip install -r requirements-dev.txt
pytest -q
python tools/build_skill.py   # -> dist/geo-viewer.skill
```

## Related

[network-qa](https://github.com/YOUR-GITHUB-USERNAME/network-qa) — finds and auto-corrects geometry and topology errors in network data.

## Licence

MIT © Bhuvenesh. Basemap data: Natural Earth (public domain); OpenStreetMap data © OpenStreetMap contributors, ODbL.
Not affiliated with or endorsed by Anthropic.
