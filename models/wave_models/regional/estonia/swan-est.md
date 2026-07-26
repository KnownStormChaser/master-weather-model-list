# SWAN-EST (Estonian Nearshore Wave Model)

## What this model is
SWAN-EST is a regional nearshore wave forecasting system for Estonian coastal and inland waters, based on the third-generation spectral wave model **SWAN (Simulating WAves Nearshore)**.

SWAN solves the two-dimensional spectral wave action balance equation and is designed to compute wind-wave parameters in shallow coastal seas and inland water bodies, where depth-induced processes dominate. The Estonian Environment Agency runs a configuration (SWAN-EST) adapted to Estonian and eastern Baltic conditions.

---

## Who runs it
- **Organization:** Estonian Environment Agency (Keskkonnaagentuur), through its national weather service, the Estonian Weather Service (Riigi Ilmateenistus, ilmateenistus.ee)
- **Country / region:** Estonia
- **Note:** The portal lists Keskkonnaagentuur as both author and publisher. The model-adaptation developer is not named on the portal for SWAN specifically (the companion NEMO-EST ocean model is credited to TalTech Marine Systems Institute). The "Estonian Meteorological and Hydrological Institute" (EMHI) is a former name — it was merged into Keskkonnaagentuur in 2013.

---

## What area it covers
- **Coverage:** Estonian coastal and nearshore waters, including the Gulf of Finland, Gulf of Riga, the Väinameri (West Estonian Archipelago Sea), and the adjacent eastern Baltic Proper along the Estonian coast
- **Domain bounds (from live files):** 21.55°E – 30.35°E, 56.94°N – 60.72°N
- **Grid:** 529 (longitude) × 455 (latitude) = 240,695 points; roughly 70% of the grid is land/masked (168,085 fill-valued points)

---

## Basic details
- **Model type:** Regional deterministic wave model
- **Grid system:** Single regular latitude–longitude grid
- **Core wave model:** SWAN (Simulating WAves Nearshore), third-generation spectral
- **Horizontal resolution:** ≈0.0167° longitude × 0.0083° latitude (1/60° × 1/120°) — roughly **1 km** (near-square cells of ~0.93–0.96 km at ~59°N)
- **Forecast length:** 90 hours (91 hourly steps, t0 to t0+90 h)
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** Hourly
- **Directional convention:** Nautical (direction waves come *from*, clockwise from geographic North) — set via the `Directional_convention` global attribute

---

## Forcing and nesting
- **Wind forcing:** Not explicitly documented for SWAN-EST. The Environment Agency's atmospheric model inputs are **MEPS (MetCoOp-HARMONIE)** and **ECMWF**; the companion NEMO-EST ocean model is forced by ECMWF meteorology. The SWAN-EST wind source is therefore most likely MEPS and/or ECMWF winds, but the portal does not state this specifically. (TBD)
- **Ice forcing:** Not documented. (TBD)
- **Current forcing:** Not documented. SWAN-EST represents refraction and wave blocking by currents in its physics, but whether it is coupled to NEMO-EST currents operationally is not stated. (TBD)
- **Nested inside / parent for:** Not documented. (TBD)

---

## Physical processes represented
Per the Environment Agency's model description, SWAN-EST accounts for bottom friction and represents:
- Wave generation by wind
- Propagation of the wave field in time and space
- Depth-induced shoaling (changes in the wave profile due to changing water depth)
- Energy exchange between waves via quadruplet and triad wave–wave interactions
- Depth-induced wave breaking (breaking due to decreasing depth)
- Refraction due to bathymetry and currents
- Whitecapping (partial dissipation in deep water)
- Wave blocking by currents

---

## Data assimilation
No wave-observation assimilation is documented for SWAN-EST. (TBD)

---

## What it provides
The distributed NetCDF files contain **three output fields** (verified against live files and the portal's file-level metadata table):

| Variable | Meaning | Units | Notes |
|----------|---------|-------|-------|
| `hs` | Significant wave height | m | `standard_name = sea_surface_wave_significant_height` |
| `tps` | Peak wave period (SWAN smoothed/"relative" peak period, TPS) | s | observed range ~1–9 s |
| `thetap` | Peak wave direction | degrees (nautical) | 0–360°; observed range ~7.5–352.5° |

> **Correction vs. the portal's general blurb:** The portal's SWAN *model description* states SWAN can output the 2D wave spectrum, mean and maximum period, mean direction, and RMS near-bottom orbital velocity. Those are SWAN's general capabilities — they are **not** present in the distributed files, which contain only `hs`, `tps`, and `thetap`.

> **Portal metadata error:** The portal's file-metadata table labels `thetap` with the standard name "sea surface wave directional spread." This is inconsistent with the variable: its full 0–360° packed range and nautical direction convention identify it as **peak wave direction**, not directional spread.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC-BY 4.0) — https://creativecommons.org/licenses/by/4.0/ (attribution: cite Keskkonnaagentuur / Estonian Environment Agency)
- **Access rights:** Public ("Avalik")
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (classic / CDF-2 64-bit-offset; CF-1.5), int16-packed with `scale_factor`/`add_offset` (CF-compliant libraries unpack automatically); land mask via `_FillValue = -32768`
- **File naming:** `swan_YYYYMMDDHH.nc` (HH = 00 or 12 UTC); ~131 MB per file
- **Archive:** Rolling — approximately the most recent month of cycles is retained
- **Browsable file list (portal UI):**
  https://avaandmed.keskkonnaportaal.ee/dhs/Active/documentList.aspx?ViewId=319c5400-af7c-43cc-ba6e-94428d1dbc2a
- **Direct file-download pattern:**
  `https://avaandmed.keskkonnaportaal.ee/dhs/Active/_layouts/RM/Content.aspx?ListId=228b4970-73de-44a4-83e1-9513be360001&ID=<documentId>&fileId=1`
- **KAIA machine API (SharePoint RmApi):**
  - Metadata query (POST): `https://avaandmed.keskkonnaportaal.ee/_vti_bin/RmApi.svc/active/items/query`
  - Zipped files (POST): `https://avaandmed.keskkonnaportaal.ee/_vti_bin/RmApi.svc/active/items/zipped-files`
  - Request body filters on content type, e.g. `{"filter":{"isEqual":{"field":"$contentType","value":"<contentType>"}}}` (the portal documents `0102FB04` for the SWAT hydrology dataset; the SWAN-specific `$contentType` value is not exposed on the public view page — TBD). The SWAN records list is `ListId = 228b4970-73de-44a4-83e1-9513be360001`.

---

## Relationship to other wave models in the Baltic
SWAN-EST and [BALWAM](../finland/balwam.md) (FMI, distributed via Copernicus Marine) are complementary rather than redundant:

- **SWAN-EST** is SWAN-based (~1 km) and optimized for shallow-water nearshore processes along the Estonian coast — depth-induced breaking, bottom friction, triad interactions, and wave blocking by currents. It distributes only integrated parameters (Hs, peak period, peak direction).
- **BALWAM** is WAM-based, provides uniform ~1 nautical-mile coverage across the entire Baltic, is coupled to a full Baltic Sea ocean-physics system, and assimilates wave observations.

Users needing high-resolution nearshore wave detail in Estonian waters should look to SWAN-EST; users needing basin-wide, ocean-coupled, assimilating wave conditions should look to BALWAM.

---

## Notes
- SWAN-EST is one of two operational marine forecast systems the Environment Agency distributes as open NetCDF; the other is the [NEMO-EST](../../../ocean_models/regional/estonia/nemo-est.md) ocean model (0.5 nmi, run 1× daily, developed with TalTech Marine Systems Institute). Both are documented on the same portal page.
- The distributed output is deliberately compact: three int16-packed fields on a ~1 km grid, ~131 MB per 90-hour cycle.
- Because ~70% of grid cells are land, plotting/regridding tools should respect the `_FillValue` mask rather than treating masked cells as zero.

---

## Official documentation
- Dataset page (Ilma mudelprognoosid): https://keskkonnaportaal.ee/et/avaandmed/ilma-mudelprognoosid
- Data description & usage (Ilma mudeliandmete kirjeldus): https://keskkonnaportaal.ee/et/avaandmed/ilma-mudelprognoosid/ilma-mudeliandmete-kirjeldus
- Visualized product (significant wave height & direction): https://www.ilmateenistus.ee/meri/mereprognoosid/oluline-lainekorgus-ja-suund/
- Access rights & licensing description: https://keskkonnaportaal.ee/et/avaandmed#Juurdepsuigusetekirjeldusandmestikejuures
- SWAN model: https://swanmodel.sourceforge.io
