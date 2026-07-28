# MOLOCH-AIM (Italy – High Resolution)

## What this model is
MOLOCH-AIM is a **high-resolution, convection-permitting regional numerical weather prediction (NWP) system** based on the MOLOCH model (**MO**dello **LOC**ale in **H**ybrid coordinates), operated as an operational chain by Agenzia ItaliaMeteo (AIM).

MOLOCH is a non-hydrostatic, fully compressible model developed at CNR-ISAC (Bologna), built on the experience of the hydrostatic BOLAM model and integrating the fully compressible atmospheric equations on a rotated latitude–longitude Arakawa C-grid with a hybrid terrain-following vertical coordinate. It is designed to resolve deep convection, intense precipitation, and small-scale flow over complex terrain.

This entry documents the **AIM operational chain** distributed via MeteoHub (dataset ID `MOLOCH_AIM`). It is distinct from the **BOLAM-MOLOCH-ISAC** suite also distributed by ItaliaMeteo (the CNR-ISAC chain run under a DPC agreement: BOLAM-ISAC at ~8 km and MOLOCH-ISAC at ~1.2 km, delivered through MeteoHub's filtered extraction service rather than as a raw cycle archive). See *Notes* for the disambiguation.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo (AIM), with CNR-ISAC and the **Moloch Consortium** (attribution string on the MeteoHub dataset card: `ItaliaMeteo-CNR-ISAC-Moloch`)
- **Country / region:** Italy
- **HPC:** CINECA (ItaliaMeteo compute allocation)
- **Model core developed by:** CNR-ISAC, Bologna

> **Operator attribution — source discrepancy.** The MeteoHub dataset card credits "ItaliaMeteo, CNR-ISAC and Moloch Consortium." The MeteoHub v0.5.3 release note instead describes the chain as "developed by ItaliaMeteo, CIMA and LaMMA." These are not necessarily in conflict (the Moloch Consortium is the community around the MOLOCH model, of which CIMA and LaMMA are active members), but the exact division of roles is **not fully documented (TBD)** — worth confirming with ItaliaMeteo.

---

## What area it covers
- **Coverage:** Italy, the central Mediterranean, the Alpine arc, the western Balkans, and the Tunisian coast
- **Domain details (verified from the distributed GRIB2):**
  - Grid type: **rotated latitude–longitude**, rotated south pole at **48°S, 13°E** (domain centre ≈ **42°N, 13°E**)
  - Dimensions: **1802 (W–E) × 1666 (S–N)** = 3,002,132 points
  - Grid increment: **0.0091°** in both directions (≈ **1.01 km**)
  - **Computed geographic extent** (eccodes `latitudes`/`longitudes`, file-verified): ≈ **33.46°N – 49.31°N**, **2.09°W – 22.63°E**

---

## Basic details
- **Model type:** Deterministic NWP (convection-permitting)
- **Model system / core:** MOLOCH (CNR-ISAC)
- **Dynamical formulation:** Non-hydrostatic, fully compressible
- **Convection-allowing:** Yes — at ~1 km the model is convection-permitting/resolving. Note: MOLOCH has historically been run with convection parameterization active even at ~2 km grid spacing (found to improve results); whether any convective parameterization is active in this ~1 km AIM configuration is **not documented (TBD)**. The output includes a convective-CAPE diagnostic (`cape_con`).
- **Horizontal resolution:** **~1.0 km** (0.0091°, file-verified). **Note:** the MeteoHub dataset card and the v0.5.3 release note both state **1.5 km** — the distributed GRIB grid is finer than the advertised figure. See *Notes*.
- **Grid dimensions:** 1802 × 1666 (see *What area it covers*)
- **Vertical levels:** TBD (not documented; pressure-level output is on 9 levels — see *What it provides*)
- **Forecast length:** **72 hours** (hourly steps 0–72; 73 messages per parameter/level file, file-verified)
- **Update frequency / cycles:** The dataset card describes the **00 UTC** deterministic run. The raw `/nwp/MOLOCH_AIM/` archive additionally carries **12 UTC** cycles (e.g. `2026072112/`), so the operational chain appears to run **2× daily (00 and 12 UTC)** even though the card documents only the 00 UTC run. **Worth confirming (TBD).**
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** TBD. MOLOCH operational chains are typically run **nested in a parent model** (historically GLOBO→BOLAM→MOLOCH at CNR-ISAC) and take their initial state from the parent rather than running an independent analysis. The parent and any surface analysis for the AIM chain are **not documented (TBD)**.

---

## Initial and boundary conditions
- **Initial conditions:** TBD (parent-model driven; parent not documented for the AIM chain)
- **Boundary conditions:** TBD

---

## What it provides
Deterministic forecasts on a single ~1 km domain. Output is split into one GRIB2 file per parameter per level. Verified fields include:

**Pressure levels (1000, 975, 950, 925, 900, 850, 700, 500, 300 hPa):**
- Temperature (`t`), geopotential height (`gh`), specific humidity (`qv`), wind components (`u`, `v`), geometric vertical velocity (`wz`), pseudo-adiabatic potential temperature (`papt`)

**Near-surface / height levels:**
- 2 m temperature (`2t`), dewpoint (`2d`), relative humidity (`2r`), specific humidity (`2sh`), and 2 m min/max temperature (`tmin_2m`, `tmax_2m`)
- 10 m wind (`10u`, `10v`); winds also at 50 m and 80 m (`u`, `v`) and 100 m (`100u`, `100v`)
- Height of the 0 °C isotherm (`h`, on `isothermZero`)

**Surface / column:**
- Mean sea level pressure (`pmsl`), total precipitation (**hourly accumulation** — see *Notes*), snowfall (`asnow`), snow water equivalent (`sdwe`)
- CAPE (`cape_con`) and convective inhibition (CIN — see *Notes*)
- Cloud cover: total (`clct`), high/medium/low (`hcc`/`mcc`/`lcc`)
- Wind gust (`gust`), lapse rate (`lapr`)
- Downward shortwave radiation (`dswrf`, time-mean), instantaneous sensible and latent heat fluxes (`ishf`, `lhtfl`)
- Ground temperature (`t_g`)

**Soil / static:**
- Soil temperature (`st`) and volumetric soil moisture (`soilw`) on a 10-layer column (≈ 0.05, 0.69, 2.21, 3.48, 5.17, 7.34, 10.05, ~13, 17.33, 22.02 m)
- Land–sea mask (`lsm`), model terrain height (`mterh`)

---

## Data availability
- **Is the data free?** Yes
- **License:** **CC BY 4.0** (attribution required). Confirmed on the MeteoHub dataset card (`License: CCBY4.0`; attribution `ItaliaMeteo-CNR-ISAC-Moloch`). See https://meteohub.agenziaitaliameteo.it/app/license
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2; originating centre `cnmc` / Rome, subCentre 102)
- **Official download location (raw cycle archive):**
  - https://meteohub.agenziaitaliameteo.it/nwp/MOLOCH_AIM/
  - Filtered/custom subsets: MeteoHub "data extraction" service (via the dataset card's *Go to data extraction to customise data* link)

Cycle directories follow the `YYYYMMDDHH/` format (e.g. `2026072112/`). Each cycle contains one subdirectory per parameter, and each parameter/level is a single GRIB2 file holding all 73 hourly steps. Files are large: each pressure-level file is ~438 MB; the combined surface file (`unknown/…_surface-0.grib`) is ~870 MB.

---

## Notes
- **Resolution: card vs. reality.** The MeteoHub card and release note advertise **1.5 km**, but the distributed GRIB grid is **0.0091° ≈ 1.0 km** (internally consistent: the E–W span `(6.358101 − (−10.031)) / 1801 = 0.009100°`). The finer ~1 km figure is the file-verified one; the 1.5 km label may be stale or approximate.
- **Precipitation and CIN hide in an `unknown/` directory.** Two fields fail eccodes' `shortName` resolution, so the producer's split-by-shortName tooling files both under `unknown/MOLOCH_AIM_*_surface-0.grib`:
  - **Total precipitation** — `discipline=0, parameterCategory=1, parameterNumber=8`, **hourly accumulation** (`typeOfStatisticalProcessing=1`, `lengthOfTimeRange=1`; verified: `startStep/endStep` advance 0→1, 1→2, …), *not* a run-total.
  - **Convective inhibition (CIN)** — `0-7-7`, instantaneous (pairs with the separate `cape_con` = `0-7-6`).
  - Snowfall (`asnow` = `0-1-29`) also has an unresolved `shortName`.
  - **Practical guidance:** key on `discipline`/`parameterCategory`/`parameterNumber` (WMO GRIB2 Code Table 4.2), not on `shortName`, when reading these fields.
- **Missing-value sentinels.** CIN fields use **−999** as fill with **no bitmap** (`bitmapPresent=0`); the precipitation messages declare `missingValue=9999`. Naive readers will average sentinels into their fields if not masked.
- **No parameter index file.** Unlike ICON-2I (which ships `ICON_2I_shortname_parameter_index.txt`), the `MOLOCH_AIM/` root has no published shortname/parameter index; the parameter list above was compiled by live GRIB inspection.
- **Rolling archive with gaps.** The raw archive holds roughly the previous ~6 days of cycles, and not every scheduled cycle is always present (observed gaps in the mid-July 2026 listing). Users needing continuity should download cycles as produced.
- **Disambiguation from MOLOCH-ISAC.** This is the **AIM** chain (raw open cycle archive under `/nwp/MOLOCH_AIM/`, CC BY 4.0). The separate **BOLAM-MOLOCH-ISAC** suite (CNR-ISAC, distributed via MeteoHub's extraction service under the CKAN dataset `previsioni-meteorologiche-modelli-bolam-moloch-globo-di-isac-cnr`, licensed CC BY-SA) is a different chain with a different domain, resolution, and licence. If MOLOCH-ISAC is later added, keep the entries and filenames distinct.
- **Ecosystem.** Runs on the same MeteoHub/CINECA infrastructure as [ICON-2I](./icon-2i.md), and shares the server with the `WRF/` (likely the LaMMA chain) and `SEASONAL/` datasets, which are separate candidates.

---

## Recent version history
- **2025 — MOLOCH-AIM added to MeteoHub (v0.5.3):** distribution of the new ~1 km MOLOCH operational chain developed by ItaliaMeteo with CNR-ISAC / the Moloch Consortium (release note names CIMA and LaMMA), running on ItaliaMeteo's CINECA allocation.

---

## Official documentation
- MeteoHub raw archive: https://meteohub.agenziaitaliameteo.it/nwp/MOLOCH_AIM/
- MeteoHub portal: https://meteohub.agenziaitaliameteo.it/
- MeteoHub release notes (news): https://meteohub.agenziaitaliameteo.it/ui/news
- ItaliaMeteo open data catalog: https://dati.agenziaitaliameteo.it
- MOLOCH model description (CNR-ISAC): https://www.isac.cnr.it/en/numerical_models/bolam
