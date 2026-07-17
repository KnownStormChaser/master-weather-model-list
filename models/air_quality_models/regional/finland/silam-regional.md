# SILAM Regional (FMI Regional Air Quality & Pollen Forecast — Northern Europe domain)

## What this model is
This entry covers FMI's **regional (Northern-Europe) SILAM configuration** — an intermediate-resolution domain sitting between the [European](./silam-europe.md) (0.1°, ~10 km) and [hires](./silam-hires.md) (~0.8 km) setups. It is distributed through FMI's THREDDS Data Server.

The domain exposes two companion datasets: a chemical air-quality run (`silam_regional_v6_1`) and a pollen / aeroallergen run (`silam_regional_pollen_v6_1`). **At the time of writing (July 2026) the chemistry aggregation is empty** — the THREDDS FMRC returns *"cannot find any files in the collection"* — while the pollen forecast is live. Both are documented below; treat the chemistry dataset as provisionally listed, pending files reappearing.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI) — Atmospheric Composition Research / Air Quality modelling group
- **Country / region:** Finland (forecast domain: Northern Europe / Fennoscandia and the Baltic)

---

## What area it covers
- **Coverage:** Northern Europe / Fennoscandia and the Baltic
- **Domain details:** Rotated latitude–longitude, **800 × 750**, ~**0.0225°** (~2.5 km), real-world bounds approximately **2.0°W–47.6°E, 52.1°N–71.8°N** (from the live pollen dataset). The chemistry dataset's grid is unverified while its collection is empty, but is expected to match.

---

## Basic details
- **Model type:** Regional (intermediate-resolution) air quality + pollen (offline chemistry-transport)
- **Model system / core:** SILAM **v6.1**
- **Horizontal resolution:** ~0.0225° on a rotated-pole grid (~2.5 km) — between the European (~10 km) and hires (~0.8 km) domains
- **Vertical levels:** 10 staggered "thick" height layers (12.5–7725 m); model top ~7.7 km
- **Model top:** ~7.7 km
- **Forecast length:** 120 h (5 days) *(pollen)*; chemistry unverified while empty
- **Update frequency / cycles:** 1× daily (00 UTC) *(pollen)*
- **Temporal output resolution:** Hourly

---

## Meteorological driver
- **Driving NWP model:** ECMWF IFS (per the FMI SILAM factsheet for the European-scale runs) — *verify for this domain / resolution*
- **Coupling:** Offline (one-way)

---

## Chemistry and aerosols
- Same SILAM v6.1 chemistry and aerosol treatment as the European configuration — see [SILAM Europe](./silam-europe.md). Not directly verifiable here while the chemistry collection is empty.

---

## What it provides
- **Chemical air quality** (`silam_regional_v6_1`, *currently no files*): when populated, full `cnc_*` chemistry as in the [European](./silam-europe.md) and [hires](./silam-hires.md) entries (O3, NO, NO2, SO2, CO, PM2.5/PM10, speciated aerosol, dust, sea salt, fire PM).
- **Pollen and aeroallergens** (`silam_regional_pollen_v6_1`, *live*): birch, alder, grass, hazel, mugwort (with sub-source variants), olive, ragweed, plus an aphids tracer. Per-taxon airborne concentration (`cnc_POLLEN_*`), ready-to-fly amount (`Poll_Rdy2fly_*`), remaining / total seasonal pollen (`poll_left_*`, `poll_tot_m2_*`), phenology heat-sums (`heatsum_*`), bias correction (`pollen_corr_*`), plus driving meteorology (2 m temperature, humidity, 10 m wind, precipitation).

---

## Data availability
- **Is the data free?** Yes
- **License:** Presumed CC BY 4.0 (FMI open data) — **not confirmed on the THREDDS server; verify** (open access ≠ open licence)
- **Is the data downloadable?** Yes (pollen); chemistry collection currently empty
- **Data formats:** NetCDF-4 (`.nc4`)
- **Official download location:**
  - Chemistry: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_v6_1/catalog.xml — **collection empty / FMRC error at check time (July 2026)**
  - Pollen: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_pollen_v6_1/catalog.xml — live; OPeNDAP, NetCDF Subset Service, HTTPServer

---

## Notes
- **Chemistry aggregation was empty at check time** ("cannot find any files in the collection"). Recheck before relying on it; the **pollen forecast is the currently active product** for this domain.
- Intermediate resolution (~2.5 km) fills the gap between the European (~10 km) and hires (~0.8 km) domains.
- Pollen is documented here as an air-quality-relevant exposure, consistent with [SILAM Europe](./silam-europe.md) and [SILAM Hires](./silam-hires.md).
- Rotated-pole grid — reproject before combining with regular lat/lon products.

---

## Official documentation
- SILAM model site: http://silam.fmi.fi
- FMI SILAM factsheet: https://atmosphere.copernicus.eu/sites/default/files/2020-09/SILAM%20factsheet_Feb2020.pdf
- THREDDS catalogs: https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_v6_1/catalog.xml and https://thredds.silam.fmi.fi/thredds/catalog/silam_regional_pollen_v6_1/catalog.xml
- FMI open data: http://en.ilmatieteenlaitos.fi/open-data
