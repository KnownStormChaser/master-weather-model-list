# GFS-Wave (WAVEWATCH III) – NOAA/NCEP

## What this model is
GFS-Wave is the operational global ocean wave forecast component of NOAA/NCEP's Global Forecast System (GFS). It is built on the third-generation spectral wave model **WAVEWATCH III (WW3)** and predicts significant wave height, wave periods, wave directions, and partitioned wind-sea and swell components across the world's oceans.

As of GFS v16 (March 2021), the previously standalone `multi_1` deterministic wave system was retired and the wave model was unified into GFS, running one-way coupled to the GFS atmosphere. Unification raised the wind-forcing update frequency from 3 hours to 30 minutes, added ocean-current forcing from RTOFS, and extended the wave forecast from 180 h to 384 h.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global oceans, distributed as **seven output grids** — three native computational grids plus four post-processed grids.
- **Domain details (live-verified 2026-07-25, grid headers decoded from the 12 UTC run):**

  **Native computational grids:**
  - `arctic.9km` — Arctic Polar, **polar stereographic**, 1006 × 1006, ~9 km, useful coverage ~50–90°N
  - `global.0p16` — "Global Core", regular lat-lon, 2160 × 406, 0.1667° (10′), **15°S–52.5°N only** (despite the `global` label, this grid is *not* pole-to-pole)
  - `gsouth.0p25` — Southern Ocean, regular lat-lon, 1440 × 277, 0.25° (15′), 10.5°S–79.5°S

  **Post-processed grids:**
  - `global.0p25` — full global mosaic, regular lat-lon, 1440 × 721, 0.25°, **90°S–90°N** (the only truly global grid)
  - `atlocn.0p16` — Atlantic Ocean, 0.16°
  - `epacif.0p16` — Eastern Pacific, 0.16°
  - `wcoast.0p16` — U.S. West Coast, 0.16°

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** Multi-grid mosaic (three native grids tiling the globe) with four post-processed output grids
- **Core wave model:** WAVEWATCH III (WW3)
- **Horizontal resolution:** Domain-dependent — 9 km (Arctic), 0.1667° (Global Core), 0.25° (Southern Ocean / global mosaic), 0.16° (regional post-processed grids)
- **Forecast length:** 384 h (16 days)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly from f000–f120, then 3-hourly from f123–f384 (209 steps per domain; live-verified)

---

## Forcing and nesting
- **Wind forcing:** GFS atmosphere 10 m winds, coupled at **30-minute** frequency (one-way, atmosphere → wave)
- **Ice forcing:** Sea-ice concentration from the GFS/GDAS analysis
- **Current forcing:** Surface currents from **RTOFS** provided as input (one-way forcing)
- **Nested inside / parent for:** Wave component of the GFS system; the ensemble counterpart is the [GEFS](../../../ensemble_models/global/usa/gefs.md)-coupled WAVEWATCH III wave ensemble (GEFS-Wave), which replaced the earlier standalone Global Wave Ensemble

---

## Data assimilation
- **Assimilates wave observations:** No — GFS-Wave is a forced wave model with no wave data assimilation. Skill derives from the driving GFS winds and initial sea state, not from altimeter/SAR assimilation.

---

## What it provides
Gridded GRIB2 wave forecasts, each file with a `.idx` sidecar. Live-verified field inventory (`global.0p25` f000, 19 records; discipline 10, oceanographic + surface wind):

- **Surface wind:** wind speed (`WIND`), wind direction (`WDIR`), u/v components (`UGRD`, `VGRD`)
- **Combined sea state:** significant height of combined wind waves and swell (`HTSGW`)
- **Primary wave system:** mean period (`PERPW`), direction (`DIRPW`)
- **Wind-wave partition:** significant height (`WVHGT`), mean period (`WVPER`), direction (`WVDIR`)
- **Swell partitions 1–3:** significant height (`SWELL`), mean period (`SWPER`), direction (`SWDIR`)

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Distributed via NOAA Open Data Dissemination (NODD); NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (each file with a `.idx` sidecar)
- **File naming:** `gfs.YYYYMMDD/CC/wave/gridded/gfswave.tCCz.{domain}.f###.grib2` — e.g. `gfswave.t12z.global.0p25.f000.grib2`
- **Official download locations:**
  - **NOMADS** (real-time, ~10-day rolling window):
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/gfs/prod/
  - **NCEP production FTP** (ftpprd):
    https://ftpprd.ncep.noaa.gov/data/nccf/com/gfs/prod/
  - **AWS Open Data** — bucket `noaa-gfs-bdp-pds` (live-verified; wave subtree archived from 2021-01-01 to present):
    https://registry.opendata.aws/noaa-gfs-bdp-pds/
    e.g. `https://noaa-gfs-bdp-pds.s3.amazonaws.com/gfs.YYYYMMDD/CC/wave/gridded/`
  - **Microsoft Azure** — blob container `noaagfs` (live-verified; public, no SAS token needed):
    `https://noaagfs.blob.core.windows.net/gfs/gfs.YYYYMMDD/CC/wave/gridded/`
  - **Google Cloud Storage** — NODD bucket `global-forecast-system` (live-verified):
    `https://storage.googleapis.com/global-forecast-system/gfs.YYYYMMDD/CC/wave/gridded/`

The NODD cloud mirrors (AWS, Azure, GCP) carry the same production tree as NOMADS, typically with no rate limits and a deeper archive than the NOMADS rolling window.

---

## Notes
- **The Google Earth Engine `NOAA/GFS0P25` asset does *not* contain wave data.** It is a curated atmosphere-only ImageCollection (temperature, humidity, wind, precipitation, radiation, cloud). GFS-Wave on Google is available only through the raw NODD **Google Cloud Storage** bucket (`global-forecast-system`), not through Earth Engine — the two are different products and should not be conflated.
- **`global.0p16` is a mid-latitude grid, not a global one** — its coverage is 15°S–52.5°N (the native Global Core grid). For pole-to-pole global fields use `global.0p25`. This is a common point of confusion given the shared `global` prefix.
- The `wave/` directory also contains a `station/` subdirectory holding spectral bulletins and point spectra (`bull`, `spec`). These are **station/point time-series products and fall outside this repository's gridded-data scope**; only the `gridded/` subtree is cataloged here.
- **Coupling and family relationships:** GFS-Wave is one-way coupled to the GFS atmosphere and takes RTOFS currents as input. Its ensemble sibling is the [GEFS](../../../ensemble_models/global/usa/gefs.md)-coupled WAVEWATCH III ensemble. The broader GFS system is documented in the [GFS entry](../../../nwp_models/global/usa/gfs.md).
- WAVEWATCH III is developed at NOAA/NCEP and is the foundation for many national and international operational wave systems; a number of the regional WW3 entries in this repository (e.g. MET Norway, Météo-France, ItaliaMeteo) are independent configurations of the same core model.

---

## Recent version history
### GFS v16 — 22 March 2021 (wave unification)
- Retired the standalone `multi_1` deterministic wave system; wave model unified into GFS as a one-way-coupled WW3 component under `gfs.YYYYMMDD/CC/wave/`
- Wind-forcing update frequency increased from 3 h to 30 min
- RTOFS ocean-current forcing added as wave input
- Wave forecast length extended from 180 h to 384 h

### Upcoming — GFSv17 (proposed October 2026)
- NOAA's proposed GFSv17 upgrade moves toward fully coupled atmosphere–ocean–sea-ice–wave forecasting within the Unified Forecast System; see the [GFS entry](../../../nwp_models/global/usa/gfs.md#upcoming-changes) for status. Wave file paths/domains may change; re-verify against NOMADS if that upgrade goes operational.

---

## Official documentation
- GFS-Wave / WAVEWATCH III at EMC: https://polar.ncep.noaa.gov/waves/
- NOMADS product access: https://nomads.ncep.noaa.gov/
- AWS NODD registry: https://registry.opendata.aws/noaa-gfs-bdp-pds/
- Azure GFS (AIforEarthDataSets): https://microsoft.github.io/AIforEarthDataSets/data/noaa-gfs.html
