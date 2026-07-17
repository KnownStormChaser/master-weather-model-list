# GEFS-Aerosols (GEFS-Chem)

## What this model is
GEFS-Aerosols is NOAA/NCEP's operational **global aerosol forecast** — the atmospheric-composition component of the Global Ensemble Forecast System (GEFS). It is the "GFS-CHEM" coupled application: the FV3-based GFS (v15 physics) coupled via NUOPC to **GSDCHEM**, a chemistry component built on the WRF-Chem `chem_driver` and configured for NASA **GOCART**-simple aerosols. It forecasts the global distribution of five aerosol families (dust, sea salt, sulfate, black carbon, organic carbon) plus derived column optical properties and surface particulate matter. It is distributed publicly as the `chem` stream of the GEFS suite — hence "GEFS-Chem." GSDCHEM replaced the earlier NEMS GFS Aerosol Component (NGAC, 1×1°). Primary uses are air-quality guidance, long-range smoke and dust transport, and aerosol-optical (visibility / solar-resource) applications.

GEFS itself is a 21-member weather ensemble to 16 days, but the aerosol component is distributed as a **single aerosol forecast** (no per-member spread) to 5 days.

---

## Who runs it
- **Organization:** NOAA / National Weather Service — NCEP/EMC; GSDCHEM developed with NOAA GSL, built on WRF-Chem/GOCART heritage (NASA GSFC)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Distributed on regular lat/lon grids — 0.5° for the 3D speciated fields, 0.25° for the 2D column/surface fields (verified from files)

---

## Basic details
- **Model type:** Global atmospheric composition / aerosol forecast (online-coupled)
- **Model system / core:** FV3 (GFS v15 physics) coupled via NUOPC to **GSDCHEM** (WRF-Chem `chem_driver`, GOCART-simple aerosols) (NOMADS description)
- **Horizontal resolution:** ~25 km native (FV3 cubed-sphere **C384**). Published grids: 0.5° (3D product) and 0.25° (2D product) — verified from files
- **Vertical levels:** 64 hybrid levels (C384**L64**; verified from the 3D files)
- **Model top:** ~0.27 hPa (GEFSv12 L64; not stated in the NOMADS description)
- **Forecast length:** 120 hours (5 days) — verified (f000–f120)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) — verified and confirmed by the NOMADS description
- **Temporal output resolution:** 3-hourly — verified

---

## Meteorological driver
- **Driving NWP model:** GEFS (FV3, GFS v15 physics) — aerosols computed inline within the same integration
- **Coupling:** Online (NUOPC-coupled GFS + GSDCHEM; aerosol–radiation interaction included)
- **Tracer transport:** Advected by the dynamics plus GFS physics (PBL scheme and Simple Arakawa-Schubert deep/shallow convection); subgrid wet scavenging handled within the SAS routines (NOMADS description)

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** Simple sulfur chemistry only (SO2 → sulfate via DMS/SO2/MSA precursors); no full gas-phase photochemistry (no prognostic O3/NOx/CO) (NOMADS description)
- **Aerosol treatment:** Bulk, GOCART-simple — ~20 species carried internally (including the sulfur gas-phase precursors); the **15 dry-mass aerosol tracers** below are the ones distributed in the 3D product (verified)
- **Aerosol components represented (verified from 3D inventory):**
  - Dust — 5 size bins (0.2–2, 2–3.6, 3.6–6, 6–12, 12–20 µm), FENGSHA scheme
  - Sea salt — 5 size bins (0.06–0.2, 0.2–1, 1–3, 3–10, 10–20 µm)
  - Sulfate — 1 mode
  - Black carbon — hydrophobic + hydrophilic
  - Organic carbon (particulate organic matter) — hydrophobic + hydrophilic
- **Plume rise:** 1-D cloud model (Freitas 2012) for wildfire smoke injection

---

## Emissions
- **Anthropogenic inventory:** CEDS (Community Emissions Data System), 2014 inventory (NOMADS description)
- **Biogenic emissions:** Not stated in the NOMADS description (TBD)
- **Wildfire emissions:** GBBEPx (NESDIS Global Biomass Burning Emissions Product), Fire-Radiative-Power-based, with Freitas (2012) plume rise (NOMADS description)
- **Dust scheme:** FENGSHA, online, 5-bin (NOMADS description)
- **Sea salt scheme:** Online, 5-bin (NOMADS description)
- **Other sources:** Volcanic ash/SO2 optionally included (NOMADS description)

---

## What it provides
All fields below verified from the live GRIB2 inventory (17 Jul 2026, 12z).

- **3D speciated aerosol fields** (`a3d`, 0.5°, 64 hybrid levels): dry mass mixing ratios of the 15 GOCART tracers (dust ×5 bins, sea salt ×5 bins, sulfate, black carbon ×2, organic carbon ×2)
- **2D column optical properties** (`a2d`, 0.25°, whole-atmosphere): aerosol optical thickness / AOD (`AOTK`), scattering AOD (`SCTAOTK`), single-scattering albedo (`SSALBK`), asymmetry factor (`ASYSFK`), and column mass density (`COLMD`) — each as total aerosol plus per-species (dust, sea salt, sulfate, black carbon, organic carbon)
- **2D surface particulate matter** (`a2d`, 0.25°): fine PM (`PMTF`) and coarse PM (`PMTC`) at the surface, as total plus dust/sea-salt contributions

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work), disseminated openly via NOAA Open Data Dissemination (NODD). Attribution requested; no endorsement or affiliation may be implied, and modified data must not be presented as unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, each file paired with a `.idx` index for byte-range subsetting
- **Official download locations:**
  - **NOMADS (real-time, rolling ~4-day window):** https://nomads.ncep.noaa.gov/pub/data/nccf/com/gens/prod/ → `gefs.YYYYMMDD/CC/chem/{pgrb2ap5,pgrb2ap25}/gefs.chem.tCCz.{a3d_0p50,a2d_0p25}.fHHH.grib2`
  - **AWS Open Data (real-time + archive):** `s3://noaa-gefs-pds/` (region `us-east-1`; browse: https://noaa-gefs-pds.s3.amazonaws.com/index.html; `aws s3 ls --no-sign-request s3://noaa-gefs-pds/gefs.YYYYMMDD/CC/chem/`). New-object notifications: SNS `arn:aws:sns:us-east-1:123901341784:NewGEFSObject`. Registry: https://registry.opendata.aws/noaa-gefs/
  - **Google Cloud (NODD mirror):** https://console.cloud.google.com/marketplace/product/noaa-public/gfs-ensemble-forecast-system
  - **Microsoft Azure / Planetary Computer (NODD mirror):** https://planetarycomputer.microsoft.com/dataset/storage/noaa-gefs

---

## Notes
- **Naming.** Official NOAA name: **GEFS-Aerosols**; the coupled application is **GFS-CHEM** (GFS v15 + GSDCHEM), and data are distributed under the `chem` product group (`gefs.chem.*`) — hence "GEFS-Chem." GSDCHEM is the NUOPC chemistry component that replaced NGAC. This entry documents the aerosol/composition component only, not the 21-member meteorological ensemble.
- **Species count.** The model carries ~20 GOCART-simple species internally (including sulfur gas-phase precursors); the public **3D product distributes the 15 aerosol dry-mass tracers**. Don't read the "20 species" figure as the number of distributed 3D fields.
- **Single aerosol stream.** Unlike the meteorological GEFS, the aerosol output is a single forecast with no per-member files (verified), extending to 120 h rather than the atmosphere's 384 h.
- **Not a full-chemistry model.** GOCART-simple forecasts aerosols and their optics but no prognostic gas-phase pollutants (O3, NOx, CO). For gridded O3/PM AQI over the U.S., see AQM; GEFS-Aerosols is the global aerosol/PM and AOD product.
- **Two grids, two dimensionalities.** The 3D speciated fields are the coarser 0.5° product (`pgrb2ap5`); the 2D column-optics and surface-PM fields are the finer 0.25° product (`pgrb2ap25`).

---

## Recent version history
- GSDCHEM/GEFS-Aerosols replaced the earlier NEMS GFS Aerosol Component (NGAC, 1×1°) and became operational with the **GEFSv12** upgrade (September 2020). (Confirm exact date/version against the EMC change log.)

---

## Official documentation
- AWS Open Data registry: https://registry.opendata.aws/noaa-gefs/
- NOAA GEFS (EMC) page: https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs.php
- AWS open-data docs (bucket layout): https://github.com/awslabs/open-data-docs/tree/main/docs/noaa/noaa-gefs-pds
