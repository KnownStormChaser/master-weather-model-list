# HARMONIE-AROME Netherlands (KNMI – Cy43 P1)

## What this model is
HARMONIE-AROME Netherlands is the **convection-permitting regional numerical weather prediction (NWP) model** run cooperatively by the **United Weather Centres-West (UWC-West)** partnership of KNMI (Netherlands), the Icelandic Met Office, DMI (Denmark), and Met Éireann (Ireland), and distributed publicly by KNMI through the KNMI Data Platform.

The dataset documented here — `harmonie_arome_cy43_p1` — is the **Dutch-domain** output of the same UWC-West model run that produces the [European DINI datasets (P3/P5)](./harmonie-knmi.md), packaged at near-native resolution on a **regular** lat-lon grid covering the Netherlands, Belgium, and surrounding North Sea waters. It is KNMI's primary public deterministic forecast product for the Netherlands, optimised for short-range forecasting of small-scale, rapidly evolving weather such as showers, thunderstorms, fog, and local wind effects.

---

## Who runs it
- **Operating partnership:** United Weather Centres-West (UWC-West)
  - KNMI (Royal Netherlands Meteorological Institute)
  - Icelandic Met Office (Veðurstofa Íslands)
  - DMI (Danish Meteorological Institute)
  - Met Éireann (Ireland)
- **Public distributor (this dataset):** KNMI
- **Country / region:** Netherlands

---

## What area it covers
- **Coverage:** Netherlands, Belgium, and adjacent North Sea waters
- **Domain type:** Dutch-domain cutout of the larger UWC-West DINI integration (internal domain code `N20`)
- **Grid bounds (verified from GRIB, matches KNMI metadata):**
  - North: 56.002°N
  - South: 49.0°N
  - West: 0.0°E
  - East: 11.281°E
- **Grid dimensions (verified from GRIB):** 390 × 390 points

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system / core:** HARMONIE-AROME
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (native ~2 km)
- **Model version:** Cycle 43 (Cy43); operational at UWC-West since 20 June 2024 after a one-year test period, replacing the deprecated KNMI-only Cy40 P1 dataset
- **Native horizontal resolution:** ~2 km (Lambert conformal)
- **Public distribution grid (P1):** ~0.029° longitude × ~0.018° latitude on a **regular** lat-lon grid (verified: iDirectionIncrement 0.029°, jDirectionIncrement 0.018°) — bi-linear interpolation from the native Lambert conformal grid (lineage: "Bi-linear interpolation from lambert to regular lat-lon"). At Dutch latitudes this is ~2 km in both directions, i.e. close to native resolution.
- **Vertical levels:** 90 model levels (native model configuration; increased from 65 in the previous Cy40 configuration). Note P1 itself distributes only near-surface/boundary-layer fields, not the full model column — see *What it provides*.
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
P1 is a **near-surface and boundary-layer subset** of the same convection-permitting 2 km UWC-West run that drives the European P3/P5 datasets. It contains **no pressure levels and no model levels** (contrast P3, which adds pressure levels, and P5, which adds all 90 model levels). Verified level/parameter content:

- **Surface (0 m):** surface pressure, orography (geopotential), temperature, visibility, total precipitation, snow fields, total/low/medium/high cloud cover, land–sea mask, radiation and surface-flux fields, and related quantities
- **2 m:** temperature, dewpoint, relative humidity
- **10 m:** wind (u/v) components and gusts
- **50, 100, 200, 300 m:** temperature and wind (u/v)
- **800, 801, 802 m:** temperature only (three isolated levels — present in the files despite KNMI's abstract describing the product as "up to 300 m")
- **Whole-atmosphere fields:** a small number of integrated quantities

Compared to P3, P1 trades geographic coverage for two practical advantages:
- a **regular** (rather than rotated) lat-lon grid, simplifying client-side processing and removing the need to handle wind rotation
- distribution at near-native resolution rather than at a regridded 0.05° spacing

It is convection-permitting and optimised for convective precipitation and thunderstorms, low clouds and fog, local wind effects (including coastal and lake-effect circulations), and frontal precipitation over the Low Countries.

The same model runs are also distributed under several other dataset names emphasising different variable subsets and use cases — including a deterministic aviation-focused product (`uwcw_extra_lv_ha43_nl_2km`) and ensemble products (P2a, P2b) on related grids.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1 (parameters identified by `indicatorOfParameter`; files delivered as per-timestep GRIB bundled into hourly `.tar` archives, ~825–865 MB per run)
- **Data retention:** **Files have been retained on the KNMI Data Platform from 8 January 2026 onward** as a continually growing rolling archive (dataset start time: 2026-01-08). Per KNMI's announcement, this archive policy was introduced specifically for P1 due to its popularity and was **not applied retroactively** — runs prior to 8 January 2026 are not available. Other HARMONIE datasets (P3/P5 Europe, BES Caribbean, P2/P4 ensembles) continue to use the standard 72-hour rolling-deletion policy. For older P1 archival data, contact the KNMI licensing office at `licentiebureau@knmi.nl` — this is a paid, personalised product.
- **Dataset landing page:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p1-1-0

### How to access — KNMI Open Data API

KNMI distributes its open data exclusively through the **KNMI Data Platform Open Data API**. There is no anonymous FTP or open S3 bucket for HARMONIE; an API key is always required.

**API endpoint pattern:**

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_p1/versions/1.0/files
```

To download a specific file, list the dataset to discover filenames, then request a presigned download URL:

```
GET /open-data/v1/datasets/harmonie_arome_cy43_p1/versions/1.0/files
GET /open-data/v1/datasets/harmonie_arome_cy43_p1/versions/1.0/files/{filename}/url
```

The API key is passed in the `Authorization` HTTP header (no `Bearer` prefix). File naming inside each archive encodes run time and lead time, e.g. `HA43_N20_YYYYMMDDHHMM_0HHMM_GB` (`N20` = the Dutch P1 domain).

### API key options

There are three key varieties (anonymous, registered, bulk), all free:

1. **Anonymous key** — published directly in the [Open Data API documentation](https://developer.dataplatform.knmi.nl/open-data-api). Shared across all unregistered users; **50 requests/minute and 3000 requests/hour, both shared globally**. Each key has a fixed expiry (now dated to **1 August**); as of this writing a key valid until **1 August 2027** is published, alongside one valid until 1 August 2026. Suitable for one-off downloads and exploration; not recommended for unattended scripts, as it stops working at expiry.

2. **Registered key** — obtained by creating a free account on the [KNMI Developer Portal](https://developer.dataplatform.knmi.nl/) and requesting an Open Data API key. Tied to a verified email address, does **not** expire, and provides a private quota of **1000 requests/hour** (200 requests/second rate limit). Recommended for automated or production use. The growing P1 archive in particular is well-suited to registered or bulk access for research use cases.

3. **Bulk key** — for full-dataset downloads that exceed the standard quota, requested by emailing `opendata@knmi.nl` with subject "KDP complete dataset download" and the dataset name and version, from the email registered on the Developer Portal. Note KNMI's fair-use policy: a bulk key permits downloading the dataset **once**.

### Update notifications
A separate **Notification Service** (MQTT over WebSockets at `mqtt.dataplatform.knmi.nl:443`) pushes an event when a new file is published, removing the need for polling — KNMI explicitly recommends it over polling, which it considers abuse if excessive. Access requires a free Developer Portal API key (used as the MQTT password; username unused). Topic pattern:

```
dataplatform/file/v1/harmonie_arome_cy43_p1/1.0/#
```

---

## Notes
- All KNMI HARMONIE Cy43 products (Netherlands P1, Europe P3/P5, Caribbean BES, ensemble P2/P4, etc.) come from the same UWC-West model run and differ only by domain, distributed grid, retained variable/level set, and (for P2/P4) deterministic vs ensemble output.
- The grid in P1 is **regular** lat-lon (verified `regular_ll`), so wind components are aligned with true geographic east/north and no rotation correction is needed when computing wind direction — in contrast to the [P3/P5 European datasets](./harmonie-knmi.md), which are on a rotated grid.
- GRIB files are encoded in GRIB **edition 1**, with parameters identified by `indicatorOfParameter` (not the GRIB2 category/number scheme). KNMI publishes a parameter code table for P1 at https://english.knmidata.nl/open-data/harmonie.
- Forecasts are issued **hourly**, reflecting the publication frequency of new forecast files from the operational UWC-West run cycle.
- P1's **growing retained archive from 8 January 2026** makes it materially more useful for historical research and verification work than the other KNMI HARMONIE products, which are limited to the most recent 72 hours.

---

## Official documentation
- KNMI Data Platform — dataset page:
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p1-1-0
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
