# HARMONIE-AROME Caribbean (KNMI – Cy43 BES)

## What this model is
HARMONIE-AROME Caribbean is a **regional numerical weather prediction (NWP) model** operated by KNMI for the Caribbean and northern South American region. It is a configuration of the HARMONIE-AROME modelling system at Cycle 43, run on the same UWC-West supercomputer infrastructure as the European and Dutch HARMONIE products but on separate Caribbean domains operated by KNMI for forecasting in the Dutch Caribbean (Bonaire, St. Eustatius, Saba — together "BES") and surrounding regions.

The dataset documented here — `harmonie_arome_cy43_bes` — is the **current** operational Caribbean HARMONIE distribution. It replaces the older `uwcw_extra_ha43_bess_0p05deg` (note: extra "s") dataset, which was deprecated on 27 April 2026 and stopped updating on 30 June 2026.

**Important — this dataset bundles three separate domains.** Unlike the European (P3/P5) and Dutch (P1) products, each `bes` file is an outer `.tar` archive containing **three nested tar archives**, one per model domain (verified from live data):

| App/common name | Internal code | Grid | Increment | Resolution | Coverage (verified bounds) |
|---|---|---|---|---|---|
| HARMONIE **Leeward** 2.8 km | `BEN` (*Benedenwindse*) | 101 × 101 | 0.025° | ~2.7 km | ABC islands / Bonaire — 70.0–67.5°W, 11.0–13.5°N |
| HARMONIE **Windward** 2.8 km | `BOV` (*Bovenwindse*) | 101 × 101 | 0.025° | ~2.7 km | SSS islands / Saba–St. Eustatius — 64.5–62.0°W, 16.5–19.0°N |
| HARMONIE **Caribbean** 5.5 km | `BESS` | 740 × 550 | 0.05° | ~5.5 km | Wide Caribbean — 79.5–42.55°W, −3.0–24.45°N |

> **Terminology note.** KNMI's Dutch domain names invert the English convention: *Benedenwindse* ("Leeward") is the **southern** ABC group (~12°N), and *Bovenwindse* ("Windward") is the **northern** SSS group (~17°N) — the opposite of how "Leeward/Windward Islands" are used in English-language geography. Weather applications that list "HARMONIE Leeward 2.8 km / Windward 2.8 km / Caribbean 5.5 km" are surfacing these three domains; they are not stale — all three are live inside the single `bes` dataset.

---

## Who runs it
- **Organization:** KNMI (Royal Netherlands Meteorological Institute)
- **Model lineage:** Shares Cycle 43 modelling code with the UWC-West partnership (KNMI, Iceland, Denmark, Ireland), but the Caribbean domains are operated by KNMI rather than as a joint UWC-West product
- **Country / region:** Netherlands (operating); Dutch Caribbean and surrounding regions (forecast area)

---

## What area it covers
- **Coverage (combined):**
  - Dutch Caribbean (Bonaire, St. Eustatius, Saba)
  - Windward and Leeward Islands
  - Suriname
  - Adjacent Caribbean Sea, Gulf of Mexico waters, and western tropical Atlantic
- **Three domains:** two 2.8 km island-scale nests (BEN, BOV) plus one 5.5 km wide-area domain (BESS) — see the table above for individual bounds.
- **Note on the wide-domain bounds:** the live BESS grid extends to **24.45°N** (verified from GRIB). KNMI's dataset-page bounding box lists 22.45°N as the north bound — a ~2° discrepancy; the live grid is authoritative.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** HARMONIE-AROME
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Mixed — the two 2.8 km nests (BEN, BOV) are effectively convection-permitting; the 5.5 km wide domain (BESS) is not, and relies on parameterised deep convection
- **Model version:** Cycle 43 (Cy43); the current Caribbean dataset has been published since 18 June 2024 and replaced an earlier Cy40 BES product
- **Horizontal resolution:** 0.025° (~2.7 km) for BEN and BOV; 0.05° (~5.5 km) for BESS — all on **regular** lat-lon grids, bi-linearly interpolated from the native Lambert conformal model grid (lineage: "Bi-linear interpolation from lambert to regular lat-lon")
- **Vertical levels:** 90 model levels (native model configuration); the public files distribute near-surface/boundary-layer/pressure-level subsets rather than the full column — see *What it provides*
- **Forecast length:** 0–60 h (verified: 61 hourly steps per run, all three domains)
- **Update frequency / cycles:** **3-hourly** (00/03/06/09/12/15/18/21 UTC) — verified from run timestamps
- **Temporal output resolution:** 1 hour

---

## What it provides
All three domains are GRIB edition 1 on regular lat-lon grids. Verified level content differs by domain:

**BEN / BOV (2.8 km island nests)**
- Height-above-ground levels: **0, 2, 10, 100, 250, 500, 750, 1000 m** (a deeper boundary-layer profile than the wide domain)
- Mean-sea-level pressure (height-above-sea 0)
- A single hybrid model level (level 65)
- No pressure levels

**BESS (5.5 km wide domain)**
- Height-above-ground levels: **0, 2, 10 m** (surface/near-surface only)
- **Pressure levels: 200, 500, 700, 850, 925 hPa**
- Mean-sea-level pressure and a whole-atmosphere field

Typical parameters across the domains include temperature, wind components, humidity, precipitation, cloud, and surface fields (see KNMI's parameter table). The wide BESS domain is appropriate for tropical convection at the parameterised scale, trade-wind regimes, heavy-rainfall and tropical-disturbance synoptic guidance, and pre-tropical-cyclone environmental fields; the 2.8 km nests add island-scale detail over the ABC and SSS groups.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1 (verified). Files are delivered as an outer `.tar` per run (~1.76 GB), each containing three inner `.tar` archives (`HA43_BEN_*`, `HA43_BESS_*`, `HA43_BOV_*`), which in turn hold per-timestep GRIB files named `HARM_{BEN|BESS|BOV}_YYYYMMDDHHMM_0HHMM_GB`. The deprecated `bess` predecessor was distributed in NetCDF — the migration to GRIB was a real format change, not just a rename.
- **Data retention:** Only the most recent **72 hours** are kept on the KNMI Data Platform. There is no rolling archive. For older Caribbean HARMONIE archives, contact the KNMI licensing office at `licentiebureau@knmi.nl` — this is a paid, personalised product.
- **Dataset landing page:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-bes-1-0

### How to access — KNMI Open Data API

KNMI distributes its open data exclusively through the **KNMI Data Platform Open Data API**. There is no anonymous FTP or open S3 bucket for HARMONIE; an API key is always required.

**API endpoint pattern:**

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files
```

To download a specific file, list the dataset to discover filenames, then request a presigned download URL:

```
GET /open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files
GET /open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files/{filename}/url
```

The API key is passed in the `Authorization` HTTP header (no `Bearer` prefix). Files are named `HA43_YYYYMMDDHH_GB.tar`. **Consumers must un-nest twice**: extract the three inner domain tars from the outer archive, then extract the per-timestep GRIB files from each.

### API key options

There are three key varieties (anonymous, registered, bulk), all free:

1. **Anonymous key** — published directly in the [Open Data API documentation](https://developer.dataplatform.knmi.nl/open-data-api). Shared across all unregistered users; **50 requests/minute and 3000 requests/hour, both shared globally**. Each key has a fixed expiry (now dated to **1 August**); as of this writing a key valid until **1 August 2027** is published, alongside one valid until 1 August 2026. Suitable for one-off downloads; not recommended for unattended scripts.

2. **Registered key** — obtained by creating a free account on the [KNMI Developer Portal](https://developer.dataplatform.knmi.nl/) and requesting an Open Data API key. Tied to a verified email address, does **not** expire, and provides a private quota of **1000 requests/hour** (200 requests/second rate limit). Recommended for automated or production use.

3. **Bulk key** — for full-dataset downloads exceeding the standard quota, requested by emailing `opendata@knmi.nl` with subject "KDP complete dataset download" and the dataset name and version, from the registered email. A bulk key permits downloading the dataset **once** (fair-use policy).

### Update notifications
A separate **Notification Service** (MQTT over WebSockets at `mqtt.dataplatform.knmi.nl:443`) pushes an event when a new file is published, removing the need for polling — KNMI recommends it over polling, which it considers abuse if excessive. Access requires a free Developer Portal API key (used as the MQTT password; username unused). Topic pattern:

```
dataplatform/file/v1/harmonie_arome_cy43_bes/1.0/#
```

---

## Notes
- **Three domains in one dataset.** The single `bes` product bundles the BEN (Leeward 2.8 km), BOV (Windward 2.8 km), and BESS (Caribbean 5.5 km) domains as nested tars. This is the distinguishing feature of the Caribbean product versus the European (P3/P5) and Dutch (P1) distributions, which each contain a single grid.
- The grids are **regular** lat-lon (matching P1, in contrast to P3/P5's rotated grid), so wind components are aligned with true geographic east/north and no rotation correction is needed.
- GRIB files are encoded in GRIB **edition 1**, with parameters identified by `indicatorOfParameter`. KNMI publishes a parameter code table for the Caribbean domain (under the heading "HARMONIE Cy43 BESS") at https://english.knmidata.nl/open-data/harmonie. Note the parameter-table reference still uses "BESS".
- All KNMI HARMONIE Cy43 products (Netherlands P1, Europe P3/P5, Caribbean BES, ensemble P2/P4, etc.) share the Cy43 codebase. The Caribbean domains are a KNMI-only configuration and are **not** part of the shared UWC-West DINI integration.

---

## Migration from the deprecated `bess` dataset (historical)
The predecessor `uwcw_extra_ha43_bess_0p05deg` was deprecated on 27 April 2026 and stopped updating on **30 June 2026** — a deadline that has now passed (as of July 2026). Integrations still pointing at the old dataset path or the NetCDF format should already have migrated to `harmonie_arome_cy43_bes` (GRIB1). *(Retired-dataset status not re-verified in this pass.)*

---

## Official documentation
- KNMI Data Platform — current dataset page:
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-bes-1-0
- Deprecation notice for the predecessor `bess` dataset:
  https://developer.dataplatform.knmi.nl/deprecation-uwcw_extra_ha43
- KNMI Open Data API documentation:
  https://developer.dataplatform.knmi.nl/open-data-api
- KNMI Notification Service documentation:
  https://developer.dataplatform.knmi.nl/notification-service
- KNMI Developer Portal:
  https://developer.dataplatform.knmi.nl/
- KNMI HARMONIE overview and parameter tables:
  https://english.knmidata.nl/open-data/harmonie
