# SILAM Regional (FMI Regional Air Quality & Pollen Forecast — Northern Europe domain)

## What this model is
This entry covers FMI's **regional (Northern-Europe) SILAM configuration** — an intermediate-resolution domain sitting between the [European](./silam-europe.md) (0.1°, ~10 km) and [hires](./silam-hires.md) (~0.8 km) setups. It is distributed through FMI's THREDDS Data Server.

The domain exposes two companion datasets: a chemical air-quality run (`silam_regional_v6_1`) and a pollen / aeroallergen run (`silam_regional_pollen_v6_1`). Both are live and documented below. (Note: through late July 2026 the chemistry dataset returned an HTTP 500 FMRC error — *"cannot find any files in the collection"* — across all endpoints; FMI localized and fixed the server-side issue on 2026-07-20, and the collection is now accessible and verified.)

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI) — Atmospheric Composition Research / Air Quality modelling group
- **Country / region:** Finland (forecast domain: Northern Europe / Fennoscandia and the Baltic)

---

## What area it covers
- **Coverage:** Northern Europe / Fennoscandia and the Baltic
- **Domain details:** Rotated latitude–longitude, **800 × 750**, **0.0225°** (verified for both the chemistry and pollen datasets), real-world bounds **2.0°W–47.6°E, 52.1°N–71.8°N**. FMI describes this Fennoscandia + Baltic suite as **~2.4 km**, **nested in the European SILAM CAMS simulations** (per the CAMS Finland programme page).

---

## Basic details
- **Model type:** Regional (intermediate-resolution) air quality + pollen (offline chemistry-transport)
- **Model system / core:** SILAM **v6.1**
- **Horizontal resolution:** ~0.0225° on a rotated-pole grid (~2.5 km) — between the European (~10 km) and hires (~0.8 km) domains
- **Vertical levels:** 10 staggered "thick" height layers (12.5–7725 m); model top ~7.7 km
- **Model top:** ~7.7 km
- **Forecast length:** **48 h (2 days)** per run — verified for both the chemistry (48 hourly files/run) and pollen (48 time steps/run) datasets, on a four-day rolling archive
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** Hourly
- **Archive:** Four-day rolling window (per FMI; 4 daily runs present at check, 2026-07-18 → 2026-07-21)

---

## Meteorological driver
- **Driving NWP model:** **MEPS** (MetCoOp Ensemble Prediction System) — the zeroth / unperturbed (control) member, at its native 2.5 km resolution. Confirmed by FMI (M. Sofiev, July 2026).
- **Coupling:** Offline (one-way)

---

## Chemistry and aerosols
- Same SILAM v6.1 chemistry and aerosol treatment as the European configuration — see [SILAM Europe](./silam-europe.md). The chemistry dataset carries **475 output fields** on 10 height layers (verified July 2026).

---

## What it provides
- **Chemical air quality** (`silam_regional_v6_1`, *live*): full `cnc_*` chemistry (475 fields) on 10 height layers, as in the [European](./silam-europe.md) and [hires](./silam-hires.md) entries (O3, NO, NO2, SO2, CO, PM2.5/PM10, speciated aerosol, dust, sea salt, fire PM).
- **Pollen and aeroallergens** (`silam_regional_pollen_v6_1`, *live*): birch, alder, grass, hazel, mugwort (with sub-source variants), olive, ragweed, plus an aphids tracer. Per-taxon airborne concentration (`cnc_POLLEN_*`), ready-to-fly amount (`Poll_Rdy2fly_*`), remaining / total seasonal pollen (`poll_left_*`, `poll_tot_m2_*`), phenology heat-sums (`heatsum_*`), bias correction (`pollen_corr_*`), plus driving meteorology (2 m temperature, humidity, 10 m wind, precipitation).

---

## Data availability
- **Is the data free?** Yes
- **License:** **CC BY 4.0** — confirmed by FMI (M. Sofiev, July 2026) for all data released from thredds.silam.fmi.fi
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (`.nc4`)
- **Official download location:**
  - Chemistry: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_v6_1/catalog.xml — live (OPeNDAP, NetCDF Subset Service, HTTPServer); native files `SILAM-AQ-regional_v6_1_<YYYYMMDDHH>_<NNN>.nc4`
  - Pollen: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_pollen_v6_1/catalog.xml — live; OPeNDAP, NetCDF Subset Service, HTTPServer

---

## Notes
- **Chemistry access history:** Through late July 2026 the `silam_regional_v6_1` collection returned an HTTP 500 FMRC error ("cannot find any files in the collection") across all public endpoints, while the pollen twin and other domains worked. FMI localized and fixed the server-side issue on 2026-07-20; the collection is now live and verified. Retained on a four-day rolling archive.
- Intermediate resolution (~2.5 km) fills the gap between the European (~10 km) and hires (~0.8 km) domains.
- Pollen is documented here as an air-quality-relevant exposure, consistent with [SILAM Europe](./silam-europe.md) and [SILAM Hires](./silam-hires.md).
- Rotated-pole grid — reproject before combining with regular lat/lon products.

---

## Official documentation
- SILAM model site: http://silam.fmi.fi
- FMI SILAM factsheet: https://atmosphere.copernicus.eu/sites/default/files/2020-09/SILAM%20factsheet_Feb2020.pdf
- THREDDS catalogs: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_v6_1/catalog.xml and https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_pollen_v6_1/catalog.xml
- FMI open data: http://en.ilmatieteenlaitos.fi/open-data
