# HARMONIE-AROME Europe / DINI (KNMI – Cy43 P3 & P5)

## What this model is
HARMONIE-AROME Europe is the **convection-permitting regional numerical weather prediction (NWP) model** run cooperatively by the **United Weather Centres-West (UWC-West)** partnership of KNMI (Netherlands), the Icelandic Met Office, DMI (Denmark), and Met Éireann (Ireland), and distributed publicly by KNMI through the KNMI Data Platform.

This entry covers KNMI's two open **European-scale DINI-domain** (**D**enmark–**I**celand–**N**etherlands–**I**reland) distributions of that run:
- **P3** (`harmonie_arome_cy43_p3`) — near-surface, boundary-layer, and a small set of pressure-level parameters.
- **P5** (`harmonie_arome_cy43_p5`) — selected parameters on **all model (hybrid) levels**, for full-column vertical profiles.

Both are packaged from the same UWC-West Cy43 run on an identical rotated lat-lon grid; they differ only in which parameters and levels are retained. The same underlying run is also available over the smaller Dutch domain (P1) and the Caribbean domain (BES); see the related KNMI HARMONIE entries.

---

## Who runs it
- **Operating partnership:** United Weather Centres-West (UWC-West)
  - KNMI (Royal Netherlands Meteorological Institute)
  - Icelandic Met Office (Veðurstofa Íslands)
  - DMI (Danish Meteorological Institute)
  - Met Éireann (Ireland)
- **Public distributor (this dataset):** KNMI
- **Country / region:** Netherlands (distribution); multi-national (operations)

---

## What area it covers
- **Coverage:** Large parts of north-western and central Europe, extending from East Greenland to southern Italy
- **Domain name:** DINI (Denmark–Iceland–Netherlands–Ireland)
- **Geographic bounding box (per KNMI metadata):**
  - North: 62.6°N
  - South: 38.75°N
  - West: 25.0°W
  - East: 16.0°E
- **Native grid extent (rotated coordinates, from GRIB):** first point (−13.5° lon, −13.6° lat) to last point (20.25° lon, 14.55° lat), rotated south pole at (−35.0° lat, −8.0° lon)

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system / core:** HARMONIE-AROME
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (at the native 2 km scale; note the public grid is regridded coarser — see resolution)
- **Model version:** Cycle 43 (Cy43); operational at UWC-West since 20 June 2024 after a one-year test period, replacing the deprecated KNMI-only Cy40 datasets
- **Native horizontal resolution:** ~2 km (Lambert conformal)
- **Public distribution grid (P3 & P5):** ~0.05° on a **rotated** lat-lon grid — bi-linear interpolation from the native Lambert conformal grid (lineage: "Bi-linear interpolation from lambert to rotated lat-lon")
- **Grid dimensions (verified from GRIB):** 676 × 564 points (Ni × Nj), 0.05° × 0.05° increments
- **Vertical levels:** 90 hybrid model levels (verified from P5 hybrid-level data; increased from 65 in the previous Cy40 configuration)
- **Forecast length:** 0–60 h (verified: 61 hourly steps per run)
- **Update frequency / cycles:** Hourly (1× per hour)
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes — DINI is a self-cycling assimilating production (standard UWC-West configuration; not file-verified here)
- **Method / cadence:** TBD

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** UWC-West DINI cycled analysis
- **Boundary conditions:** ECMWF IFS (standard UWC-West configuration; update cadence TBD, not file-verified here)

---

## What it provides

**P3 — near-surface, boundary-layer, and pressure-level subset**
- Surface and near-surface fields at **0, 2, 10, 50, 100, 200, and 300 m** above ground (temperature, humidity, wind components, precipitation, cloud cover, radiation/flux fields, visibility, and related quantities)
- A small standard set of **pressure levels — 300, 500, 700, 850, 925 hPa** (geopotential, temperature, u/v wind, relative humidity)
- Mean-sea-level and whole-atmosphere fields
- Suitable for frontal precipitation and convective systems over NW Europe, boundary-layer fields, and regional pressure/temperature patterns

**P5 — all model levels, selected parameters**
- **Temperature, u/v wind components, and specific humidity on all 90 hybrid model levels** (verified: GRIB1 params 11/33/34/51 × 90 levels)
- Surface pressure and orography (geopotential), plus a few near-surface fields, to reconstruct full vertical profiles
- Suitable for vertical profiles/soundings, hub-height and upper-level wind, trajectory and dispersion applications, and any use needing the full model column

The full operational output (ensemble, renewable-energy-focused, and other variable subsets) is distributed under further dataset names (P2a/b, P4a/b, etc.).

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1 (parameters identified by `indicatorOfParameter`; files delivered as per-timestep GRIB bundled into hourly `.tar` archives — one archive per run, ~3.4 GB for P3, ~17 GB for P5)
- **Data retention:** **Only the most recent 72 hours** are kept on the KNMI Data Platform for both P3 and P5. There is no rolling archive (unlike P1, which began retaining files from 8 January 2026 onward). For historical runs, contact the KNMI licensing office at `licentiebureau@knmi.nl` — this is a paid, personalised product.
- **Dataset landing pages:**
  - P3: https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p3-1-0
  - P5: https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p5-1-0

### How to access — KNMI Open Data API

KNMI distributes its open data exclusively through the **KNMI Data Platform Open Data API**. There is no anonymous FTP or open S3 bucket for HARMONIE; an API key is always required.

**API endpoint patterns:**

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_p3/versions/1.0/files
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_p5/versions/1.0/files
```

To download a specific file, list the dataset to discover filenames, then request a presigned download URL:

```
GET /open-data/v1/datasets/{dataset}/versions/1.0/files
GET /open-data/v1/datasets/{dataset}/versions/1.0/files/{filename}/url
```

The API key is passed in the `Authorization` HTTP header (no `Bearer` prefix). File naming inside each archive encodes run time and lead time, e.g. `HA43_N55_YYYYMMDDHHMM_0HHMM_GB` (P3) and `HA43_N55ML_YYYYMMDDHHMM_0HHMM_GB` (P5, `ML` = model levels).

### API key options

There are three key varieties (anonymous, registered, bulk), all free:

1. **Anonymous key** — published directly in the [Open Data API documentation](https://developer.dataplatform.knmi.nl/open-data-api). Shared across all unregistered users; **50 requests/minute and 3000 requests/hour, both shared globally**. Each key has a fixed expiry (now dated to **1 August**); as of this writing a key valid until **1 August 2027** is published, alongside one valid until 1 August 2026. Suitable for one-off downloads and exploration; not recommended for unattended scripts, as it stops working at expiry.

2. **Registered key** — obtained by creating a free account on the [KNMI Developer Portal](https://developer.dataplatform.knmi.nl/) and requesting an Open Data API key. Tied to a verified email address, does **not** expire, and provides a private quota of **1000 requests/hour** (200 requests/second rate limit). Recommended for automated or production use.

3. **Bulk key** — for full-dataset downloads that exceed the standard quota, requested by emailing `opendata@knmi.nl` with subject "KDP complete dataset download" and the dataset name and version, from the email registered on the Developer Portal. Note KNMI's fair-use policy: a bulk key permits downloading the dataset **once**.

### Update notifications
A separate **Notification Service** (MQTT over WebSockets at `mqtt.dataplatform.knmi.nl:443`) pushes an event when a new file is published, removing the need for polling — KNMI explicitly recommends it over polling, which it considers abuse if excessive. Access requires a free Developer Portal API key (used as the MQTT password; username unused). Topic pattern:

```
dataplatform/file/v1/harmonie_arome_cy43_p3/1.0/#
dataplatform/file/v1/harmonie_arome_cy43_p5/1.0/#
```

---

## Notes
- All KNMI HARMONIE Cy43 products (Netherlands P1, Europe P3/P5, Caribbean BES, ensemble P2/P4, etc.) come from the same UWC-West model run and differ only by domain, distributed grid, retained variable/level set, and (for P2/P4) deterministic vs ensemble output. **P3 and P5 are the same run on the same rotated 676 × 564 / 0.05° grid** — P3 retains near-surface/boundary-layer/pressure-level fields, P5 retains T/u/v/q on all 90 model levels.
- The grid is **rotated** lat-lon (rotated south pole at −35.0° lat, −8.0° lon) — wind components are defined relative to the rotated grid axes, not true north. Software ingesting these files must account for this when computing geographic wind direction. (Contrast with the P1 Dutch dataset, which is on a regular lat-lon grid.)
- GRIB files are encoded in GRIB **edition 1**. KNMI publishes parameter code tables for HARMONIE at https://english.knmidata.nl/open-data/harmonie.
- The DINI domain is the operational European backbone for all four UWC-West partners; DMI distributes its own subset of the same domain as the [HARMONIE-AROME (DMI) DINI dataset](../denmark/harmonie-dmi.md), and Met Éireann distributes its slice via the [Met Éireann Open Data Portal](../ireland/harmonie-arome-ireland.md). These are sourced from the same run but packaged independently.

---

## Official documentation
- KNMI Data Platform — dataset pages:
  - P3: https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p3-1-0
  - P5: https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p5-1-0
- KNMI Open Data API documentation:
  https://developer.dataplatform.knmi.nl/open-data-api
- KNMI Notification Service documentation:
  https://developer.dataplatform.knmi.nl/notification-service
- KNMI Developer Portal:
  https://developer.dataplatform.knmi.nl/
- KNMI HARMONIE overview and parameter tables:
  https://english.knmidata.nl/open-data/harmonie
- UWC-West collaboration background (Icelandic Met Office):
  https://en.vedur.is/about-imo/news/new-icelandic-met-office-weather-and-climate-supercomputer-becomes-operational
