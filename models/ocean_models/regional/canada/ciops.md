# CIOPS (Coastal Ice-Ocean Prediction System)

## What this model is
The **Coastal Ice-Ocean Prediction System (CIOPS)** is Environment and Climate Change Canada's operational kilometric-scale coastal ocean forecast system, run at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. It produces 48-hour hourly forecasts four times daily over three distributed domains, resolving the channels, fjords, estuaries and shelf fronts that its parents cannot.

CIOPS is the bottom tier of ECCC's three-level ocean stack, downstream of [GIOPS](../../global/canada/giops.md) (global, 1/4°) and [RIOPS](./riops.md) (regional, 1/12°). Its defining architectural feature is that **it runs no ocean data assimilation of its own**. Each domain runs a *pseudo-analysis* forced at the ocean boundaries by RIOPS forecasts and spectrally nudged to the RIOPS solution in the deep ocean (>1500 m); the 00 UTC forecast starts from that pseudo-analysis, and the 06/12/18 UTC forecasts start from the previous cycle's 6-hour restart. CIOPS is therefore a coastal dynamical downscaling of RIOPS, and its skill at any location is bounded by RIOPS at the nearest assimilated information.

> **Three distributed products, two systems.** CIOPS-East and CIOPS-West are separate systems with separate technical specifications. **The Salish Sea 500 m product is a sub-domain of CIOPS-West, not a third system** — its files stamp `CIOPSW_240_F`, identical to the West NEP domain, and it is documented only inside the CIOPS-West specification. There is no Salish-specific technical specification, technical note, factsheet, or dependency diagram.

The three domains **diverge substantially** in variables, vertical levels, physics, forcing and even geodetic constants. This entry documents them separately wherever they differ rather than presenting a single averaged description.

The current operational version of both systems is **2.4.0**, implemented 14 April 2026 as an HPC-infrastructure port. Files stamp `version 240`, matching the changelog. Like [RIOPS](./riops.md) and unlike [GIOPS](../../global/canada/giops.md), CIOPS has a single clean version line.

---

## Who runs it
- **Production Unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data
- **Operational since:** 1 December 2021 (v2.0.0, when CIOPS-East and CIOPS-West moved from experimental to operational)
- **Role in any larger system:** terminal — CIOPS feeds no downstream ECCC ocean system
- **Contact stamped in files:** `production-info@ec.gc.ca`

---

## What area it covers

| | **CIOPS-East** | **CIOPS-West (NEP)** | **Salish Sea 500 m** |
|---|---|---|---|
| Model domain (spec) | 34.88°–54.46°N, coast to 323°E | 44.3°–59.6°N, coast to 142.3°W | 46.9°–51.1°N, coast to 126.4°W |
| Native grid | 945 × 1410 | 714 × 1020 | 398 × 898 |
| Native resolution | 1/36° (2–2.5 km) | 1/36° (2–2.5 km) | 500 m |
| **Distributed grid (verified)** | **1333 × 980** | **600 × 785** | **629 × 888** |
| Distributed spacing | 0.03° lon × 0.02° lat | 0.03° lon × 0.02° lat | ~0.008102° lon × ~0.004502° lat |
| Longitude range | 283.00° → 322.96° | 220.00° → 237.97° | 233.800° → 238.887° |
| Latitude range | 34.88° → 54.46° | 44.30° → 59.98° | 47.000° → 50.991° |
| Coordinate dtype | float64 | float64 | float32 |
| `earth_radius` | 6371229.0 (float32) | 6371229.0 (float32) | **6370997.0 (float64)** |
| Ocean-valid fraction | 69.24% | 57.17% | 11.02% |

All three are regular latitude-longitude grids with `grid_mapping_name = latitude_longitude`.

> **The Salish Sea files use a different Earth radius from their two siblings.** East and West declare 6371229.0 m (the GEM/CMC sphere); Salish declares **6370997.0 m** (the Clarke 1866 authalic sphere) and, unlike the other two, omits `longitude_of_prime_meridian` from the grid-mapping variable. The offset is 232 m in radius — negligible for most coastal work, but it means the three products are not strictly co-referenced, and reprojection code that assumes one CRS across the family will be slightly wrong on one of them.

> **The readme understates the Salish Sea resolution.** The CIOPS readme says the system forecasts "over different domains (East, West, Salish Sea) four times a day at 1/36° resolution." The Salish Sea domain is 500 m — roughly 1/216° — not 1/36°. The Datamart page states the resolution correctly.

---

## Basic details

Shared across all three: NEMO 3.6 / OPA ocean engine; Arakawa C-grid; explicit leapfrog with a non-linear free surface solved explicitly (time-splitting of barotropic and baroclinic stepping); **zstar vertical coordinates with variable volume (vvl)**; GLS (Generic Length Scale) vertical mixing; TVD advection with sub-time stepping.

| | **CIOPS-East** | **CIOPS-West (NEP)** | **Salish Sea 500 m** |
|---|---|---|---|
| Version stamp in files | `CIOPSE_240_F` | `CIOPSW_240_F` | `CIOPSW_240_F` |
| Sea ice model | CICE 6.2.0 | CICE 6.2.0 (no ice forms) | **None** — surface flux limiting scheme |
| Model z-levels (spec) | 100 | 75 | 40 |
| **Distributed z-levels (verified)** | **99** | **68** | **39** |
| Deepest distributed level | 5657.81 m | 4488.16 m | 414.534 m |
| Baroclinic time step | 150 s | 60 s | 40 s |
| Momentum diffusion | Bi-Laplacian (Del-4) | **Laplacian (Del-2)** | **Laplacian (Del-2)** |
| Surface scheme | GEM bulk formulae | GEM bulk formulae | **Core bulk formulae** |
| Minimum depth | 7.5 m | 8 m | 4 m |
| Bathymetry | SRTM30 (Becker et al., 2009) + GoMSS (Katavouta et al., 2016) + DFO observations | SRTM30_plus v11 + Cascadia DEM (Haugerud, 1999) | CHS soundings + NOAA 3-arc-second + USGS Cascadia |
| Tidal constituents | **13** (M2, N2, S2, K2, K1, O1, Q1, P1, M4, Mf, Mm, Mn4, Ms4) | **8** (M2, K1, N2, S2, K2, O1, P1, Q1) | **8**, plus tuning for the Strait of Georgia |

- **Forecast length:** 48 h, all domains
- **Update frequency:** 4× daily
- **Production cycles:** 00, 06, 12, 18 UTC
- **Temporal output resolution (verified):** **hourly, 49 steps (000–048), instantaneous** — every variable carries `cell_methods = time: point`. Step `PT000H` is the instantaneous state at cycle time.
- **Prognostic variables:** 3D horizontal currents, potential temperature, salinity, turbulent kinetic energy; 2D sea surface height. **Derived:** vertical velocity. Neither TKE nor vertical velocity is distributed.
- **Archive availability:** rolling 30 days on Datamart

> **CIOPS-West's vertical grid is RIOPS's, truncated.** The 68 distributed West levels (0.50753 m → 4488.16 m) are element-for-element the first 68 levels of the [RIOPS](./riops.md) 75-level grid. The NEP domain has no water below ~4500 m, so the seven deepest levels are dropped from distribution. East and Salish use their own independent level sets.

> **Distributed level counts are lower than model level counts in every domain** (99 vs 100, 68 vs 75, 39 vs 40). This is deliberate truncation of levels that are entirely below the domain bathymetry, not data loss. The Datamart page correctly documents the *distributed* counts (`nk` = 99 / 68 / 39); the technical specifications give the *model* counts. Both are right about different things, which is worth knowing before reconciling them.

---

## Forcing

| | **CIOPS-East** | **CIOPS-West (NEP)** | **Salish Sea 500 m** |
|---|---|---|---|
| Atmospheric | GDPS-**G1** v9.0.0 blended with [HRDPS](../../../nwp_models/regional/canada/hrdps.md) v7.0.0 where coverage allows | GDPS-**G0** v9.0.0 (10 km uncoupled) blended with HRDPS v7.0.0 | **HRDPS only** — no GDPS blend |
| Lateral ocean boundary | RIOPS-F v2.4.0 | RIOPS-F v2.4.0 | Nested within NEP (no separate LBC row in the specification) |
| River runoff | R1D 1D river model for the St. Lawrence; CanHyS observed discharge; climatology elsewhere (Dai and Trenberth, 2002; Saucier et al., 2003) | Monthly climatology (Morrison et al., 2012; Dai, 2017; Dai et al., 2009) | CanHyS observed discharge for the Fraser at Hope (station 08MF005); climatology elsewhere (Morrison, 2011) across 150 input locations |
| Initialization | CIOPS-E pseudo-analysis v2.3.0 | CIOPS-**E** pseudo-analysis v2.3.0 *(see flag)* | CIOPS-W pseudo-analysis v2.3.0 |

All three include spectral nudging of the solution to RIOPS over the deeper ocean (>1500 m) during the pseudo-analysis. The 00 UTC forecast starts from the pseudo-analysis valid at the same time; 06, 12 and 18 UTC start from the previous forecast's 6-hour restart.

> **The CIOPS-West specification says the NEP domain is initialized from the CIOPS-***East*** pseudo-analysis.** The SS500 table in the same document says CIOPS-**West**. Given that CIOPS-East and CIOPS-West cover opposite coasts, the NEP row is almost certainly a copy-paste error and should read CIOPS-W. Flagged rather than silently corrected — **TBD**, confirm with CCMEP.

> **Published forcing versions are stale.** Both specifications name GDPS G0/G1 at **v9.0.0** and HRDPS at **v7.0.0**. GDPS has since moved to 9.1.0 and then **10.0.0 (26 May 2026)**, the release that made the operational atmosphere a hybrid physics-AI system nudged toward [GEML](../../../nwp_models/global/canada/gdps-geml.md). See *Notes*.

---

## Coupling

**CIOPS-East** is coupled online to CICE 6.2.0, with the Delta-Eddington radiation scheme introduced in v2.3.0. It is the only domain distributing sea ice fields.

**CIOPS-West (NEP)** runs the same NEMO/CICE6 binary as East — the v2.3.0 factsheet notes the CICE6 upgrade had "no impact due to no formation of sea ice" in the Northeast Pacific. No ice variables are distributed.

**Salish Sea 500 m** has no CICE component at all; sea ice is handled by a surface flux limiting scheme in the surface layer.

No wave coupling in any domain. No two-way atmospheric coupling — all three take one-way atmospheric fluxes.

---

## Data assimilation

**None.** CIOPS runs no ocean or ice data assimilation. Assimilation skill is inherited from [RIOPS](./riops.md) through the lateral boundaries and the deep-ocean spectral nudging in the pseudo-analysis. This is the clearest structural difference from both parents: [GIOPS](../../global/canada/giops.md) and [RIOPS](./riops.md) each run SAM2 ocean analyses, and RIOPS additionally runs the RIPS 2D-Var ice analysis.

---

## What it provides

Filenames use **descriptive CamelCase variable tokens** (`SeaIceAreaFraction`, `SeaWaterPotentialTemp`), while the variables *inside* the files retain the NEMO short names (`iiceconc`, `votemper`) plus the CMC `nomvar` codes (`GL`, `TM`). This is the third distinct filename convention across the three sibling systems — GIOPS uses `CMC_giops_iiceconc_…`, RIOPS uses `…_IICECONC_…`, CIOPS uses `…_SeaIceAreaFraction_…`.

### Ocean fields — all three domains (11 variables per step)
| Filename token | In-file name | `nomvar` | Levels | Units |
|---|---|---|---|---|
| `MixedLayerThickness` | `sokaraml` | `MLW` | Sfc | m (density criterion) |
| `TurboclineDepth` | `somixhgt` | `MLTW` | Sfc | m |
| `SeaSfcHeight` | `sossheig` | `SSH` | Sfc | m above geoid |
| `SeaWaterPotentialTemp` | `votemper` | `TM` | DBS-0.5m and DBS-all | Kelvin |
| `SeaWaterSalinity` | `vosaline` | `SALW` | DBS-0.5m and DBS-all | `1e-3` |
| `SeaWaterVelocityX` | `vozocrtx` | `UUW` | DBS-0.5m and DBS-all | m s⁻¹ |
| `SeaWaterVelocityY` | `vomecrty` | `VVW` | DBS-0.5m and DBS-all | m s⁻¹ |

### Sea ice fields — CIOPS-East only (10 additional variables)
`SeaIceAreaFraction` (`iiceconc`), `SeaIceVol` (`iicevol`, volume per unit area in m), `SeaIceSnowVol` (`isnowvol`, cm), `SeaIceVelocityX`/`Y` (`itzocrtx`/`itmecrty`), `SeaIceSnowTemp` (`iicesurftemp`), `SeaIceCompressiveStrength` (`iicestrength`), `SeaIceInternalPressure` (`iicepressure`), `SeaIceDivergence` (`iicedivergence`), `SeaIceShear` (`iiceshear`).

> **Neither CIOPS-West nor the Salish Sea distributes any sea ice field.** Both carry exactly 11 variables per step; only CIOPS-East carries 21. The shared `CIOPS_Variables-List_en.csv` lists all ten ice variables without indicating that they are East-only.

### Currents include tides
All three domains run explicit tidal forcing from WebTide, so `vozocrtx`, `vomecrty` and `sossheig` contain the tidal signal. Hourly output is a necessity rather than a convenience — coarser sampling would alias the semidiurnal constituents. This matches [RIOPS](./riops.md) and contrasts with [GIOPS](../../global/canada/giops.md), which has no tides.

### Static fields
None distributed in any domain. No bathymetry, land-sea mask, or cell-area files; the domain mask must be inferred from `_FillValue`.

---

## Data availability

- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required. Suggested attribution: "Data Source: Environment and Climate Change Canada." https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded only
- **Data formats:** NetCDF-4 (HDF5 container), `Conventions = CF-1.6`, **zlib level 1 with shuffle**. Chunking: East 2D `[1, 490, 667]`, West 2D `[1, 785, 600]` and Salish 2D `[1, 888, 629]` (both whole-field); East 3D `[1, 25, 245, 334]`, West 3D `[1, 23, 262, 200]`, Salish 3D `[1, 13, 296, 210]`. Time encoded as seconds since 1950-01-01 00:00:00, gregorian calendar.
- **Official download locations:**
  - `https://dd.weather.gc.ca/today/model_ciops/east/2km/{HH}/{hhh}/`
  - `https://dd.weather.gc.ca/today/model_ciops/west/2km/{HH}/{hhh}/`
  - `https://dd.weather.gc.ca/today/model_ciops/salish-sea/500m/{HH}/{hhh}/`
  - Dated archive: `https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_ciops/{domain}/{res}/{HH}/{hhh}/`
  - `{HH}` is `00`/`06`/`12`/`18`; `{hhh}` runs `000`–`048` hourly
- **File naming:** `{YYYYMMDD}T{HH}Z_MSC_CIOPS-{Domain}_{Var}_{LVLTYPE}-{LVL}_LatLon{res}_PT{hhh}H.nc`
  - `{Domain}` is `East`, `West`, or `SalishSea`; `{LVLTYPE}-{LVL}` is `Sfc`, `DBS-0.5m`, or `DBS-all`; `{res}` is `0.03x0.02` or `0.008x0.005`
  - Example: `20260808T18Z_MSC_CIOPS-East_SeaWaterSalinity_DBS-all_LatLon0.03x0.02_PT000H.nc`
  - Note the **domain token in the path differs from the token in the filename** — `salish-sea/500m/` on disk, `CIOPS-SalishSea` in the filename.
- **Files per cycle (live-confirmed on all four 2026-08-08 cycles):** East **1,029** (21 × 49); West **539** (11 × 49); Salish **539** (11 × 49)
- **Volume per cycle:** East ~21.0 GiB; West ~4.22 GiB; Salish ~1.05 GiB
- **Daily totals:** East 4,116 files / **84.04 GiB**; West 2,156 / **16.91 GiB**; Salish 2,156 / **4.20 GiB** — **8,428 files and ~105 GiB per day across the family**
- **File size:** the 3D velocity fields dominate. East `SeaWaterVelocityY_DBS-all` 148–150 MiB and `SeaWaterVelocityX_DBS-all` 140–145 MiB per step, against `SeaWaterPotentialTemp_DBS-all` at 66–67 MiB. West 3D 11–33 MiB; Salish 3D 3.8–6.4 MiB. East 2D sea ice files are tiny (54–112 KiB) because the fields are near-uniform.
- **Retention:** dated directories persist **30 days** — live-probed 2026-08-09: `20260710` present, `20260709` returns 404 (same policy as GIOPS, RIOPS, GDSPS and RESPS)
- **Publication latency (three-day sample, 2026-08-06 to 2026-08-08):** each domain-cycle is written as a single 1–3 minute burst.
  - 06Z and 18Z complete at roughly **T+4h30m to T+4h56m**
  - **00Z completes roughly T+6h01m to T+6h17m** across all three domains — about 90 minutes later than the other cycles, consistent with the 00Z forecast waiting on the pseudo-analysis and the RIOPS/GIOPS analysis chain above it
  - **CIOPS-East 12Z is a consistent outlier at T+5h13m to T+5h20m**, roughly 40 minutes behind East's own 06Z and 18Z cycles and behind West and Salish at 12Z
  - Ordering within a cycle is stable: West first, then East, then Salish
- **Push notification:** available via AMQP (MSC Datamart sr3/sarracenia)
- **Other access:** MSC GeoMet serves **213 CIOPS-East, 202 CIOPS-West and 202 CIOPS-SalishSea layers** as WMS/WCS, plus per-domain footprint layers. MSC AniMet provides visualization; a WMS view is also available at https://www.meteocentre.com/plus under the CMC experimental tab.
- **Discovery metadata:**
  - CIOPS-East: https://open.canada.ca/data/en/dataset/bfe44cce-a9c4-467f-9172-c8800b32e4ec
  - CIOPS-West: https://open.canada.ca/data/en/dataset/390abee6-4ba0-4d6e-ae79-25753d1c43f3
  - CIOPS-SalishSea: https://open.canada.ca/data/en/dataset/cccb0064-5ab3-416a-a4f0-566b54f466f3

---

## Version history

### April 14, 2026 — CIOPS-East v2.4.0 and CIOPS-West v2.4.0 (current)
- Migration to ECCC's new High Performance Computing infrastructure
- Computational only. **Confirmed by the file version stamp**, which reads `240` across the current archive for all three distributed domains.

### June 11, 2024 — CIOPS-East v2.3.0 and CIOPS-West v2.3.0
**CIOPS-East:**
- New region of increased mixing in the upper St. Lawrence estuary, reduced to better fit observations
- Updated St. Lawrence River temperature climatology
- St. Lawrence discharge switched from the IML feed to direct calculation from CanHyS observations
- R1D river model mean sea level adjusted to align with the RIOPS IC-4 Mean Dynamic Topography
- CICE 4 → **CICE 6.2.0** with Delta-Eddington radiation

**CIOPS-West:**
- **Atmospheric forcing switched from RDPS to GDPS-G0 at 10 km** (RDPS retired)
- `dfo_nemo` module updated in line with DFO port model versions; DFO boxes (XIOS output) and weight files adjusted
- Same NEMO binary as CIOPS-East, with CICE 6.2.0 and Delta-Eddington — no impact, as no sea ice forms in the domain

### Version 2.2.0 — undocumented
Both v2.3.0 factsheets state the upgrade was "from version 2.2.0 to 2.3.0", but **the CIOPS changelog contains no v2.2.0 entry**, jumping directly from 2.1.0 (June 2022) to 2.3.0 (June 2024). The date and content of v2.2.0 are not published. **TBD.**

### June 28, 2022 — CIOPS v2.1.0
- HPC infrastructure migration (computational only)

### December 1, 2021 — CIOPS v2.0.0 (declared operational)
- **CIOPS-East and CIOPS-West moved from experimental to operational status**
- CIOPS-East: reduced bottom roughness; **vertical resolution in the upper water column increased from 75 to 100 levels**; reduced wave roughness parameter; new wave roughness formulation for wind stress; increased diffusivity in the upper St. Lawrence estuary west of Tadoussac
- CIOPS-West: **addition of the Salish Sea sub-domain at 500 m resolution**

---

## Notes

- **Latitude coordinates are silently masked at the domain edges in two of the three domains.** The `latitude` variables declare `valid_min` / `valid_max` as float32 values placed exactly at the domain endpoints, while the coordinates themselves are stored at a precision that falls marginally outside those bounds. CF-aware readers therefore mask edge entries:
  - **CIOPS-East:** indices **0 and 979** both masked. `valid_min` float32(34.88) = 34.880001068 exceeds the stored float64 34.88; `valid_max` float32(54.46) = 54.459999084 falls below the stored 54.46. Both the southernmost and northernmost rows of the latitude axis come back masked.
  - **Salish Sea:** index **0** masked. The stored float32 46.999996185 falls below `valid_min` 47.0.
  - **CIOPS-West:** unaffected — its bounds happen to sit outside the data range.

  Longitude is unaffected everywhere. The field data is fine; it is the coordinate axis that is damaged, which turns it into a masked array and can break naive indexing, plotting, or interpolation at the domain edge. Read with `set_auto_mask(False)` to recover the values. This is the same class of defect as the [RIOPS](./riops.md) `depth` `valid_max` problem, here affecting the horizontal axis and affecting the three domains inconsistently.

- **Sea ice is zero-filled, not masked** (CIOPS-East). `iiceconc` is valid across the whole ocean domain with a minimum of exactly `0.0`. In the August sample every valid point was exactly zero — no ice in the Northwest Atlantic at that season. This matches [RIOPS](./riops.md) and is the **opposite** of [GIOPS](../../global/canada/giops.md), which fill-values everything below 1% concentration.

- **The ice mask and ocean mask differ slightly.** On CIOPS-East, `iiceconc` is valid at 69.45% of grid points while `sokaraml` is valid at 69.24% — a small but real difference in domain mask between the ice and ocean fields, echoing the one-point discrepancy seen in RIOPS.

- **The Datamart page is unusually good — and worth trusting over the RIOPS one.** It correctly documents per-domain grid dimensions (`ni`/`nj`/`nk` = 1333/980/99, 600/785/68, 629/888/39), gives the full and correct depth level lists for each domain, and correctly describes the files as NetCDF. All of this is verified accurate against the files. This stands in sharp contrast to the [RIOPS](./riops.md) Datamart page, which states the wrong level count, lists GIOPS's depths, and calls the files GRIB2.

- **The variable list CSV is shared across domains and does not flag the ice-field asymmetry.** `CIOPS_Variables-List_en.csv` lists all ten sea ice variables with no indication that they exist only for CIOPS-East. It does correctly enumerate the three separate level counts (39, 68, 99).

- **The Salish Sea product is under-documented relative to its siblings.** No dedicated technical specification, technical note, factsheet, dependency diagram, or changelog entry exists. Everything published about it sits in sections 2 and 3 of the CIOPS-West specification. It nonetheless has its own Open Government metadata record and its own 202 GeoMet layers, so it is treated as a first-class product in distribution but not in documentation.

- **Physics diverges more than the shared branding suggests.** Momentum diffusion is bi-Laplacian in East but Laplacian in West and Salish; the surface scheme is GEM bulk formulae in East and West but Core bulk formulae in Salish; baroclinic time steps run 150 s / 60 s / 40 s; minimum depths are 7.5 m / 8 m / 4 m; East uses 13 tidal constituents against 8 in the two western domains. Treating "CIOPS" as one model configuration will produce wrong answers.

- **Changelog typographical errors.** The 14 April 2026 heading reads "CIOPS-Eat", and the 11 June 2024 section refers to "CIOPS-Eest". Cosmetic, but they defeat text search for "CIOPS-East" on that page.

- **The AI question reaches CIOPS by two routes, and neither is documented.** CIOPS takes atmospheric forcing from HRDPS blended with GDPS-G0/G1. GDPS moved to **10.0.0** on 26 May 2026 with GEML spectral nudging, and HRDPS is itself piloted by GDPS-G0. So AI-derived information could enter CIOPS both directly through the GDPS blend and indirectly through HRDPS's pilot — and, further upstream, through [RIOPS](./riops.md) at the lateral boundaries. **None of this is documented, and the CIOPS files stamp only their own version, so there is no forensic route from the data.** **TBD:** confirm with CCMEP. See the corresponding flags in the [GIOPS](../../global/canada/giops.md) and [RIOPS](./riops.md) entries, and the open scope question about whether these systems belong in [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Volume is concentrated in one domain.** CIOPS-East alone is 80% of the family's ~105 GiB/day, and its two 3D velocity files account for roughly two-thirds of that. Users needing surface fields only should filter on `_Sfc_` and `DBS-0.5m`, which reduces East from ~21 GiB to under 0.4 GiB per cycle.

- **Relative-link correction.** The previous revision linked GIOPS as `./giops.md` from `models/ocean_models/regional/canada/`, which does not resolve. Corrected to `../../global/canada/giops.md`. `./riops.md` was already correct. The repository-wide relative-link sweep remains outstanding.

---

## Official documentation
- CIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops_en/
- Datamart access, grids and file nomenclature: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops-datamart_en/
- GeoMet access: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops-geomet_en/
- Variable list (CSV, JS-loaded on the Datamart page): https://eccc-msc.github.io/open-data/assets/csv/CIOPS_Variables-List_en.csv
- CIOPS-East technical specifications (serves v2.3.0): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_CIOPS-EAST_e.pdf
- CIOPS-East technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_ciops-east_e.pdf
- CIOPS-East factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_ciops-east_e.pdf
- CIOPS-West technical specifications (serves v2.3.0; also the only documentation of the Salish Sea domain): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_CIOPS-WEST_e.pdf
- CIOPS-West technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_ciops-west_e.pdf
- CIOPS-West factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_ciops-west_e.pdf
- CIOPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/changelog_ciops_en/
- ECCC multi-system changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Dependency diagrams: [CIOPS-East](https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_CIOPS-E_en.svg) · [CIOPS-West](https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_CIOPS-W_en.svg)
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Madec, G. et al. (2017). NEMO ocean engine (v3.6). *Notes du Pôle de modélisation de l'IPSL*, No. 27. Zenodo. https://doi.org/10.5281/zenodo.1472492
- Madec, G., Delecluse, P., Imbard, M., Lévy, C. (1998). OPA 8.1 Ocean General Circulation Model reference manual. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace.
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Hunke, E.C. (2001). Viscous-plastic sea ice dynamics with the EVP model: linearization issues. *J. Comput. Phys.*, 170, 18–38.
- Lipscomb, W.H., Hunke, E.C., Maslowski, W., Jakacki, J. (2007). Ridging, strength, and stability in high-resolution sea ice models. *J. Geophys. Res.*, 112, C03S91. https://doi.org/10.1029/2005JC003355
- Becker, J.J. et al. (2009). Global bathymetry and elevation data at 30 arc seconds resolution: SRTM30_PLUS. *Marine Geodesy*, 32(4), 355–371. https://doi.org/10.1080/01490410903297766
- Katavouta, A., Thompson, K.R., Lu, Y., Loder, J.W. (2016). Interaction between the tidal and seasonal variability of the Gulf of Maine and Scotian Shelf region. *J. Phys. Oceanogr.*, 46(11), 3279–3298.
- Haugerud, R.A. (1999). Digital elevation model (DEM) of Cascadia. USGS Open-File Report 99-369. https://pubs.usgs.gov/of/1999/0369/
- Morrison, J., Foreman, M.G.G., Masson, D. (2012). A method for estimating monthly freshwater discharge affecting British Columbia coastal waters. *Atmosphere-Ocean*, 50(1), 1–8. https://doi.org/10.1080/07055900.2011.637667
- Dai, A., Trenberth, K.E. (2002). Estimates of freshwater discharge from continents. *J. Hydrometeorol.*, 3, 660–687.
- Saucier, F.J., Roy, F., Gilbert, D., Pellerin, P., Ritchie, H. (2003). Modeling the formation and circulation processes of water masses and sea ice in the Gulf of St. Lawrence. *J. Geophys. Res.*, 108, 3269. https://doi.org/10.1029/2000JC000686
- Rascle, N., Ardhuin, F., Queffeulou, P., Croizé-Fillon, D. (2008). A global wave parameter database for geophysical applications. Part 1. *Ocean Modelling*, 25, 154–171. https://doi.org/10.1016/j.ocemod.2008.07.006
- Smith, S.D. et al. (1992). Sea surface wind stress and drag coefficients: the HEXOS results. *Boundary-Layer Meteorol.*, 60, 109–142. https://doi.org/10.1007/BF00122064
- Umlauf, L., Burchard, H. (2003). A generic length-scale equation for geophysical turbulence models. *J. Mar. Res.*, 61, 235–265.
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
