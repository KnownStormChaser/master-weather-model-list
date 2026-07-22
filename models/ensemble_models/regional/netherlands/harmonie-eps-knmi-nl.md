# HARMONIE-AROME EPS Netherlands (KNMI – Cy43 P2a)

## What this model is
HARMONIE-AROME EPS Netherlands is the **convection-permitting regional ensemble prediction system (EPS)** run by the **United Weather Centres-West (UWC-West)** partnership and distributed publicly by KNMI. It is the probabilistic counterpart to the deterministic [HARMONIE-AROME Netherlands (P1)](../../../nwp_models/regional/netherlands/harmonie-arome-netherlands.md) forecast, sharing the same Cy43 model, Dutch domain, and 390 × 390 grid, but providing an ensemble of forecasts to quantify short-range forecast uncertainty over the Netherlands.

**Important — this is a time-lagged ensemble.** Each hourly file contains only the control member plus **5 perturbed members**; the perturbed member numbers **rotate with the run hour**, so the complete 30-member ensemble must be assembled from **six consecutive hourly cycles**. See *Perturbations and design*.

---

## Who runs it
- **Organization:** KNMI (Royal Netherlands Meteorological Institute), as part of the United Weather Centres-West (UWC-West) partnership with the Icelandic Met Office, DMI (Denmark), and Met Éireann (Ireland)
- **Country / region:** Netherlands

---

## What area it covers
- **Coverage:** Netherlands, Belgium, and adjacent North Sea waters (the Dutch `ned` / `N20` domain)
- **Domain details:** Grid bounds verified from GRIB — 49.0–56.002°N, 0.0–11.281°E; identical to the deterministic P1 product

---

## Basic details
- **Model type:** Ensemble NWP (regional, limited-area)
- **Model system / core:** HARMONIE-AROME (Cycle 43), UWC-West configuration
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (native ~2 km)
- **Ensemble size:** **30 perturbed members + 1 control** (verified). Distributed **time-lagged**: each hourly run contains the control (`000`) plus 5 perturbed members; member IDs rotate over a 6-hour cycle.
- **Horizontal resolution:** ~0.029° longitude × ~0.018° latitude (~2 km) on a **regular** lat-lon grid, bi-linearly interpolated from the native ~2 km Lambert conformal model grid
- **Grid dimensions:** 390 × 390 points (verified)
- **Vertical levels:** 90 model levels (native model configuration); the public EPS files distribute **near-surface fields only** — see *What it provides*
- **Forecast length:** 0–60 h (verified: 61 hourly steps per member)
- **Update frequency / cycles:** Hourly
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes — the UWC-West production is self-cycling and assimilating (not file-verified here)
- **Method / cadence:** TBD

---

## Initial and boundary conditions (for limited-area ensembles)
- **Initial conditions:** UWC-West cycled analysis (per-member perturbed; scheme not verified)
- **Boundary conditions:** ECMWF IFS (standard UWC-West configuration; update cadence and whether boundary perturbations are applied not verified) — TBD

---

## Perturbations and design
- **Initial condition perturbations:** TBD (not documented in the dataset metadata or file headers)
- **Model/physics perturbations:** TBD
- **Stochastic schemes:** TBD
- **Time-lagging (verified):** This is the defining structural feature of the distribution. Five perturbed members are produced per hourly cycle, and member numbering advances with the run hour on a 6-hour rotation, so that members 1–30 are covered every six runs. The control member (`000`) is present in **every** cycle. Verified mapping:

  | Run hour (UTC) | H mod 6 | Perturbed members present |
  |---|---|---|
  | 00, 06, 12, 18 | 0 | 1–5 |
  | 01, 07, 13, 19 | 1 | 6–10 |
  | 02, 08, 14, 20 | 2 | 11–15 |
  | 03, 09, 15, 21 | 3 | 16–20 |
  | 04, 10, 16, 22 | 4 | 21–25 |
  | 05, 11, 17, 23 | 5 | 26–30 |

  To build the full 30-member ensemble, combine six consecutive hourly runs, accepting a lag of up to 6 hours on the oldest members. **A single file is not a complete ensemble** — treating one run's six members as the ensemble will severely under-sample the forecast distribution.

---

## What it provides
Individual **ensemble member forecasts** (raw members; no pre-computed ensemble mean, spread, probability, or percentile products are distributed — these must be derived by the user after assembling members across cycles).

Each member/timestep file carries 20 GRIB messages of **near-surface and screen-level parameters** (verified level structure):
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
- **Data formats:** GRIB edition 1. Delivered as one `.tar` archive per run (~1.88 GB), containing 366 per-member/per-timestep GRIB files named `harm43_v1_ned_uwcw_meteo_{MMM}_{YYYYMMDDHHMM}_{HHHMM}_GB`, where `{MMM}` is the zero-padded member number (`000` = control).
- **Data retention:** Only the most recent **72 hours** are kept on the KNMI Data Platform. No rolling archive. For historical runs, contact `licentiebureau@knmi.nl` (paid, personalised product).
- **Official download location:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p2a-1-0

### How to access — KNMI Open Data API
Access is exclusively via the KNMI Data Platform Open Data API; an API key is always required, passed in the `Authorization` header (no `Bearer` prefix).

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_p2a/versions/1.0/files
```

```
GET /open-data/v1/datasets/harmonie_arome_cy43_p2a/versions/1.0/files
GET /open-data/v1/datasets/harmonie_arome_cy43_p2a/versions/1.0/files/{filename}/url
```

Key options (anonymous / registered / bulk) are identical to the other KNMI HARMONIE datasets; see the [P1 entry](../../../nwp_models/regional/netherlands/harmonie-arome-netherlands.md#api-key-options). Because assembling a full ensemble requires six runs per valid time, a **registered key** (1000 requests/hour, non-expiring) is strongly recommended over the shared anonymous key.

### Update notifications
Notification Service (MQTT over WebSockets, `mqtt.dataplatform.knmi.nl:443`), topic:

```
dataplatform/file/v1/harmonie_arome_cy43_p2a/1.0/#
```

---

## Notes
- **Relationship to the deterministic counterpart.** P2a is the ensemble sibling of [P1](../../../nwp_models/regional/netherlands/harmonie-arome-netherlands.md), on the identical 390 × 390 regular lat-lon Dutch grid. P1 carries a richer deterministic variable and level set (including 300 m and 800–802 m fields); P2a trades vertical/variable richness for ensemble spread at screen level.
- **Relationship to the European ensemble.** [P4a](./harmonie-eps-knmi-eu.md) is the same ensemble system on the wider European DINI domain, with the same member count, rotation, forecast length, and variable set.
- **Public data is a subset of the full operational ensemble output** — only near-surface parameters are distributed.
- The grid is **regular** lat-lon, so wind components align with true geographic east/north; no rotation correction is needed (in contrast to the European P4a domain).
- **Time-lagging is easy to miss.** The dataset metadata does not state ensemble size, and a single archive looks like a 6-member ensemble. The 30+1 figure and the rotation table above were established by inspecting member IDs across six consecutive live runs.

---

## Official documentation
- KNMI Data Platform — dataset page:
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-p2a-1-0
- KNMI Open Data API documentation:
  https://developer.dataplatform.knmi.nl/open-data-api
- KNMI Notification Service documentation:
  https://developer.dataplatform.knmi.nl/notification-service
- KNMI HARMONIE overview and parameter tables:
  https://english.knmidata.nl/open-data/harmonie
