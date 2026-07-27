# Met Office NWS Ocean (FOAM-NWSO — AMM15, Atlantic Margin Model 1.5 km)

## What this model is
The Met Office NWS Ocean product is a **regional physical ocean analysis and 6-day forecast for the North-West European Shelf**, run on the **Atlantic Margin Model 1.5 km (AMM15)** domain. The science configuration is referred to as **FOAM-NWSO**. It predicts temperature, salinity, and circulation for the waters surrounding the UK at ~1.5 km resolution, produced by the Met Office Operational Marine Post-Processing Shelf-Seas suite (MaPP-SS).

AMM15 is a NEMO shelf-seas configuration with explicit tides. On the Met Office AWS Open Data distribution, the physics fields are **co-located in the same S3 bucket** as the AMM15-WW3 wave product (the wave files share the `NWS`/`amm15` naming) — the two are the physics and wave halves of the same coupled shelf-seas system.

---

## Who runs it
- **Production Unit:** UK Met Office
- **Country:** United Kingdom
- **Programme or coordinating body:** Met Office Operational Marine Post-Processing Shelf-Seas suite (MaPP-SS). Also distributed through the Copernicus Marine NWS MFC (Copernicus physics product identifier likely `NWSHELF_ANALYSISFORECAST_PHY_004_013` — **verify before asserting**).
- **Role in any larger system:** Regional downscaling of a Met Office global FOAM ocean over the NW European Shelf; two-way coupled to the AMM15-WW3 regional wave model.

---

## What area it covers
- **Coverage:** Atlantic North-West European Shelf
- **Domain bounds:** 16°W–13°E, 46°N–62.74°N (verified: lat 46.0000–62.7432, lon −16.0000–13.0000)
- **Grid dimensions:** 958 × 1240 (lon × lat) — verified from live files; identical delivery grid to the [AMM15-WW3 wave product](../../../wave_models/regional/uk/amm15-ww3-uk.md)
- **Special masked or excluded regions:** Baltic/Kattegat handling follows the shelf-seas convention (Baltic covered by Baltic MFC products). A minimum-depth floor applies in the underlying NEMO configuration (see Notes).

---

## Basic details
- **Model type:** Regional shelf-seas ocean physics; deterministic; two-way coupled to a regional wave model
- **Core ocean model:** NEMO (AMM15 configuration; NEMO v3.6 per the coupled wave-system documentation — the physics product sheet does not restate the version)
- **Sea ice model:** None (shelf-seas domain)
- **System name:** FOAM-NWSO (AMM15)
- **Horizontal resolution:** ~1.5 km — delivered on a regular grid of **0.014° lat × 0.03° lon** (≈ ¹⁄₇₄° × ¹⁄₃₃°)
- **Vertical levels:** **33 levels, 0–5000 m** (0, 3, 5, 10, 15, 20, 25, 30, 40, 50, 60, 75, 100, 125, 150, 175, 200, 225, 250, 300, 350, 400, 450, 500, 550, 600, 750, 1000, 1500, 2000, 3000, 4000, 5000 m)
- **Vertical coordinate:** Z-level on the delivered grid (native AMM15 uses terrain-following/hybrid coordinates before interpolation — confirm native coordinate if documenting the raw model)
- **Forecast length:** ~6-day forecast plus preceding analysis/hindcast (daily fields span T-36 → T+156 h; hourly/instantaneous span T-47 → T+168 h; quarter-hourly to T+168.75 h)
- **Update frequency:** Once daily
- **Production cycles:** 00Z (single `T0000Z` folder per day on AWS)
- **Target delivery time:** ~08:30 UTC (typical daily delivery)
- **Temporal output resolution:** Daily mean (25-hour mean for daily products), hourly instantaneous, and quarter-hourly (15-min) instantaneous for surface currents and SSH
- **Archive availability:** 2-year rolling archive on AWS Open Data
- **Bathymetry source:** AMM15 bathymetry (EMODnet-derived; confirm exact version)

---

## Forcing
- **Atmospheric forcing:** Met Office Unified Model (MetUM) surface fields (one-way)
- **River runoff:** Shelf-seas runoff dataset (climatological/daily; TBD — exact source not in product sheet)
- **Lateral boundary conditions:** From a Met Office global FOAM ocean (parent configuration — relationship to FOAM-GC worth confirming)
- **Tidal forcing:** Explicit tides — evidenced by barotropic velocity outputs (`ubar`, `vbar`) and the 15-min SSH/surface-current products that resolve the tidal signal
- **Ice forcing or coupling:** N/A
- **Initial conditions:** FOAM/NEMOVAR data assimilation on the shelf domain (assimilated observation set for the physics not detailed in the product sheet; TBD)

---

## Coupling
- **Two-way wave coupling:** the AMM15 physics is coupled online (OASIS3-MCT) to the **[AMM15-WW3](../../../wave_models/regional/uk/amm15-ww3-uk.md)** regional wave model — the wave model receives surface currents/sea level from this physics, and returns wave effects. The physics and wave products are distributed together (same AWS bucket).

---

## What it provides
Variable names (NetCDF), verified from live files. **Note the AWS distribution carries more variables than the 2023 product sheet lists** (marked † below).

### Daily-mean (25-hour mean)
- `thetao` — potential temperature [°C] (3D, 33 levels) — filetype `TEM`
- `t0` — **in-situ** temperature [°C] (3D) — filetype `TEMPIS` †
- `so` — salinity [PSU] (3D) — `SAL`
- `uo`, `vo`, `wo` — eastward/northward/upward current [m s⁻¹]; `ubar`, `vbar` — barotropic currents — `CUR`
- `bottomT` — bottom potential temperature [°C] — `BED`
- `mlotst` — mixed-layer thickness [m] — `MLD`

### Hourly instantaneous
- `TEM`/`SST`/`BED` (potential temperature: full column / surface / bottom), `SAL`/`SSS` (salinity: full column / surface), `CUR`/`SSC` (currents: full column / surface), `SSH` (`zos`), `MLD`
- `uomax` (`MAXCURU`), `vomax` (`MAXCURV`) — max current velocity in the water column [m s⁻¹] †
- `zomax` (`ZMAXCUR`) — depth of max current in the water column [m] †

### Quarter-hourly (15-min) instantaneous
- `SSC` (surface currents `uo`, `vo`), `SSH` (`zos`) — surface only (96 timesteps/day, verified)

### Static file
- Land-sea mask, depth, bathymetry (shared with the AMM15-WW3 wave product)

---

## Data availability
- **Is the data free?** Yes — anonymous S3 access, no registration (AWS distribution). The Copernicus Marine distribution requires free registration.
- **License:** **CC BY-SA 4.0** (British Crown copyright 2025, Met Office) on the AWS distribution. Share-alike obligation applies (derivatives under the same licence) in addition to attribution. *(The Copernicus Marine copy carries the Copernicus Marine Service licence instead.)*
- **Is the data downloadable?** Yes — direct HTTPS/S3 (AWS); SFTP/FTP pull (Copernicus/UKMCAS channels)
- **Data formats:** NetCDF-4; **CF-1.8 and ACDD-1.3 conventions** (verified from live files)
- **Product identifier:** Met Office AMM15 NWS physics (`metoffice_foam1_amm15_NWS`); AWS registry: *Met Office NWS Ocean model on a 2-year rolling archive*. Copernicus counterpart: `NWSHELF_ANALYSISFORECAST_PHY_004_013` (verify).
- **Dataset identifiers (per-variable files):** `TEM`, `TEMPIS`†, `SAL`, `CUR`, `BED`, `MLD`, `SST`, `SSS`, `SSC`, `SSH`, `MAXCURU`†, `MAXCURV`†, `ZMAXCUR`†. Frequencies: `dm` (daily mean), `hi` (hourly instant), `qh` (quarter-hourly instant). Standard variables carry 9 validity-day files per bulletin (2 analysis + 7 forecast); the max-current diagnostics and in-situ temperature appear with fewer files (5–6), suggesting forecast-focused coverage — **exact leadtime coverage TBD**.
- **File naming:** `metoffice_foam1_amm15_NWS_{VARIABLE}_b{YYYYMMDD}_{FREQ}{YYYYMMDD}.nc`, where the first date is the bulletin date and the second the validity date; `FREQ` ∈ {`dm`, `hi`, `qh`}. Bucket path: `nws-ocean/{YYYY}/{MM}/{DD}/T0000Z/`. The **AMM15-WW3 wave files** (`level1_wave_amm15_NWS_WAV_b..._hi....nc`) reside in the same folder.
- **File size:** ~12 MB per hourly 2D surface file (e.g. SSH hourly, 24×1240×958); larger for 3D and quarter-hourly (96 timesteps)
- **Official access:**
  - AWS registry: https://registry.opendata.aws/met-office-nws-ocean/
  - Bucket: `s3://met-office-nws-ocean-model-data/` (region `eu-west-2`)
  - Browse: https://met-office-nws-ocean-model-data.s3.eu-west-2.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://met-office-nws-ocean-model-data/`
  - AMM15 product sheet: https://www.metoffice.gov.uk/binaries/content/assets/metofficegovuk/pdf/data/amm15-data-product-sheet.pdf
- **DOI:** TBD (none on the registry)
- **Delivery mechanism:** AWS Open Data (S3, anonymous); also SFTP/FTP pull to UKMCAS/Copernicus users. New-object notifications via SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-nws-ocean-model-data-object_created`.

---

## Version history

### 2025 — AWS Open Data distribution (CC BY-SA 4.0)
- NWS Ocean physics published on AWS as a 2-year rolling archive; carries additional diagnostics beyond the earlier UKMCAS/FTP product sheet (in-situ temperature `TEMPIS`; water-column max-current `MAXCURU`/`MAXCURV`/`ZMAXCUR`).

### 2023 — AMM15 product sheet (UKMCAS/FTP distribution)
- Documented AMM15 NWS physics delivered via FTP to UKMCAS by the MaPP-SS suite (Crown Copyright 2023). Variable set: TEM, SAL, CUR, BED, MLD, SST, SSS, SSC, SSH.

*(AMM15 replaced the earlier 7 km AMM7 shelf configuration; document AMM7 lineage separately if needed. Figure lineage: Tonani et al., 2019.)*

---

## Relationship to other ocean products

### Companion products from same operator
- **[AMM15-WW3 (NWS wave)](../../../wave_models/regional/uk/amm15-ww3-uk.md)** — the wave counterpart on the **same AMM15 domain and delivery grid**, **two-way coupled** to this physics, and **distributed in the same AWS bucket**. The strongest companion link in the repository.
- **[Met Office Global Ocean / FOAM-GC](../../global/uk/foam-gc.md)** — global parent lineage (provides the ocean context/boundary conditions for the shelf downscaling; exact parent configuration to confirm).

### Regional peers / overlapping coverage
- Overlaps the Copernicus **IBI** domain (Bay of Biscay, Celtic Sea, western Channel); see the overlap discussion in the [AMM15-WW3 entry](../../../wave_models/regional/uk/amm15-ww3-uk.md).

### Parent global product
- A Met Office global FOAM ocean (see FOAM-GC cross-reference above).

### AI-based counterparts
- TBD.

---

## Notes
- **Undocumented-on-sheet variables:** the AWS distribution adds in-situ temperature (`TEMPIS`/`t0`, distinct from potential temperature `thetao`) and water-column maximum-current diagnostics (`MAXCURU`/`uomax`, `MAXCURV`/`vomax`, `ZMAXCUR`/`zomax` — the depth at which the max current occurs). These are absent from the 2023 product sheet and were confirmed only by inspecting live files.
- **Shared bucket with waves:** physics and AMM15-WW3 wave files sit in the same `nws-ocean/.../T0000Z/` folder; filter on the `metoffice_foam1_` vs `level1_wave_` prefix.
- **Time coordinate:** `seconds since 1970-01-01T00:00:00Z` (note: differs from the Global Ocean product's 1900 epoch). Hourly files carry 24 steps/day; quarter-hourly carry 96.
- **Minimum-depth floor:** the underlying NEMO v3.6 AMM15 configuration imposes a minimum depth (~10 m in the coupled wave-ocean setup) because v3.6 lacks wetting-and-drying; fields in very shallow areas (e.g. Wadden Sea) are unreliable. See the AMM15-WW3 entry for the full caveat.

---

## Official documentation
- AMM15 data product sheet (NWS-Ocean / FOAM-NWSO / AMM15), Met Office, Crown Copyright 2023.
- AWS registry: https://registry.opendata.aws/met-office-nws-ocean/
- Figure/domain reference: Tonani et al. (2019).
