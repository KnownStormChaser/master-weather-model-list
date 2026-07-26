# KNMI Seamless Precipitation Ensemble Nowcast (Netherlands — pySTEPS blend)

## What this model is
The KNMI Seamless Precipitation Ensemble Nowcast is an experimental probabilistic precipitation nowcasting system for the Netherlands. It produces a **20-member ensemble** of precipitation-intensity forecasts (and derived exceedance probabilities) out to **6 hours** at 5-minute resolution, by **seamlessly blending radar extrapolation with an NWP ensemble** — an experimental KNMI implementation of the open-source [pySTEPS](https://pysteps.github.io) library. It is the probabilistic, longer-range companion to the deterministic [KNMI Radar Nowcast](./knmi-radar-nowcast.md).

> **Pilot status.** KNMI distributes this as a **pilot** product that "may undergo changes or be discontinued at any time without prior notice." The dataset metadata states the pilot would last "until January 31st 2026 at the latest" — however, the product was still producing files operationally (every 5 minutes) as of late July 2026, so the pilot appears to have been extended past its stated end date, or the metadata is stale. Treat availability as provisional and re-check before depending on it. (Candidate for the repository's `STATUS.md` experimental list.)

---

## Who runs it
- **Organization:** Royal Netherlands Meteorological Institute (KNMI)
- **Country / region:** Netherlands

---

## What area it covers
- **Coverage:** Netherlands (and immediate surroundings)
- **Domain details:** RAD_NL25 grid — 700 × 765 points, 1 km, polar-stereographic (`+proj=stere +lat_0=90 +lon_0=0 +lat_ts=60`). In the NetCDF the field dimensions are `y=765, x=700`, with 2D `lat`/`lon` coordinate arrays and a `polar_stereographic` grid-mapping variable (CF-1.7).

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Seamless extrapolation–NWP blend
- **Technique / algorithm:** Experimental KNMI implementation of **pySTEPS** — STEPS-style stochastic ensemble nowcasting (spectral-cascade decomposition with stochastic perturbations), blended with an NWP ensemble across lead time
- **Underlying / driving model:** [HARMONIE-AROME Cy43 ensemble](../../../ensemble_models/regional/netherlands/harmonie-eps-knmi-nl.md) (the seamless blend transitions from radar extrapolation toward the HARMONIE-AROME Cy43 EPS at longer lead times)
- **Probabilistic / ensemble:** Yes — **20-member** stochastic ensemble; exceedance probabilities derived from it
- **Horizontal resolution:** 1 km
- **Vertical structure:** 2D single-level surface precipitation
- **Lead time:** 0–6 hours (72 time steps: +5 to +360 minutes)
- **Update frequency:** Every 5 minutes
- **Temporal output resolution:** 5 minutes
- **Latency:** Files published ~4 minutes after reference time (observed from creation timestamps)

---

## Inputs
- **Radar:** Initiated from the KNMI **RTCOR-5m** 5-minute real-time radar/gauge precipitation accumulation ([KNMI Radar](../../../../observations/radar/netherlands/knmi-radar.md)).
- **NWP fields:** **HARMONIE-AROME Cy43 ensemble** forecast (see [KNMI HARMONIE-AROME EPS](../../../ensemble_models/regional/netherlands/harmonie-eps-knmi-nl.md)).

---

## Blending / seamless transition
The system merges radar-extrapolation ensemble members with the HARMONIE-AROME Cy43 NWP ensemble so that short lead times are dominated by radar extrapolation and longer lead times transition toward the NWP ensemble — giving a "seamless" 0–6 h probabilistic forecast. The specific scale/lead-time weighting and crossover window are not documented in the dataset metadata (TBD; the pySTEPS blending framework provides the mechanism).

---

## What it provides
Two companion datasets, both on the 1 km RAD_NL25 grid, NetCDF (CF-1.7), one file per run, 72 time steps:

- **Ensemble members** (`seamless_precipitation_ensemble_forecast_members`): variable `precip_intensity(ens_number=20, time=72, y=765, x=700)` in **mm/h** — the full 20-member ensemble of precipitation intensity.
- **Exceedance probabilities** (`seamless_precipitation_ensemble_forecast_probabilities`): variable `exceedance_probability(threshold=6, time=72, y=765, x=700)` in **percent**, for six thresholds: **0.1, 0.3, 1, 3, 10, 30 mm/h**.

---

## Data availability
- **Is the data free?** Yes (free KNMI API key required)
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-1.7). Filenames `KNMI_PYSTEPS_BLEND_ENS_<YYYYMMDDHHMM>.nc` (members, ~66 MB) and `KNMI_PYSTEPS_BLEND_PROB_<YYYYMMDDHHMM>.nc` (probabilities, ~17 MB).
- **Update cadence:** Every 5 minutes.
- **Official download location:**
  - Members: https://dataplatform.knmi.nl/dataset/seamless-precipitation-ensemble-forecast-members-1-0
  - Probabilities: https://dataplatform.knmi.nl/dataset/seamless-precipitation-ensemble-forecast-probabilities-1-0
  - API datasets `seamless_precipitation_ensemble_forecast_members` and `…_probabilities`, version `1.0`, via the KNMI Open Data API.

---

## Notes
- **One system, two products.** The members and probabilities datasets are outputs of the same pySTEPS blend; the probabilities are derived from the 20 members.
- **Cross-links.** Combines the [KNMI radar QPE](../../../../observations/radar/netherlands/knmi-radar.md) (RTCOR-5m) with the [HARMONIE-AROME Cy43 ensemble](../../../ensemble_models/regional/netherlands/harmonie-eps-knmi-nl.md) — a rare openly-distributed seamless radar-NWP probabilistic nowcast.
- **Relationship to the deterministic nowcast.** The [KNMI Radar Nowcast](./knmi-radar-nowcast.md) is the deterministic, 0–2 h, extrapolation-only, HDF5 counterpart.
- **pySTEPS is not ML.** STEPS is a stochastic/spectral method, not a machine-learning model. (Distinct from KNMI's separate `qrf-rt-ssh` Quantile-Regression-Forest precipitation forecasts, which *are* ML-based.)
- **Retention.** Short rolling window, typical of 5-minute nowcasts.

---

## Recent version history
- **v1.0 (pilot)** — initial experimental release; stated pilot end 31 January 2026, observed still operational July 2026 (see pilot-status note).

---

## Official documentation
- KNMI Data Platform — members: https://dataplatform.knmi.nl/dataset/seamless-precipitation-ensemble-forecast-members-1-0
- KNMI Data Platform — probabilities: https://dataplatform.knmi.nl/dataset/seamless-precipitation-ensemble-forecast-probabilities-1-0
- pySTEPS documentation: https://pysteps.readthedocs.io/
- KNMI-OSS radar repository: https://gitlab.com/KNMI-OSS/radar/datasets
- KNMI Open Data API: https://developer.dataplatform.knmi.nl/open-data-api
