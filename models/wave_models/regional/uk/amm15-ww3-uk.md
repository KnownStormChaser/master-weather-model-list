# AMM15-WW3 (North-West European Shelf Wave Analysis and Forecast)

## What this model is
AMM15-WW3 is an operational regional wave analysis and forecast system for the North-West European Shelf, run by the **UK Met Office** as part of the **Atlantic European North West Shelf Monitoring and Forecasting Centre (NWS MFC)** and distributed through the **Copernicus Marine Service**.

It is a WAVEWATCH III v7.1 wave model two-way coupled online to a NEMO v3.6 physical ocean model via OASIS3-MCT, sharing the same AMM15 (Atlantic Margin Model 15-cell) domain as the NWS physics product. The system uses the Met Office's Spherical Multiple-Cell (SMC) variable-resolution wave grid (Li and Saulter, 2014), which ranges from 3 km in deep water to 1.5 km near coastlines and islands for improved resolution of topographic features. Both wave and physics products are delivered on the same regular 1/33° longitude × 1/74° latitude output grid.

The official Copernicus Marine product identifier is `NWSHELF_ANALYSISFORECAST_WAV_004_014`. "AMM15-WW3" is the system name used in the PUM and reflects the model pairing (AMM15 domain, WAVEWATCH III). The product has an unusual lineage documented in the Version history section below — between September 2023 and November 2025 it was an extraction from Spain's IBI operational system, not a Met Office product.

---

## Who runs it
- **Production Unit:** UK Met Office (current, as of November 2025)
- **Country:** United Kingdom
- **Role in Copernicus Marine:** Production Unit for the NWS MFC wave product
- **Previous operator:** Nologin/CESGA (Spain), 2023–2025 — see Version history

---

## What area it covers
- **Coverage:** North-West European Shelf and adjacent deep North-East Atlantic
- **Domain bounds:** 16°W – 13°E, 46°N – 62.75°N
- **Grid dimensions:** 958 × 1240
- **Regions covered:** North Sea, Irish Sea, English Channel, Celtic Sea, Bay of Biscay, plus adjacent deep-water regions of the North-East Atlantic
- **Masked regions:** Grid points in the Baltic Sea and Kattegat south of 57.25°N are masked (handled by the Baltic MFC products)

The domain deliberately extends beyond the continental shelf to place the model's boundary region in deep water, while the focus region is the shelf seas themselves.

---

## Basic details
- **Model type:** Regional deterministic wave model (two-way coupled to ocean physics)
- **Core wave model:** WAVEWATCH III v7.1
- **Coupled physics model:** NEMO v3.6 (Madec et al., 2017)
- **Coupling framework:** OASIS3-MCT
- **System name:** AMM15
- **Native wave grid:** SMC (Spherical Multiple-Cell) variable-resolution grid at 3–1.5 km
- **Native physics grid:** 1/60° rotated spherical-polar grid (~1.8 km uniform after Euler rotation; Guihou et al., 2017)
- **Output grid (delivered):** Regular 1/33° longitude × 1/74° latitude (~1.9 ± 0.4 km × 1.5 km)
- **Spectral resolution:** 36 directions (10° bins) × 30 geometrically spaced frequency cells
- **Frequency range:** 0.04118 Hz to 0.6532 Hz (~25 s to ~1.5 s wave periods); f⁻⁵ parametric tail above spectral maximum
- **Non-linear interactions:** Discrete Interaction Approximation (DIA) with multiplication factor 1.1 (Hasselmann et al., 1985)
- **Forecast length:** 7 days (168 hours)
- **Analysis window:** 48-hour hindcast run prior to each forecast (T-48 to T+0)
- **Update frequency:** Daily
- **Production cycle:** 00 UTC base time
- **Target delivery time:** 12:00 UTC daily (actual delivery typically 06:40–10:00 UTC)
- **Temporal output resolution:** 1-hourly instantaneous
- **Archive:** Rolling 2-year analysis archive
- **Bathymetry:** EMODnet 2015, with minimum 10 m depth imposed and Chawla-Tolman (2008) sub-grid obstruction cells

---

## Coupling
AMM15-WW3 is **two-way online coupled** to the NEMO v3.6 physical ocean model that underlies the companion physics product `NWSHELF_ANALYSISFORECAST_PHY_004_013`:

- **Wave → ocean:** Modification of water-side surface stress on the ocean by wave growth and dissipation; Stokes-Coriolis force added to the momentum equation; wave-height-dependent ocean surface roughness
- **Ocean → wave:** The wave model is forced by the ocean surface currents from NEMO
- **Coupling frequency:** Hourly

This is a tighter coupling than most operational wave products. For comparison, [IBIWAM](../spain/ibiwam.md) is also two-way coupled but with a different coupling granularity; [BALWAM](../finland/balwam.md) receives hourly surface currents and sea level from a companion physics product as one-way forcing, without full two-way feedback into ocean physics.

---

## Forcing
- **Atmospheric forcing:** Met Office Global Unified Model (MetUM) 10 m winds at 10 km horizontal resolution
- **Lateral boundary conditions:** Wave spectra from the Met Office global wave model (GloWave; Saulter et al., 2016) at 25 km, interpolated onto the SMC grid
- **Surface currents:** From the coupled NEMO AMM15 physics (via two-way OASIS3-MCT coupling)
- **Initial conditions:** Restart from previous forecast cycle, with 48-hour hindcast spin-up applied before each forecast using analysed winds and surface currents

---

## Data assimilation
As of the November 2025 Met Office re-introduction, **no wave data assimilation is documented in the PUM** for the current AMM15-WW3 system. The initial conditions are constrained via the 48-hour hindcast run prior to each forecast, which uses analysed atmospheric and ocean fields. This is a significant difference from the prior IBI-extraction version of this product (2023–2025), which used Optimal Interpolation of multi-satellite altimeter SWH including SWOT-NADIR, HY2B, Jason-3, Sentinel-3A/B, Sentinel-6A, CryoSat-2, and CFOSAT.

Users comparing pre-November-2025 and post-November-2025 catalog data should be aware of this methodology change alongside the operator change.

---

## What it provides
Hourly instantaneous fields of integrated wave parameters and partitioned swell fields:

### Total wave spectrum parameters
- Spectral significant wave height (Hm0)
- Expected maximum wave height (linear, 1st order) — VCMX
- Expected maximum wave height from crest (linear, 1st order) — VMXL
- Wave periods: spectral moments (-1,0), (0,2), peak period (Tp)
- Mean wave direction and principal direction at spectral peak
- Stokes drift (U and V components at surface)

### Partitioned fields
- Wind wave: height, period, direction
- Primary swell: height, period, direction
- Secondary swell: height, period, direction

Partitioning is defined using topographic partitions and a spectral wave-age cut-off.

### Static file
- Land-sea mask, depth, bathymetry (shared with the NWS physics product)

### 3D Stokes drift
The PUM documents how to compute 3D Stokes drift from the surface Stokes drift (VSDX, VSDY), Hm0, and mean wave period using the Breivik et al. (2016) approximation, for users needing total Lagrangian velocity (surface Eulerian current + Stokes drift) for drift applications.

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.7 conventions)
- **Product identifier:** `NWSHELF_ANALYSISFORECAST_WAV_004_014`
- **Dataset identifiers:**
  - `cmems_mod_nws_wav_anfc_1.5km_PT1H-i` (hourly forecast and analysis)
  - `cmems_mod_nws_wav_anfc_1.5km_static` (bathymetry, mask)
- **File naming:** `metoffice_wave_amm15_NWS_WAV_b{YYYYMMDD}_hi{YYYYMMDD}.nc`
- **File size:** ~261 MB per file
- **Official access:** https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_WAV_004_014/description
- **Delivery mechanism:** Copernicus Marine Toolbox
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

This product has an unusual lineage. The Copernicus Marine identifier has persisted since 2020, but the underlying operator, model, and methodology have changed substantially over time.

### November 2025 — Issue 3.0 (current)
**Re-introduction of the NWS wave product by the UK Met Office.** Complete system replacement.
- New operator: UK Met Office (previously Nologin/CESGA, Spain)
- New model: WAVEWATCH III v7.1 coupled to NEMO v3.6 via OASIS3-MCT (AMM15 system)
- New grid: SMC 3–1.5 km variable-resolution grid
- Two-way online wave-ocean coupling
- 7-day forecast retained
- Data assimilation not documented (previously DA was part of the IBI-extraction version)

### May 2024 — Issue 2.1
- Under Nologin/CESGA operation (IBI extraction)
- Model resolution increased from 1/20° to 1/36°
- Assimilation upgrade: SWOT-NADIR and HY2B altimetric data added

### September 2023 — Issue 2.0
- **"First release of the NWS wave analysis and forecast product, as an extraction of the IBI operational system"** — per PUM record table
- Operator: Nologin/CESGA (Spain) — the same production unit as the [IBIWAM](../spain/ibiwam.md) product
- Model: MFWAM at 1/20°
- Included MFWAM's multi-satellite altimeter data assimilation

### 2020–2022 — Issues 1.0–1.3
- Earlier NWS wave product (pre-IBI-extraction era)
- January 2022: Upgrade to GLO-HR boundary conditions and new MDT

**Interpretation note:** Between September 2023 and November 2025, pulling data from `NWSHELF_ANALYSISFORECAST_WAV_004_014` returned output from the same operational system as `IBI_ANALYSISFORECAST_WAV_005_005`, just cropped to the NWS domain. From November 2025 onward, the product is a genuinely independent Met Office system. For long-term studies or climatologies, the pre- and post-November-2025 time series should be treated as separate model eras.

---

## Relationship to other wave products

### Copernicus Marine regional wave portfolio
AMM15-WW3 is part of the Copernicus Marine set of coupled regional wave forecasting systems, each run by a different national institute:

- **[BALWAM](../finland/balwam.md)** — Baltic Sea (FMI, Finland; WAM Cycle 4.7)
- **[MEDWAM](../greece/medwam.md)** — Mediterranean Sea (HCMR, Greece; WAM Cycle 6)
- **[IBIWAM](../spain/ibiwam.md)** — Iberian-Biscay-Irish waters (Nologin/CESGA, Spain; MFWAM)
- **[ARCWAM](../norway/arcwam.md)** — Arctic Ocean (MET Norway; WAM Cycle 4.7)
- **This entry** — North-West European Shelf (UK Met Office; WAVEWATCH III v7.1)

AMM15-WW3 is the only one of this set to use WAVEWATCH III as its core model (the others all use the WAM family) and the only one with genuine two-way online wave-ocean coupling via OASIS3-MCT as a matter of operational design.

### Overlapping regional coverage
The NWS domain overlaps substantially with:
- **[IBIWAM](../spain/ibiwam.md)** — IBI covers 19°W–5°E, 26°N–56°N; the overlap with NWS is the Bay of Biscay, Celtic Sea, Irish Sea approaches, and western English Channel. IBIWAM is at 1/36° with MFWAM and multi-satellite DA; AMM15-WW3 is at 1.5–3 km SMC with WW3 and no documented DA but two-way ocean coupling.
- **[UK Met Office Global Wave Model (GloWave)](../../global/uk/ukmo-wave.md)** — provides AMM15-WW3's lateral boundary conditions; same operator, global vs regional scope

### Relationship to GloWave
AMM15-WW3 is nested inside **GloWave** (the Met Office global wave model). GloWave provides the 25 km wave spectra boundary conditions, while AMM15-WW3 refines these with the 3/1.5 km SMC grid and couples them to the regional NEMO AMM15 ocean/tide system. GloWave itself has its own Copernicus Marine distribution via the [UK Met Office Global Wave product](../../global/uk/ukmo-wave.md).

---

## Notes
- A minimum depth of 10 m is imposed in the underlying ocean model because NEMO v3.6 lacks wetting-and-drying capability. Areas like the Wadden Sea, where extensive bathymetry is shallower than 10 m, are affected: wave propagation and wave-tide interaction will be incorrect at these depths, and where the seabed would be exposed the model incorrectly retains water. The PUM explicitly recommends against direct use of model fields in regions with extensive sub-10 m bathymetry, and suggests nested downstream models place boundaries well away from such areas. NEMO v4's wetting-and-drying module (O'Dea et al. 2020) is planned for a future upgrade.
- The SMC grid (Li and Saulter 2014) is a distinctive Met Office approach: rather than a single uniform grid, the model runs on a multi-resolution triangular/square grid with finer cells near coastlines and islands. Output is interpolated onto the regular 1/33° × 1/74° delivered grid.
- The operational use case explicitly cited in the PUM includes coastal flood forecasting by the Met Office, UK Environment Agency, Scottish Environment Protection Agency, Natural Resources Wales, and the Department for Infrastructure Rivers Directorate Ireland. These agencies use the forecasts for warnings around wave overtopping and coastal flooding.
- The historical link between this product and the IBI system (2023–2025) is the reason the IBIWAM entry in this repository mentions that NWS was derived from IBI during that period — this entry captures the other side of that transition.
- The product is delivered with each file covering 24 hours; the dimensions in the annex show `time = UNLIMITED, // (24 currently)`. The forecast is split across 7 daily files per cycle.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_WAV_004_014/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-NWS-PUM-004-014.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-NWS-QUID-004-014.pdf
- UK Met Office: https://www.metoffice.gov.uk/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- Li, J.G. and Saulter, A. (2014). Unified global and regional wave model on a multi-resolution grid. *Ocean Dynamics*, 64, 1657–1670.
- Saulter, A., Bunney, C. and Li, J. (2016). Application of a refined grid global model for operational wave forecasting. Met Office: Exeter, UK.
- Guihou, K. et al. (2017). Kilometric scale modelling of the North West European shelf seas. *J. Geophys. Res.-Oceans*, 122. https://doi.org/10.1002/2017JC012960
- Hasselmann, S. et al. (1985). Computations and parametrizations of the nonlinear energy transfer in a gravity-wave spectrum. Part 2. *J. Phys. Oceanogr.*, 15, 1378–1391.
- Breivik, Ø., Bidlot, J.R., Janssen, P.A. (2016). A Stokes drift approximation based on the Phillips spectrum. *Ocean Model.*, 100, 49–56.
- Chawla, A., Tolman, H.L. (2008). Obstruction grids for spectral wave models. *Ocean Modelling*, 22, 12–25.
- Tolman, H.L. (2014). User manual and system documentation of WAVEWATCH III version 4.18. NOAA/NWS/NCEP/MMAB Technical Note 316.
- O'Dea, E. et al. (2020). Implementation and assessment of a flux limiter based wetting and drying scheme in NEMO. *Ocean Modelling*, 155. https://doi.org/10.1016/j.ocemod.2020.101708
