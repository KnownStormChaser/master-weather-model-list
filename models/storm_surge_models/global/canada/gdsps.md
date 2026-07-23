# GDSPS (Global Deterministic Storm Surge Prediction System)

## What this model is
The **Global Deterministic Storm Surge Prediction System (GDSPS)** is Environment and Climate Change Canada's operational global water level forecast system, run at the Canadian Centre for Meteorological and Environmental Prediction (CCMEP). It produces 240-hour global forecasts of **total water level** and **storm surge elevation** twice daily.

GDSPS is not a conventional 2D barotropic surge model. It is a modified configuration of the **NEMO** ocean model (Wang et al. 2021, 2022) running on the global tripolar ORCA12 grid at 1/12°, with a "light baroclinic" formulation of 9 vertical levels whose 3D temperature and salinity are nudged toward [GIOPS](../../../ocean_models/global/canada/giops.md) and [GDPS](../../../nwp_models/global/canada/gem-global.md) fields. Tides are run explicitly within the model rather than added afterward, and the storm surge field is obtained in post-processing by removing the tidal signal.

Both products are distributed as one-file-per-timestep NetCDF-4 on the MSC Datamart:
- **`SSH`** — total water level (variable `zos`), referenced to model Mean Water Level
- **`ETAS`** — storm surge elevation (variable `etas`), the non-tidal residual

The current operational version is **GDSPS 2.3.0**, implemented at the 12 UTC run on **26 May 2026**.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada (ECCC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP)
- **Country / region:** Canada (global coverage)

---

## What area it covers
- **Coverage:** Global ocean
- **Domain details:** Native model domain is the global tripolar **ORCA12** grid, 4322 × 3606 points at 1/12° (~3–9 km depending on latitude). Isolated inland lakes were removed from the bathymetry in v2.1.0.
- **Storm surge (`ETAS`) availability:** `etas` is computed **only** within the region bounded by 150°W–40°W and 40°N–80°N, plus a **100 km-wide band along the shoreline worldwide**. Everywhere else `etas` is masked. Live check on the 2026-07-22 12 UTC run: `etas` is valid at **9.6%** of grid points (215,977 of 2,253,051), spanning 85.3°S to 84.3°N — confirming the worldwide coastal band is genuinely populated in both hemispheres, not just the Canadian box. `zos` is valid at **66.8%** of points (all ocean).
- **Inundation coverage:** None. The domain stops at the coastline; no wetting/drying or overland flooding is documented or produced.

---

## Basic details
- **Model type:** Deterministic storm surge / total water level model
- **Core hydrodynamic model:** NEMO, modified for storm surge (Wang et al. 2021, 2022)
- **Dimensionality:** "Light baroclinic" — primitive equations with **9 vertical levels** optimized for efficiency, with 3D temperature and salinity nudged rather than freely evolving. Prognostic variable is water level.
- **Native horizontal resolution:** 1/12° on the ORCA12 tripolar grid
- **Time step:** 240 s
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** 1 h (forecast hours 000–240, all 241 steps distributed)

---

## Grid and bathymetry
- **Native grid type:** Structured curvilinear tripolar (ORCA12), 4322 × 3606
- **Distributed grid type:** Regular latitude–longitude, **1801 (lon) × 1251 (lat)**
- **Distributed grid spacing (live-verified):** **0.143883° latitude × 0.200000° longitude.** Latitude runs −89.9281° to +89.9281°; longitude runs 0° to 360° inclusive, meaning the first and last columns are the **same meridian duplicated**. Nominal spacing is published as 0.144 × 0.200.
- **Bathymetry source:** GEBCO_2014 (Weatherall et al. 2015) with adjustments from bathymetric data supplied by F. Lyard; isolated lakes excluded since v2.1.0
- **Wetting and drying:** No
- **Bottom stress:** Quadratic, bottom drag coefficient 2.5 × 10⁻³
- **Surface wind stress:** Quadratic with wind-dependent drag — 1.2 × 10⁻³ for W < 8 m s⁻¹, and (0.68 + 0.065 W) × 10⁻³ for W ≥ 8 m s⁻¹

---

## Vertical datum and reference level
- **Vertical datum:** Model **Mean Water Level (MWL)**, defined as the mean of the pseudo-analysis water level over the preceding **365 days** (minimum 90 days if a full year is unavailable). This is a model-internal datum, not a national or hydrographic one.
- **What the water level field is measured relative to:**
  - `zos` (SSH) = **total water level above MWL** — includes tide, surge, and the retained seasonal signal
  - `etas` (ETAS) = **non-tidal residual**, i.e. `zos` minus the harmonically-analysed tide
  - **Model tidal height is recoverable as `zos − etas`** wherever `etas` is defined
- **Datum conversion offsets provided?** No. No chart-datum, LAT, or geoid offset field is distributed. Users needing a hydrographic datum must supply their own conversion.
- **Sea level rise / steric handling:** The solar annual (SA) and solar semi-annual (SSA) constituents are deliberately **retained** in `etas` rather than removed during de-tiding, so seasonal steric and ice-driven modulation stays in the surge field. Because MWL is a trailing 365-day mean, the reference level drifts slowly with the model climate rather than being fixed to an epoch.

> **Live-verified caution — misleading CF metadata.** The `zos` variable carries `standard_name = sea_surface_height_above_geoid`, but its own `long_name` ("Sea surface height (MWL)"), its `description` attribute, and the technical specification's *Levelling* row all state the field is referenced to the 365-day model mean, **not to the geoid**. Any tooling that trusts the CF `standard_name` and applies a geoid-based interpretation will be wrong. Treat the datum as MWL.

---

## Tide handling
- **Are tides included?** Yes — tides are run explicitly inside the model, and `SSH` is a genuine total water level. `ETAS` is the de-tided residual derived in post-processing.
- **Tidal forcing source:** Depth-averaged tidal currents for the **M2, S2, N2, K2, O1, K1, P1 and Q1** constituents are nudged toward **TPXO8** (Egbert and Erofeeva 2002), in deep water south of 66°N only.
- **Separation of components:** Two of the three components are distributed directly (`SSH` = total, `ETAS` = surge residual). The **tide is not distributed as its own product** but is recoverable as `zos − etas` within the `etas` mask.
- **De-tiding method:** Harmonic analysis with **T_tide** (Pawlowicz et al. 2002) over a **366-day** window, built by concatenating the pseudo-analysis history with the current forecast. The window was extended from 180 to 366 days when baroclinicity and ice effects were added, because those introduce seasonal modulation of the tides. SA and SSA constituents are excluded from the removal.
- **Tide–surge interaction:** Modelled nonlinearly within the run; the separation is applied only afterward, in post-processing.

---

## Forcing and coupling
- **Meteorological forcing — wind:** 10 m winds (`UU`, `VV`) from [GDPS](../../../nwp_models/global/canada/gem-global.md), hourly. Pseudo-analysis uses the delayed-cutoff **G2** stream; the forecast uses **G1**.
- **Meteorological forcing — pressure:** GDPS sea level pressure (`PN`), hourly, with a **constant reference pressure of 101,000 Pa** for the inverse barometer tendency (set constant in v2.1.0 to make runs reproducible).
- **Driving NWP version:** GDPS **10.0.0** as of GDSPS 2.3.0
- **Wave contribution:** None. GDSPS is not coupled to a wave model and does not include wave setup. ECCC's wave forecasting is handled separately by GDWPS.
- **River discharge / freshwater forcing:** Not documented (TBD)
- **Ocean forcing:** 3D temperature and salinity nudged with coefficient **γ = 0.2 day⁻¹** toward 0.25° vertically-interpolated fields from **GIOPS 3.5.0** (GU analysis) and GDPS G1 24-hourly forecasts. This captures low-frequency signals with periods beyond ~15 days; higher-frequency signals generated by the 1/12° model itself are not nudged directly.
- **Ice forcing:** Ice fraction (`GL`), ice velocity (`UUI`, `VVI`) and surface currents (`UUW`, `VVW`) from GDPS G1, 3-hourly. Ice effects enter through a parameterized ice–ocean stress in which the ice-to-surface relative velocity is split into tidal and surge components, the tidal part derived via a transfer function from a three-year tide-enabled GIOPS run. **Ice effects are disabled in the Gulf of St. Lawrence.**
- **Input interpolation:** Linear in time; linear aggregation-interpolation in space

---

## Data assimilation
- **Assimilates water level observations:** **No.** GDSPS has no data assimilation system.
- **Initialization instead uses a pseudo-analysis cycle:** the model is run with delayed-cutoff atmospheric forcing four times daily, in 6-hour windows centred on 00, 06, 12 and 18 UTC. Each pseudo-analysis restarts from the previous one's restart file, making it a continuous cycle rather than a sequence of independent analyses.
- **Forecast initialization:** the 00 UTC forecast starts from the previous day's **18 UTC** pseudo-analysis; the 12 UTC forecast starts from the **06 UTC** pseudo-analysis. GDPS G1 IAU data bridges the 3-hour gap between the end of the pseudo-analysis and G1 availability.

---

## What it provides
- **Total water level** (`zos`, m, relative to MWL) — file type `SSH`
- **Storm surge elevation** (`etas`, m, non-tidal residual) — file type `ETAS`, masked outside the coastal band and Canadian box
- **Model tidal height** — not distributed directly; derive as `zos − etas`

No currents, no inundation depth, no peak-envelope product, and no station time series are published for this system. Output is gridded only.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTP)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required. Suggested attribution: "Data Source: Environment and Climate Change Canada." https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Data formats:** NetCDF-4 (HDF5 container), `Conventions = CF-1.6`, zlib level 1 with shuffle, one 2D field per file chunked whole
- **Official download location:**
  - Current day: https://dd.weather.gc.ca/today/model_gdsps/15km
  - Dated archive: `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_gdsps/15km/{HH}/`
  - where `HH` is `00` or `12`
- **File naming:** `{YYYYMMDD}T{HH}Z_MSC_GDSPS_{VAR}_Sfc_LatLon0.144x0.200_PT{hhh}H.nc`, with `VAR` in `{ETAS, SSH}` and `hhh` from `000` to `240`
- **Files per run:** 482 (241 `ETAS` + 241 `SSH`) — live-confirmed on the 2026-07-22 12 UTC run
- **File size:** `ETAS` ~730–750 KiB, `SSH` ~2.6–2.9 MiB; approximately **830 MiB per run**
- **Retention:** Dated directories persist for **30 days** (live-probed: 2026-06-23 present, 2026-06-22 returns 404)
- **Publication latency:** The full run is written in a single burst roughly **T+5h40m** after cycle time (the 2026-07-22 12 UTC run was written at 17:39 UTC, all 482 files within ~10 seconds of each other)
- **Push notification:** Available via AMQP (MSC Datamart sr3/sarracenia)
- **Other access:** The same fields are served through MSC GeoMet as WMS and WCS layers; WCS can return raw coverage subsets, though the Datamart NetCDF is the canonical raw distribution.

---

## Notes
- **The `15km` path segment is a nominal label, not the grid spacing.** The distributed grid is 0.1439° latitude (~16.0 km) by 0.2000° longitude (~22.2 km at the equator, narrowing poleward). Neither figure is 15 km.
- **The datamart documentation's grid table mixes axis orders.** It lists `ni = 1801`, `nj = 1251` (longitude then latitude) but gives resolution as "0.144° x 0.200°" (latitude then longitude). Live inspection confirms 1801 longitude points at 0.2° and 1251 latitude points at ~0.1439°, so the table is correct but the ordering is inconsistent between rows.
- **Longitude wraps with a duplicated column.** The grid includes both 0° and 360°, so column 0 and column 1800 are the same meridian. Software that assumes non-repeating longitudes may double-count at the seam.
- **Distribution grid ≠ model grid.** Users wanting native 1/12° ORCA12 output will not find it here; only the interpolated regular lat-lon product is public.
- **The published factsheet is stale.** It documents version 2.1.0 (dated 11 June 2024) while the operational system is 2.3.0. The technical specification PDF is the current document.
- **`ETAS` masking is aggressive.** Only ~10% of grid points carry a surge value. Applications outside the Canadian box need to confirm their points of interest fall inside the 100 km coastal band before relying on `etas`.
- **Relationship to other ECCC systems:**
  - Meteorological driver: [GDPS](../../../nwp_models/global/canada/gem-global.md) (10 m wind, MSLP, ice, surface currents). Note that GDPS 10.0.0 introduced AI spectral nudging toward GEML, so GDSPS 2.3.0 is indirectly driven by a partly data-driven atmospheric forecast.
  - Ocean state: [GIOPS](../../../ocean_models/global/canada/giops.md) 3.5.0 supplies the temperature and salinity fields the light-baroclinic component is nudged to.
  - Regional ensemble counterpart: **RESPS** (Regional Ensemble Storm Surge Prediction System), covering the Northwest Atlantic. GDSPS and RESPS are the deterministic/ensemble pair in ECCC's surge suite, but they are not the same system at different membership — RESPS is regional where GDSPS is global.
  - Also related: [RIOPS](../../../ocean_models/regional/canada/riops.md) for regional ocean physics, and GDWPS for global waves.
- **Verification datum differs from distribution datum.** ECCC's own factsheet verification figures reference total water level to **Chart Datum**, while the distributed product is referenced to MWL. The two are not interchangeable and no conversion is shipped with the data.

---

## Recent version history

All GDSPS versions have been implemented at the **12 UTC run** of the stated date.

### GDSPS 2.3.0 — operational 26 May 2026 (current)
Single change: atmospheric and oceanic forcing moved to **GDPS version 10.0.0**.

### GDSPS 2.2.0 — operational 14 April 2026
Adaptation to ECCC's **new High Performance Computing infrastructure**. Infrastructure-only; no scientific or configuration changes to the model, forcing, or outputs. Implemented as part of the same multi-system HPC migration that produced [RIOPS](../../../ocean_models/regional/canada/riops.md) v2.5.0 on the same date.

### GDSPS 2.1.0 — operational 11 June 2024
- Upstream forcing moved to **GDPS 9.0.0** and **GIOPS 3.5.0** (wind, atmospheric pressure, ice concentration, ice velocity, surface currents, and salinity/temperature profiles)
- Bathymetry updated to **exclude isolated lakes**, removing invalid output points
- Reference atmospheric pressure **set to a constant 101,000 Pa**, improving run reproducibility
- Net impact on water level forecast skill assessed as neutral

### GDSPS 2.0.0 — operational 27 July 2023
Introduced the **light baroclinic formulation** (9 vertical levels with 3D temperature and salinity nudged to GIOPS/GDPS) and **ice effects** via parameterized ice–ocean stress. Because baroclinicity and ice both introduce seasonal modulation of the tides, the de-tiding harmonic analysis window was extended from 180 to 366 days, and the SA and SSA constituents were retained rather than removed so that seasonal signals stay in `etas`.

### GDSPS 1.1.0 — operational 28 June 2022
Adaptation to ECCC's High Performance Computing infrastructure of the time. Infrastructure-only; no scientific changes.

### GDSPS 1.0.0 — operational 1 December 2021
Initial operational implementation of GDSPS at CMC. Tidal nudging to TPXO8 was documented in the technical specification revision of 2 December 2021, immediately following go-live. The system's scientific lineage traces to Bernier and Thompson's work on tide–surge interaction and ensemble surge prediction for Atlantic Canada (2007, 2015), and to Wang et al. (2021) for the global total water level formulation.

> **Note on dating.** The technical specification PDF carries a document-history table whose dates are *document revision* dates, not implementation dates, and the two can diverge substantially — the v2.0.0 specification is dated 1 February 2023 while the system went operational on 27 July 2023. The changelog is the authoritative source for operational dates.

---

## Official documentation
- GDSPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_gdsps/readme_gdsps_en/
- GDSPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gdsps/readme_gdsps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDSPS_e.pdf
- Technical specifications (v2.3.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_GDSPS_2.3.0_e.pdf
- Technical specifications (v2.1.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDSPS_2.1.0_e.pdf
- Technical note (v2.1.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_gdsps-210_e.pdf
- Factsheet (v2.1.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_gdsps-210_e.pdf
- Factsheet (v2.0.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_gdsps-200_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_gdsps_e.pdf
- Factsheet (v2.1.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_gdsps_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GDSPS_en.svg
- GDSPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_gdsps/changelog_gdsps_en/
- Open Government Portal metadata: https://open.canada.ca/data/en/dataset/d244c9fa-776f-446f-9ccf-1d575cc21a5c
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Wang, P., N.B. Bernier, K.R. Thompson, and T. Kodaira (2021). Evaluation of a global total water level model in the presence of radiational S2 tide. *Ocean Modelling*, 168, 101893. https://doi.org/10.1016/j.ocemod.2021.101893
- Wang, P., N.B. Bernier, K.R. Thompson (2022). Adding baroclinicity to a global operational model for forecasting total water level: Approach and impact. *Ocean Modelling*, 174, 102031. https://doi.org/10.1016/j.ocemod.2022.102031
- Bernier, N.B., and K.R. Thompson (2015). Deterministic and ensemble storm surge prediction for Atlantic Canada with lead times of hours to ten days. *Ocean Modelling*, 86, 114–127.
- Bernier, N.B., and K.R. Thompson (2007). Tide-surge interaction off the east coast of Canada and northeastern United States. *J. Geophys. Res.*, 112, C06008.
- Egbert, G.D., and S.Y. Erofeeva (2002). Efficient inverse modeling of barotropic ocean tides. *J. Atmos. Oceanic Technol.*, 19(2), 183–204.
- Pawlowicz, R., B. Beardsley, S. Lentz (2002). Classical tidal harmonic analysis including error estimates in MATLAB using T_TIDE. *Comput. Geosci.*, 28(8), 929–937. https://doi.org/10.1016/S0098-3004(02)00013-4
- Weatherall, P., et al. (2015). A new digital bathymetric model of the world's oceans. *Earth Space Sci.*, 2(8), 331–345. https://doi.org/10.1002/2015EA000107
