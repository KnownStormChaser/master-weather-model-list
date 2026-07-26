# KNMI Radar Nowcast (Netherlands — pySTEPS)

## What this model is
The KNMI Radar Nowcast is an operational radar-based precipitation nowcasting product for the Netherlands. It extrapolates the most recent radar-derived precipitation field forward in time to produce a deterministic forecast of precipitation for the next two hours at 5-minute resolution, using an operational KNMI implementation of the open-source [pySTEPS](https://pysteps.github.io) nowcasting library.

---

## Who runs it
- **Organization:** Royal Netherlands Meteorological Institute (KNMI)
- **Country / region:** Netherlands

---

## What area it covers
- **Coverage:** Netherlands (and immediate surroundings), matching the national radar composite domain
- **Domain details:** RAD_NL25 grid — 700 × 765 points, 1 km, polar-stereographic (`+proj=stere +lat_0=90 +lon_0=0 +lat_ts=60`, sphere a=6378137/b=6356752). Same grid as the [KNMI radar composites and QPE](../../../../observations/radar/netherlands/knmi-radar.md).

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Observation extrapolation (Lagrangian radar extrapolation; no NWP blending)
- **Technique / algorithm:** Operational KNMI implementation of **pySTEPS** — spectral-cascade decomposition with advection-based extrapolation of the radar precipitation field
- **Underlying / driving model:** None (pure extrapolation of observed precipitation)
- **Probabilistic / ensemble:** No (deterministic single field)
- **Horizontal resolution:** 1 km
- **Vertical structure:** 2D single-level surface precipitation
- **Lead time:** 0–2 hours (25 time steps: +0 to +120 minutes)
- **Update frequency:** Every 5 minutes
- **Temporal output resolution:** 5 minutes
- **Latency:** Files published ~2 minutes after valid time (observed from creation timestamps)

---

## Inputs
- **Radar:** Initiated from the KNMI **RTCOR-5m** product — the 5-minute real-time radar/gauge precipitation accumulation from the [KNMI national radar network](../../../../observations/radar/netherlands/knmi-radar.md) (Den Helder + Herwijnen).
- **NWP fields:** None.

---

## What it provides
- Deterministic precipitation nowcast on the 1 km RAD_NL25 grid, in KNMI HDF5, one file per run containing 25 image groups (`image1`…`image25`, +0 to +120 min).
- The stored quantity is the **precipitation sum per 5 minutes** in mm (product `RAD_NL25_COR`, parameter `PRECIP_[MM]`, calibration `mm = 0.01 · PV`, missing value 65534). **Multiply by 12** to obtain precipitation intensity in mm/h.

---

## Data availability
- **Is the data free?** Yes (free KNMI API key required)
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** KNMI HDF5 (same legacy KNMI structure as the radar composites — `geographic` / `image1…25` / `overview`; *not* ODIM). Filenames `RAD_NL25_RAC_FM_<YYYYMMDDHHMM>.h5`.
- **Update cadence:** Every 5 minutes; ~0.75 MB per file.
- **Official download location:**
  https://dataplatform.knmi.nl/dataset/radar-forecast-2-0
  - API dataset `radar_forecast`, version `2.0`, via the KNMI Open Data API (key in `Authorization` header; access identical to the other KNMI datasets).

---

## Notes
- **Observation extrapolation, not NWP.** This product simply advects the observed radar precipitation field forward; skill decays with lead time and it captures no new development or decay of systems. For the blended, probabilistic, longer-range counterpart, see the [KNMI Seamless Precipitation Ensemble Nowcast](./knmi-seamless-precip-ensemble.md).
- **Parent radar network.** Initiated from RTCOR-5m; see [KNMI Radar](../../../../observations/radar/netherlands/knmi-radar.md).
- **Retention.** Nowcast files roll over a short recent window (typical for 5-minute nowcast products).
- **pySTEPS is not ML.** The technique is a stochastic/spectral extrapolation method, not a machine-learning model.

---

## Recent version history
- **v2.0** — pySTEPS-based precipitation-sum nowcast (current). Replaced the earlier `radar-forecast-1-0`, which forecast radar **reflectivity** composites rather than precipitation.

---

## Official documentation
- KNMI Data Platform — dataset page: https://dataplatform.knmi.nl/dataset/radar-forecast-2-0
- pySTEPS documentation: https://pysteps.readthedocs.io/
- KNMI-OSS radar repository: https://gitlab.com/KNMI-OSS/radar/datasets
- KNMI Open Data API: https://developer.dataplatform.knmi.nl/open-data-api
