# HARMONIE-AROME EPS Europe / DINI (KNMI – Cy43 P4a)

## What this model is
HARMONIE-AROME EPS Europe is the **convection-permitting regional ensemble prediction system (EPS)** run by the **United Weather Centres-West (UWC-West)** partnership and distributed publicly by KNMI, covering the European **DINI** (Denmark–Iceland–Netherlands–Ireland) domain. It is the probabilistic counterpart to the deterministic [HARMONIE-AROME Europe P3/P5](../../../nwp_models/regional/netherlands/harmonie-knmi.md) forecasts, sharing the same Cy43 model, domain, and rotated 676 × 564 grid.

**Important — this is a time-lagged ensemble.** Each hourly file contains only the control member plus **5 perturbed members**; the perturbed member numbers **rotate with the run hour**, so the complete 30-member ensemble must be assembled from **six consecutive hourly cycles**. See *Perturbations and design*.

---

## Who runs it
- **Organization:** KNMI (Royal Netherlands Meteorological Institute), as part of the United Weather Centres-West (UWC-West) partnership with the Icelandic Met Office, DMI (Denmark), and Met Éireann (Ireland)
- **Country / region:** Netherlands (distribution); multi-national (operations)

---

## What area it covers
- **Coverage:** Large parts of north-western and central Europe, from East Greenland to southern Italy (the DINI domain)
- **Domain details:**
  - Geographic bounding box (KNMI metadata): 62.6°N / 38.75°S-bound / 25.0°W / 16.0°E
  - Native grid extent (rotated coordinates, verified from GRIB): first point (−13.5°, −13.6°) to last point (20.25°, 14.55°), rotated south pole at (−35.0° lat, −8.0° lon)

---

## Basic details
- **Model type:** Ensemble NWP (regional, limited-area)
- **Model system / core:** HARMONIE-AROME (Cycle 43), UWC-West configuration
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes at the native ~2 km scale (note the public grid is regridded to ~0.05°)
- **Ensemble size:** **30 perturbed members + 1 control** (verified). Distributed **time-lagged**: each hourly run contains the control (`000`) plus 5 perturbed members; member IDs rotate over a 6-hour cycle.
- **Horizontal resolution:** ~0.05° on a **rotated** lat-lon grid, bi-linearly interpolated from the native ~2 km Lambert conformal model grid
- **Grid dimensions:** 676 × 564 points (verified)
- **Vertical levels:** 90 model levels (native model configuration); the public EPS files distribute **near-surface fields only** — see *What it provides*
- **Forecast length:** 0–60 h (verified: 61 hourly steps per member)
- **Update frequency / cycles:** Hourly
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes — the UWC-West DINI production is self-cycling and assimilating (not file-verified here)
- **Method / cadence:** TBD

---

## Initial and boundary conditions (for limited-area ensembles)
- **Initial conditions:** UWC-West DINI cycled analysis (per-member perturbed; scheme not verified)
- **Boundary conditions:** ECMWF IFS (standard UWC-West configuration; update cadence and whether boundary perturbations are applied not verified) — TBD

---

## Perturbations and design
- **Initial condition perturbations:** TBD (not documented in the dataset metadata or file headers)
- **Model/physics perturbations:** TBD
- **Stochastic schemes:** TBD
- **Time-lagging (verified):** Five perturbed members are produced per hourly cycle, with member numbering advancing on a 6-hour rotation so that members 1–30 are covered every six runs. The control member (`000`) is present in **every** cycle. Verified mapping:

  | Run hour (UTC) | H mod 6 | Perturbed members present |
  |---|---|---|
  | 00, 06, 12, 18 | 0 | 1–5 |
  | 01, 07, 13, 19 | 1 | 6–10 |
  | 02, 08, 14, 20 | 2 | 11–15 |
  | 03, 09, 15, 21 | 3 | 16–20 |
  | 04, 10, 16, 22 | 4 | 21–25 |
  | 05, 11, 17, 23 | 5 | 26–30 |

  To build the full 30-member ensemble, combine six consecutive hourly runs, accepting a lag of up to 6 hours on the oldest members. **A single file is not a complete ensemble.**

---

## What it provides
Individual **ensemble member forecasts** (raw members; no pre-computed ensemble mean, spread, probability, or percentile products are distributed — these must be derived by the user after assembling members across cycles).

Each member/timestep file carries 20 GRIB messages of **near-surface and screen-level parameters** (verified level structure, identical to P2a):
- **2 m:** temperature, relative humidity
- **10 m:** wind components (u/v) and wind gusts
- **50 m:** wind components (u/v)
- **Surface (0 m):** visibility, snow, cloud cover (total/low/medium/high), and related surface fields
- **Mean sea level:** pressure
- **Whole atmosphere:** two integrated fields

No pressure-level or model-level output is included. Parameters are identified by GRIB1 `indicatorOfParameter`; consult KNMI's HARMONIE parameter table for exact code meanings.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1. Delivered as one `.tar` archive per run (~5.85 GB), containing 366 per-member/per-timestep GRIB files named `harm43_v1_eur_uwcw_meteo_{MMM}_{YYYYMMDDHHMM}_{HHHMM}_GB`, where `{MMM}` is the zero-padded member number (`000` = control).
- **Data retention:** Only the most recent **72 hours** are kept on the KNMI Data Platform. No rolling archive. For historical runs, contact `licentiebureau@knmi.nl` (paid, personalised product).
- **Official download location:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p4a-1-0

### How to access — KNMI Open Data API
Access is exclusively via the KNMI Data Platform Open Data API; an API key is always required, passed in the `Authorization` header (no `Bearer` prefix).

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_p4a/versions/1.0/files
```

```
GET /open-data/v1/datasets/harmonie_arome_cy43_p4a/versions/1.0/files
GET /open-data/v1/datasets/harmonie_arome_cy43_p4a/versions/1.0/files/{filename}/url
```

Key options (anonymous / registered / bulk) are identical to the other KNMI HARMONIE datasets; see the [P1 entry](../../../nwp_models/regional/netherlands/harmonie-arome-netherlands.md#api-key-options). Given ~5.85 GB per run and six runs needed per full ensemble (~35 GB per valid time), a **registered or bulk key** is strongly recommended.

### Update notifications
Notification Service (MQTT over WebSockets, `mqtt.dataplatform.knmi.nl:443`), topic:

```
dataplatform/file/v1/harmonie_arome_cy43_p4a/1.0/#
```

---

## Notes
- **Relationship to the deterministic counterpart.** P4a is the ensemble sibling of the [P3/P5 European datasets](../../../nwp_models/regional/netherlands/harmonie-knmi.md), on the identical 676 × 564 rotated lat-lon DINI grid. P3 adds pressure levels and P5 adds all 90 model levels; P4a instead provides ensemble spread at screen level only.
- **Relationship to the Dutch ensemble.** [P2a](./harmonie-eps-knmi-nl.md) is the same ensemble system on the smaller Dutch domain, with the same member count, rotation, forecast length, and variable set. P2a is on a regular grid; P4a is rotated.
- **Rotated grid caveat.** Wind components are defined relative to the rotated grid axes (rotated south pole at −35.0° lat, −8.0° lon), **not** true north. Software computing geographic wind direction — including any ensemble wind-probability product — must apply the rotation correction.
- **Public data is a subset of the full operational ensemble output** — only near-surface parameters are distributed.
- **Time-lagging is easy to miss.** The dataset metadata does not state ensemble size, and a single archive looks like a 6-member ensemble. The 30+1 figure and the rotation table were established by inspecting member IDs across six consecutive live runs.

---

## Official documentation
- KNMI Data Platform — dataset page:
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p4a-1-0
- KNMI Open Data API documentation:
  https://developer.dataplatform.knmi.nl/open-data-api
- KNMI Notification Service documentation:
  https://developer.dataplatform.knmi.nl/notification-service
- KNMI HARMONIE overview and parameter tables:
  https://english.knmidata.nl/open-data/harmonie
