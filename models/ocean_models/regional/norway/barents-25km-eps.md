# Barents-2.5km EPS (Coupled Ocean and Sea Ice Ensemble Prediction System for the Arctic)

## What this model is
**Barents-2.5km EPS** is MET Norway's operational **coupled ocean and sea ice ensemble prediction system** for the Barents Sea and the waters around Svalbard, running at 2.5 km with 42 vertical layers.

It is built on the **METROMS** framework, which couples **ROMS v3.7** (ocean) to **CICE v5.1** (sea ice). MET Norway describes it as its operational forecasting model for ocean currents and sea ice in the region, and identifies its primary applications as drift modelling of pollutants and icebergs, and **search-and-rescue in the Arctic**.

The ensemble carries **24 members with 96-hour lead time**, generated as a **time-lagged ensemble** — six members per six-hourly cycle, composing a full 24-member set over 24 hours. All members are **weakly nudged towards [TOPAZ5](./topaz5.md)**.

This is the only entry in the repository's MET Norway marine set built on a **Lambert conformal conic** projection, and the only one publishing a genuine, usable ensemble.

*Live-verified against the operational THREDDS distribution on 2026-07-28 (2026-07-27 production day), plus archive sampling back to 2022-06.*

---

## Who runs it
- **Production Unit:** Norwegian Meteorological Institute (MET Norway), Division for Ocean and Ice (`creator_email = fou-hi@met.no`)
- **Country:** Norway
- **Programme:** `project = "Ocean and Ice - Research to Operation (HI-R2O)"`
- **Named contributors (file metadata):** Martina Idzanovic, Edel S. U. Rikardsen (technical contacts), Mateusz Matuszak (metadata author)
- **Operational status:** `operational_status = "Operational"`; `dataset_production_status = "In Work"` (the two disagree — a recurring pattern across MET Norway's marine files)
- **Role in larger system:** consumer of [TOPAZ5](./topaz5.md) via nudging; the highest-resolution operational ocean/ice system for the Barents Sea

---

## What area it covers
- **Coverage:** Barents Sea, waters around Svalbard, and the adjacent Arctic
- **Domain bounds:** **62.134°N – 87.621°N, 17.957°W – 79.564°E**
- **Grid dimensions:** 739 (`X` / `xi_rho`) × 949 (`Y` / `eta_rho`), ROMS Arakawa C-grid (`xi_u` 738, `eta_v` 948)
- **Projection:** **Lambert conformal conic**
  - `grid_mapping_name = lambert_conformal_conic`
  - `standard_parallel = 77.5, 77.5` (tangent case — both parallels equal)
  - `longitude_of_central_meridian = -25.0`, `latitude_of_projection_origin = 77.5`
  - `earth_radius = 6371000.0`
  - proj4: `+proj=lcc +lat_0=77.5 +lon_0=-25 +lat_1=77.5 +lat_2=77.5 +no_defs +R=6.371e+06`
- **Horizontal resolution:** 2.5 km
- **Georeferencing:** 2-D `lat` / `lon` auxiliary variables; `angle` (grid rotation) supplied
- **Land-sea mask:** `sea_mask`
- **Bathymetry field:** `h`

> **A different projection family from every other MET Norway marine product documented here.** [NorKyst v3](./norkyst-v3.md), [Norshelf](./norshelf.md), and [TOPAZ5](./topaz5.md) are polar stereographic; the [wave models](../../../wave_models/regional/norway/met-norway-ww3.md) are rotated lat-lon. This one is Lambert conformal conic with a tangent standard parallel at 77.5°N. No shared reprojection cache applies.

---

## Basic details
- **Model type:** Regional coupled ocean + sea ice **ensemble** prediction system
- **Framework:** **METROMS** — https://github.com/metno/metroms
- **Core ocean model:** **ROMS v3.7**
- **Sea ice model:** **CICE v5.1**
- **System version:** **Barents-2.5km v2.0**
- **Horizontal resolution:** 2.5 km
- **Vertical levels (native):** **42** terrain-following stretched levels (`s_rho = 42`, `s_w = 43`)
- **Vertical coordinate:** terrain-following (stretched sigma), published natively in the `sdepth` product
- **Vertical levels (z-interpolated product):** **16 fixed depths** — 0, 3, 10, 15, 25, 50, 75, 100, 150, 200, 250, 300, 500, 1000, 2000, 3000 m
- **Forecast length:** **96 hours** (ensemble surface product); **24 hours** for the `sdepth` and `zdepth` control-member products
- **Update frequency:** 4× daily — 00, 06, 12, 18 UTC (ensemble cycling); once daily for `sdepth`/`zdepth`
- **Temporal output resolution:** hourly
- **Conventions:** CF-1.8, ACDD-1.3
- **Archive availability:** surface ensemble from **2023-03-13**; control-member z-depth aggregation from **2022-06-29**
- **Scientific reference:** Geoscientific Model Development, 16, 5401 (2023) — cited in the files via `references`

---

## Ensemble configuration

**24 members, time-lagged across four six-hourly cycles.** Verified by enumerating the per-cycle member sets on 2026-07-27:

| Cycle | Members |
|---|---|
| **T00Z** | `m00` `m01` `m02` `m03` `m04` `m05` |
| **T06Z** | `m06` `m07` `m08` `m09` `m10` `m11` |
| **T12Z** | `m12` `m13` `m14` `m15` `m16` `m17` |
| **T18Z** | `m18` `m19` `m20` `m21` `m22` `m23` |

Six members per cycle, non-overlapping member IDs, composing a complete 24-member ensemble over a 24-hour production day. Each member file carries the full 96-hour forecast (97 hourly steps) from its own cycle time — so **members within one ensemble have initialisation times spread across 18 hours**, and their valid-time windows are staggered accordingly.

- **Control member:** `m00`, from the 00Z cycle. This is the member published in the `sdepth` and `zdepth` products.
- **Perturbation strategy:** not documented in the file metadata (**TBD**). The GMD reference is the place to look.
- **Nudging:** *"All members are weakly nudged towards TOQAZ5"* — per the catalog description; see *Forcing*.

> **The time-lagged structure is the single most important thing to get right here.** Treating `m00`–`m23` as a synchronous 24-member ensemble is wrong: `m23` is initialised 18 hours after `m00`. For a fixed valid time, later-cycle members have shorter lead times and will be systematically sharper. This matters for spread interpretation, and it is the same structural pattern documented for KNMI's HARMONIE-AROME time-lagged ensembles.

### Derived ensemble products
Two daily files aggregate all 24 members across the four cycles:

| File | Content | Size |
|---|---|---|
| `barents2500_his_sfc_<YYYYMMDD>_ensmean_96h.nc` | **Ensemble mean** | ~3.57 GB |
| `barents2500_his_sfc_<YYYYMMDD>_ensstd_96h.nc` | **Ensemble spread** (standard deviation) | ~3.57 GB |

Both are stamped with the **production day**, not a cycle — they cannot be produced until the 18Z members land. Observed publication for 2026-07-27: ensmean at 2026-07-28T00:39:43Z, ensstd at 00:49:59Z, i.e. roughly **+24.7 h after the 00Z cycle** of the day they describe.

NcML aggregation descriptors are also published, both per-cycle (`..._<YYYYMMDD>T<HH>Z_ens_96h.ncml`) and per-day (`..._<YYYYMMDD>_ens_96h.ncml`), plus a rolling `barents2500_his_sfc_ens_96h_latest.ncml`.

> **The `ensmean` and `ensstd` files carry a bare `title = "Barents-2.5km - ROMS"` and no ACDD block** — no `license`, no `time_coverage_*`, no `summary`. The individual member files carry the full metadata set including CC BY 4.0. If you consume only the derived products, you get no licence declaration.

---

## Forcing
- **Atmospheric forcing:** published in the output as `Uwind` / `Vwind`. MET Norway states the distribution includes *"atmospheric fields used for forcing"*. The source model is **not named in the file metadata (TBD)** — AROME-Arctic is the natural candidate for this domain but is not stated.
- **Ocean nudging:** **all members are weakly nudged towards [TOPAZ5](./topaz5.md)**, per the catalog description. This is the key structural link: Barents-2.5km EPS is a high-resolution downscaling constrained by the assimilating Arctic parent system, rather than running free.
- **Lateral boundary conditions:** not documented (**TBD**); the `sdepth` files carry a `boundary = 4` dimension, indicating boundary forcing is applied on all four edges.
- **Tidal forcing:** not documented (**TBD**)
- **River runoff:** not documented (**TBD**)
- **Initial conditions:** not documented (**TBD**)

---

## Coupling
- **Ocean ↔ sea ice:** genuine online two-way coupling via **METROMS**, linking ROMS v3.7 and CICE v5.1. This is a coupled prediction system, not an ocean model with prescribed ice.
- **Atmosphere:** one-way forcing; no coupled atmosphere.
- **Parent system:** weak nudging from [TOPAZ5](./topaz5.md) — one-way.

---

## Data assimilation
- **DA scheme:** none documented for this system. The ensemble is constrained by **weak nudging toward TOPAZ5**, which does run a 100-member EnKF. Barents-2.5km EPS therefore inherits observational constraint indirectly rather than assimilating observations itself.
- **Assimilated observations:** none documented (**TBD**)

*This is the same architectural pattern as [NorKyst v3](./norkyst-v3.md) — a high-resolution forecast nudged toward a coarser assimilating system — except that here MET Norway states the relationship explicitly rather than leaving it to be inferred.*

---

## What it provides

The system publishes three products with different member coverage, vertical treatment, and forecast length.

| Product | Members | Vertical | Forecast | Cycles/day |
|---|---|---|---|---|
| **surface** | **All 24** | Surface layer (0.5–1 m) | **96 h** | 4 |
| **sdepth** | `m00` only | **42 native sigma levels** | 24 h | 1 |
| **zdepth** | `m00` only | **16 fixed z-levels** | 24 h | 1 |

### Surface product variables (verified)
| Variable | Description |
|---|---|
| `temperature`, `salinity` | Surface layer ocean state |
| `u_eastward`, `v_northward` | **Geographic** current components |
| `w` | Vertical velocity |
| `zeta` | Sea surface height |
| `AKs` | Vertical salt diffusivity |
| `ice_concentration`, `ice_thickness` | Sea ice state |
| `ice_u`, `ice_v` | Sea ice velocity |
| `Uwind`, `Vwind` | Forcing wind components |
| `h`, `sea_mask`, `angle`, `lat`, `lon` | Static grid fields |

**Currents are pre-rotated to geographic east/north**, with `angle` also supplied — the same convenience as [NorKyst v3](./norkyst-v3.md) and [Norshelf](./norshelf.md), and unlike [TOPAZ5](./topaz5.md).

**Ice velocity is published** (`ice_u`/`ice_v`), which with hourly output and 2.5 km resolution makes this directly usable for ice drift and iceberg trajectory work — the search-and-rescue application MET Norway cites.

### sdepth product
Full native ROMS history output on the Arakawa C-grid with 42 sigma levels, including `boundary` and `tracer` dimensions. **~24.96 GB per analysis file, ~75.74 GB per forecast file.** A `barotropic/` sub-catalog is also present.

### zdepth product
16 fixed depth levels, 0–3000 m. ~3.04 GB per analysis file, ~9.26 GB per forecast file.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **CC BY 4.0** — `license = "https://spdx.org/licenses/CC-BY-4.0 (CC-BY-4.0)"`, declared in the individual member files. **Not declared in the `ensmean` / `ensstd` derived products** (see *Ensemble configuration*). MET Norway's server-wide free-data terms apply throughout.
- **Is the data downloadable?** Yes — direct HTTP and OPeNDAP
- **Data formats:** NetCDF-4 (CF-1.8 / ACDD-1.3), plus NcML aggregation descriptors
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Top-level catalog:** https://thredds.met.no/thredds/fou-hi/barents_eps.html

### 1. Surface ensemble
- **Catalog:** https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_surface/catalog.html
- **Structure:** `YYYY/MM/DD/` with `T00Z/`, `T06Z/`, `T12Z/`, `T18Z/` subdirectories
- **Member naming:** `barents_sfc_<YYYYMMDD>T<HH>Zm<NN>.nc` (~2.4–2.5 GB each)
- **Daily derived products** and NcML descriptors sit at the day level
- **Archive start (live-measured): 2023-03-13**
- A full 24-member day is roughly **60 GB**

### 2. sdepth (native sigma, control member)
- **Catalog:** https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_sdepth/catalog.html
- **Naming:** `barents_his_<YYYYMMDD>T00Zm00_AN.nc` (analysis, ~24.96 GB), `barents_his_<YYYYMMDD>T00Zm00_FC.nc` (forecast, ~75.74 GB)
- Flat listing, no year subdirectories; rolling window

### 3. zdepth (fixed depths, control member)
- **Catalog:** https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_zdepth/catalog.html
- **Naming:** `barents_zdepth_<YYYYMMDD>T00Zm00_AN.nc` (~3.04 GB), `barents_zdepth_m00_FC.nc` (~9.26 GB, fixed name — overwritten each cycle)

### 4. Best-estimate aggregations
| Aggregation | OPeNDAP path |
|---|---|
| sdepth hourly | `fou-hi/barents_eps_sdepth_be` |
| zdepth hourly | `fou-hi/barents_eps_zdepth_be` |

Base URL: `https://thredds.met.no/thredds/dodsC/<path>`

**zdepth aggregation, live-measured: 35,137 hourly steps, 2022-06-29T00:00Z → 2026-07-31T00:00Z** — just over four years, with ~696 missing hours (1.9%). Dimensions `X=739`, `Y=949`, `depth=16`.

*The `sdepth` aggregation did not return dimensions within a 190-second timeout — unsurprising given ~25 GB daily files over four years. Expect very slow first responses and subset aggressively.*

The zdepth aggregation predates the surface ensemble archive by nine months, indicating the control-member deterministic chain was running before the EPS surface products began publishing.

### Publication latency (live-measured, 2026-07-27)
| Product | Published | Latency |
|---|---|---|
| Surface members, T00Z | `date_created` 08:08 UTC | **+8 h 08 m** |
| Surface members, T18Z | 23:24 – 2026-07-28T00:28 UTC | **+5.4 to +6.5 h** |
| zdepth `_AN` / `_FC` | 08:13 UTC | **+8 h 13 m** |
| sdepth `_AN` | 08:15 UTC | **+8 h 15 m** |
| `ensmean` (day 07-27) | 2026-07-28T00:39 UTC | **+24 h 39 m** |
| `ensstd` (day 07-27) | 2026-07-28T00:49 UTC | **+24 h 49 m** |

**The derived ensemble products lag by more than a day** — structurally unavoidable, since they need the 18Z members. Applications needing ensemble statistics in near-real-time must compute them from the per-cycle members as they arrive.

### Terms of service
MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. With 24 members at ~2.5 GB each per day plus ~100 GB of sdepth output, this is a hard constraint — OPeNDAP subsetting rather than bulk download is essential. Status: https://status.met.no

---

## Notes

- **The legacy `barents25` catalog is frozen.** The summary attribute in every current file directs users to `https://thredds.met.no/thredds/fou-hi/barents25.html` for *"previous analysis as well as the latest forecast."* That catalog (`barents25km_files`) holds **809 files whose newest is `Barents-2.5km_ZDEPTHS_his.an.2022112006.nc` — 20 November 2022**, nearly four years stale. It is a v1-era archive, not a live channel. The in-file pointer is misleading; use the `barents_eps` catalogs documented here.

- **Time-lagged ensemble, not synchronous.** See *Ensemble configuration*. The most likely source of misinterpretation.

- **Three products, three different member/vertical/lead-time combinations.** Only the surface product is an ensemble; `sdepth` and `zdepth` are control-member only and stop at 24 hours. Anyone wanting subsurface ensemble output will not find it here.

- **Sea surface height reference frame is not stated** for `zeta` in the surface files (**TBD**).

- **Derived products lack ACDD metadata and licence declarations.** See *Ensemble configuration*.

- **Relationship to MET Norway's other marine systems.** Nudged toward [TOPAZ5](./topaz5.md), which supplies the assimilated Arctic parent state. Distinct from [NorKyst v3](./norkyst-v3.md) (Norwegian coast, 800 m, no ice) and [Norshelf](./norshelf.md) (shelf, 2.4 km, 4D-Var, no ice) in both domain and the presence of an online sea ice component. On the wave side see [ARCWAM](../../../wave_models/regional/norway/arcwam.md) and [MyWaveWAM3km](../../../wave_models/regional/norway/mywavewam3km.md), which cover the same Arctic waters. MET Norway's `fou-hi` catalog also carries **AICE**, not yet documented here — published evaluation benchmarks the data-driven AICE system against both Barents-2.5km and TOPAZ5.

- **Repository placement.** Filed under `models/ocean_models/` using `ocean-model.template.md`, per the templates README's rule that `ensemble-model.template.md` is for atmospheric ensembles only. **The ocean template does not currently carry an optional "Ensemble configuration" section** the way the wave and surge templates do; this entry adds one by analogy. Worth adding to `ocean-model.template.md` — this is unlikely to be the last ocean ensemble in the catalog, and [NorKyst v3](./norkyst-v3.md) nominally needs one too.

- **Naming.** MET Norway uses "Barents-2.5km EPS" in the catalog heading, `Barents-2.5km v2.0` in file summaries, `barents_eps` in catalog paths, `barents_sfc` / `barents_his` / `barents_zdepth` in filenames, and `barents2500` in the derived products. This entry uses **Barents-2.5km EPS**.

---

## Version history

No formal version history is published. The following is reconstructed from file metadata and archive inspection.

### Current — Barents-2.5km v2.0
METROMS framework coupling ROMS v3.7 and CICE v5.1, 2.5 km, 42 levels, 24-member time-lagged EPS at 96 h. Documented in Geoscientific Model Development, 16, 5401 (2023).

### 2023-03-13 — Surface ensemble archive begins
First archived day in the `barents_eps_surface` tree.

### 2022-11-20 — v1 distribution ends
Last file in the legacy `barents25km_files` catalog.

### 2022-06-29 — Control-member z-depth record begins
Earliest timestamp in the `barents_eps_zdepth_be` aggregation, nine months before the surface ensemble archive.

---

## Official documentation
- Barents-2.5km EPS catalog: https://thredds.met.no/thredds/fou-hi/barents_eps.html
- Surface ensemble files: https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_surface/catalog.html
- sdepth files: https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_sdepth/catalog.html
- zdepth files: https://thredds.met.no/thredds/catalog/fou-hi/barents_eps_zdepth/catalog.html
- Legacy v1 archive (frozen at 2022-11-20): https://thredds.met.no/thredds/fou-hi/barents25.html
- MET Norway ocean model overview: https://ocean.met.no/models
- METROMS framework source: https://github.com/metno/metroms
- ROMS project: https://www.myroms.org
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/

### Key references
- **Röhrs, J., et al. (2023). Barents-2.5km v2.0: an operational data-assimilative coupled ocean and sea ice ensemble prediction model for the Barents Sea and Svalbard.** *Geoscientific Model Development*, 16, 5401–5426. https://gmd.copernicus.org/articles/16/5401/2023/ — cited directly in the files via the `references` attribute
- Hunke, E. C., et al. (2017). CICE: the Los Alamos Sea Ice Model, version 5.1.

> **Documentation note:** this is the best-documented system in the `fou-hi` tree. The catalog page specifies model version, framework, resolution, vertical layers, ensemble size, nudging, and the product split; the file summaries add the METROMS/ROMS/CICE component versions and a peer-reviewed reference. Atmospheric forcing source, perturbation strategy, lateral boundaries, and tidal treatment remain undocumented and are recorded as TBD above.
