# CIP (Current Icing Product)

## What this model is
The Current Icing Product (CIP) is an NCEP / Aviation Weather Center in-flight icing diagnosis for the United States. Every hour it combines short-range NWP output with observations (radar, satellite, METARs, PIREPs, lightning) to produce a three-dimensional, near-real-time analysis of icing probability, icing severity, and supercooled large droplet (SLD) potential. It is a **diagnosis of current conditions, not a forecast**: each file is a single analysis valid at its cycle time, with no forward lead times. The algorithm was developed at NCAR under FAA sponsorship (originally "Current Icing Potential"; Bernstein et al., 2005).

CIP version 2 (CIP2) replaces the 13 km RAP-based CIP1 with a 3 km HRRR-based configuration and, for the first time, publishes the 3 km GRIB2 output on NOMADS.

---

## Who runs it
- **Organization:** NOAA / National Weather Service — NCEP Central Operations (production and dataflow), Aviation Weather Center (product responsibility); algorithm developed by NCAR/RAL under the FAA Aviation Weather Research Program
- **Country / region:** United States

---

## What area it covers
- **Coverage:** CONUS and adjacent coastal waters (per SCN 26-87)
- **Domain details:** Published on the 3 km HRRR CONUS grid — Lambert conformal, 1799 × 1059 points, 3000 m spacing, LoV 262.5°E, standard parallels 38.5°/38.5°, first grid point 21.138°N / 237.281°E, spherical Earth (shapeOfTheEarth 6, R = 6,371,229 m). Verified from decoded GRIB2 (cip.20260923 t21z). Icing values are populated across the full HRRR grid, including southern Canada, northern Mexico, and offshore waters, not just CONUS; the observational inputs (MRMS, METARs, PIREPs) are CONUS-centric, so how much the diagnosis outside CONUS relies on the HRRR background alone is TBD.

---

## Basic details
- **System type:** Nowcasting (near-real-time diagnosis / analysis)
- **Nowcasting method:** Seamless NWP–observation blend — a short-range NWP background combined with observations into a current-conditions analysis
- **Technique / algorithm:** NCAR CIP algorithm — decision-tree and fuzzy-logic combination of model-derived fields (temperature, humidity, liquid water, precipitation type) with observational cloud, precipitation, and icing evidence (Bernstein et al., 2005). CIP2 uses HRRR Liquid Water Content in place of Total Water Content.
- **Underlying / driving model:** [HRRR](../../../nwp_models/regional/usa/hrrr.md) 3 km (CIP2); [RAP](../../../nwp_models/regional/usa/rap.md) 13 km (CIP1). Whether CIP2 ingests the HRRR analysis or a 1 h HRRR forecast is not stated in the SCN (TBD).
- **Probabilistic / ensemble:** No ensemble. Icing probability is a probability field produced by the algorithm, not an ensemble-derived quantity.
- **Horizontal resolution:** 3 km (NOMADS); 13 km CIP2 grid on SBN only (see Notes)
- **Vertical structure:** 3D — 60 levels every 500 ft from 500 ft to 30,000 ft. Encoded as GRIB2 fixed surface type 102 (specific altitude above mean sea level), 152.4 m to 9144 m — i.e. heights above MSL, not pressure-altitude flight levels.
- **Lead time:** 0 h (diagnosis only; `forecastTime` 0, `typeOfProcessedData` = analysis)
- **Update frequency:** Hourly (24 cycles/day, t00z–t23z)
- **Temporal output resolution:** One analysis per hour
- **Latency:** ~10–11 minutes after the nominal cycle hour (observed from NOMADS last-modified times on 2026-09-23; t02z was an outlier at ~42 minutes)

---

## Inputs
- **Radar:** MRMS reflectivity
- **Satellite:** GOES geostationary imager channels — five channels in CIP1; CIP2 adds GOES-19 ABI band 5 (1.6 µm snow/ice) and band 6 (2.2 µm cloud particle size)
- **Lightning:** GOES Geostationary Lightning Mapper (GLM) in CIP2, replacing Vaisala National Lightning Detection Network (NLDN) data used by CIP1
- **Surface / other observations:** METARs; pilot reports (PIREPs)
- **NWP fields:** [HRRR](../../../nwp_models/regional/usa/hrrr.md) 3 km in CIP2 (13 km RAP in CIP1), including Liquid Water Content (replacing Total Water Content)

---

## What it provides
Hourly 3D in-flight icing diagnosis on 60 altitude levels (all three fields at every level; 180 GRIB2 messages per file):
- **Icing probability** — GRIB2 discipline 0, category 19, parameter 233 (`ICPRB`); fraction 0–1
- **Icing severity** — category 19, parameter 37 (`ICESEV`); categorical per GRIB2 Code Table 4.228: none, trace, light, moderate, severe (observed values 0–4)
- **Supercooled large droplet (SLD) potential** — category 19, parameter 217 (`SIPD`); uncalibrated index 0–1, not a probability and unitless, with higher values indicating greater SLD potential

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, JPEG2000 packing (Data Representation Template 5.40). One file per cycle, no `.idx` sidecar (a request for `.grib2.idx` returns 404), so byte-range subsetting requires building your own inventory. Observed file sizes 38–48 MB per cycle in late September 2026; SCN 26-87 gives ~60 MB/cycle, varying through the year.
- **Official download location:**
  - During the 30-day parallel: https://nomads.ncep.noaa.gov/pub/data/nccf/com/cip/para/
  - After implementation: https://nomads.ncep.noaa.gov/pub/data/nccf/com/cip/prod/ (returns 403 as of 2026-09-23; not yet populated)
- **File naming:** `cip.YYYYMMDD/cip.tHHz.3km.grib2`, where `HH` is the cycle hour (00–23)
- **Access route notes:** NOMADS is the only open channel for the 3 km product; no AWS Open Data mirror has been announced. The parallel directory held two date directories (`cip.20260922/`, `cip.20260923/`) on 2026-09-23; whether that reflects a two-day rolling retention or just the start of the parallel is TBD.

---

## Notes
- **Scope: a diagnosis, filed as a nowcast.** CIP produces no forecast lead times. It is catalogued here on the same basis as [GTGN](./gtgn.md): a near-real-time, three-dimensional aviation-hazard analysis built by blending a short-range HRRR-based background with observations.
- **Relationship to other entries:**
  - **[HRRR](../../../nwp_models/regional/usa/hrrr.md):** NWP background for CIP2 (RAP for CIP1).
  - **[DAFS](../../../nwp_models/regional/usa/dafs.md):** Carries the forecast-side icing guidance (IFI v2.0, F001–F018) on the same 3 km HRRR grid with the same three quantities. CIP is the current-conditions counterpart. Both upgrades made the same LWC-for-TWC change. DAFS NOMADS files use Complex3 packing with `.idx` sidecars; CIP2 NOMADS files use JPEG2000 with no sidecar.
  - **[GTGN](./gtgn.md):** The turbulence analogue — a 3 km HRRR-based aviation-hazard nowcast from the same AWC/NCAR lineage.
- **First appearance on NOMADS.** CIP1 was never distributed on NOMADS. Its gridded output went out over the SBN/NOAAPort and NWS TGFTP (`sn.*.bin` under `SL.us008001/DC.avspt/DS.cip20/PT.grid_DF.gr2/`), alongside AWC web imagery. SCN 26-87 describes the 3 km NOMADS files as newly added. The CIP1 TGFTP paths are removed at CIP2 implementation.
- **13 km CIP2 on SBN only.** A 13 km CIP2 grid replaces CIP1 on the SBN, with the WMO header site ID changing from KWBC to KWBB, Complex3 packing with no bitmap, and icing severity renumbered from parameter 234 to 37 (the "heavy" category becomes "severe"). This entry covers the 3 km NOMADS product. Whether the 13 km CIP2 grid will also appear on TGFTP is not stated (TBD).
- **Bitmap is a below-terrain mask, not a land mask.** The SCN says "bitmap over land mask." The decoded file shows the lowest 25 levels (500–12,500 ft MSL) carry a bitmap that masks points below HRRR terrain: ~69% of the grid is masked at 500 ft, but ocean points stay valid, and only ~100 points are masked at 12,000–12,500 ft. Levels from 13,000 ft upward carry no bitmap.
- **Decoder quirk.** ecCodes 2.49.0 decodes `SIPD` by name but reports `ICPRB` (19/233) and `ICESEV` (19/37) as `unknown`. The file declares GRIB2 master tables version 2 (local tables version 1), which likely predates Code Table 4.228 being defined. Consumers should key on category/parameter numbers rather than short names.
- **GRIB2 identification (observed):** centre KWBC, subcentre 3, generatingProcessIdentifier 191, Product Definition Template 4.0.
- **Size discrepancy.** The SCN states ~60 MB/cycle; the parallel files observed on 2026-09-22/23 were 38–48 MB. The SCN notes that size varies through the year, and icing coverage is seasonally low in September.

---

## Recent version history

### CIP2 — planned operational October 22, 2026, 1200 UTC (SCN 26-87)
30-day parallel on NOMADS `com/cip/para/` from ~September 22, 2026. Implementation slips to the next eligible weekday if October 22 is declared a Critical Weather Day or Enhanced Caution Event. Changes from CIP1:
- 3 km HRRR replaces 13 km RAP as the NWP background.
- Adds GOES-19 ABI bands 5 (snow/ice) and 6 (cloud particle size).
- HRRR Liquid Water Content replaces Total Water Content.
- GLM lightning replaces NLDN (Vaisala).
- New 3 km GRIB2 on NOMADS (60 levels, JPEG2000). A 13 km CIP2 grid replaces CIP1 on the SBN (KWBB header, Complex3, ICESEV parameter 37). CIP1 TGFTP `sn.*.bin` files are removed.

### CIP1 — operational since 2002
13 km RAP-based CIP (earlier RUC-based; developed from NCAR's IIDA algorithm), distributed via SBN/NOAAPort, NWS TGFTP, and AWC web imagery.

---

## Official documentation
- NWS Service Change Notice 26-87 (CIP2 upgrade): https://www.weather.gov/media/notification/pdf_2026/scb26-87_Upgrade_Current_Icing_Product.pdf
- NOMADS CIP parallel directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/cip/para/
- NCAR/RAL — Icing Products (CIP/FIP): https://ral.ucar.edu/solutions/products/icing-products-cipfip-operational
- NCAR/RAL — Aviation icing research: https://ral.ucar.edu/aviation/icing
- Bernstein et al. (2005), *Current Icing Potential (CIP): Algorithm Description and Comparison with Aircraft Observations*, J. Appl. Meteor., 44, 969–986: https://doi.org/10.1175/JAM2246.1
