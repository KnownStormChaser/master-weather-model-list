# National Water Model (NWM)

## What this model is
The **National Water Model** is NOAA's operational hydrologic analysis and forecast system for the United States. It simulates the land surface water budget — snow, soil moisture, runoff, groundwater — on a gridded land surface model and routes the resulting water down a vector river network. Its primary product is **river discharge (streamflow), in m³ s⁻¹, for about 3.2 million river reaches**, together with velocity, reservoir inflow/outflow and level, and gridded land surface and terrain routing fields.

**It forecasts discharge, not river stage.** Channel output carries no water level. The one exception is the separate **coastal total water level** component, which since v3.0 couples NWM river discharge into a SCHISM coastal ocean model and predicts water level along U.S. coasts. So the system does both: inland it is a discharge model; at the coast it publishes water level. See *What it provides*.

The NWM is an hourly cycling system run as many named **configurations** from shared cycles: hourly analyses, an hourly 18-hour short-range forecast, a 10-day medium-range ensemble and blend, and a 30-day long-range ensemble, across four domains (CONUS, south-central Alaska, Hawaii, Puerto Rico/U.S. Virgin Islands). The current operational release is **NWM v3.1**, built on WRF-Hydro **v5.4.0**.

---

## Who runs it
- **Organization:** NOAA / National Weather Service / Office of Water Prediction (OWP), with NWS/NCEP Central Operations (NCO) running the operational suite on WCOSS
- **Model core developer:** NCAR Research Applications Laboratory — WRF-Hydro community model, with the Noah-MP land surface model. The coastal component uses SCHISM (Virginia Institute of Marine Science).
- **Country / region:** United States

---

## What area it covers
- **Coverage:** National, across four separate domains
- **Domain details (live-verified from the 2026-11-18 file headers):**

| Domain | Land surface grid (1 km) | Terrain routing grid | Channel reaches | Reservoirs |
|---|---|---|---|---|
| CONUS | 4608 × 3840, Lambert conformal | 18432 × 15360 at 250 m | 2,776,734 | 5,782 |
| South-central Alaska | 879 × 459, polar stereographic | 3516 × 1836 at 250 m | 391,528 | 237 |
| Hawaii | 590 × 390, Lambert conformal | 5900 × 3900 at 100 m | 13,637 | 9 |
| Puerto Rico / USVI | 300 × 110, Lambert conformal | 3000 × 1100 at 100 m | 14,017 | 16 |

  - **CONUS** grid spans roughly 133.4°W–60.7°W, 20.1°N–57.6°N (grid corners). River modelling covers the full Great Lakes and Lake Champlain drainage basins, including their Canadian portions (since v2.1).
  - **Alaska** covers only south-central Alaska — Cook Inlet, the Copper River Basin and Prince William Sound — about 157.5°W–139.9°W, 58.4°N–64.1°N. Most of the state, including the Yukon basin, is outside the domain.
- **Total reach count:** 3,195,916 across the four domains (live-verified). The v3.1 notice describes this as "over 3.2 million"; it is just under.
- **Excluded areas:** Most of Alaska (above); Guam and other Pacific territories; the coastal total water level component excludes Alaska entirely.

---

## Basic details
- **Model type:** Deterministic + ensemble hydrological model
- **Core hydrological model:** WRF-Hydro (`code_version = v5.4.0`, live-verified) with the Noah-MP land surface model. The coastal component is SCHISM (`code_version = SCHISM v5.10`).
- **Routing scheme:** Diffusive-wave surface overland flow and saturated subsurface flow routing on the 250 m (100 m in Hawaii and Puerto Rico) terrain grid; **Muskingum-Cunge** channel routing on the reach network; level-pool reservoir routing plus persistence and RFC-forecast reservoir treatments (see *Grid and river network*). A Xinanjiang surface runoff scheme was added at v3.0.
- **Forecast length / update frequency:** by configuration —

| Configuration | Domain | Cycles | Length | Members |
|---|---|---|---|---|
| Analysis & assimilation (`analysis_assim`) | CONUS, Alaska, PR | hourly | 3 h look-back (`tm00`–`tm02`) | — |
| | Hawaii | hourly | 3 h look-back at 15 min (`tm0000`–`tm0245`) | — |
| Extended analysis (`analysis_assim_extend`) | CONUS | 1× daily, 16Z | 28 h look-back | — |
| | Alaska | 1× daily, 20Z | 32 h look-back | — |
| Long-range analysis (`analysis_assim_long`) | CONUS | 4× daily | 12 h look-back | — |
| Short range | CONUS | hourly | 18 h | deterministic |
| | Alaska | every 3 h | 15 h (00/06/12/18Z), 45 h (03/09/15/21Z) | deterministic |
| | Hawaii | 2× daily (00, 12Z) | 48 h | deterministic |
| | Puerto Rico | 4× daily | 48 h | deterministic |
| Medium range (`medium_range_memN`) | CONUS, Alaska | 4× daily | member 1: 240 h; members 2–6: 204 h | 6 (time-lagged GFS) |
| Medium range blend | CONUS, Alaska | 4× daily | 240 h | deterministic |
| Long range (`long_range_memN`) | CONUS | 4× daily | 720 h (30 days) | 4 per cycle, 16 per day |
| Coastal analysis (`analysis_assim_coastal`) | Atlantic/Gulf, Pacific, Hawaii, Puerto Rico | hourly | 3 h look-back | — |
| Coastal extended analysis | Atlantic/Gulf, Pacific | 1× daily, 16Z | 28 h look-back | — |
| Coastal short range | Atlantic/Gulf, Pacific | hourly | 18 h | deterministic |
| | Hawaii | 2× daily | 48 h | deterministic |
| | Puerto Rico | 4× daily | 48 h | deterministic |
| Coastal medium range (+ blend) | Atlantic/Gulf, Pacific only | 4× daily | 120 h | deterministic |

  `_no_da` (open-loop) variants exist for most analysis, short-range (Hawaii, Puerto Rico) and medium-range configurations; see *Initialization*. The medium-range open-loop runs (`medium_range_no_da`, CONUS and Alaska) are single deterministic 240 h forecasts started from the open-loop analysis, publishing channel output only, every 3 hours. Extended analyses initialise the standard analysis once a day; P-Surge-forced coastal runs keep the lengths of their STOFS-only counterparts (18 h and 120 h).
- **Temporal output resolution:** hourly for most channel, reservoir and short-range land/terrain output; **15 min** for Hawaii channel, reservoir and terrain output; 3-hourly for medium-range land and terrain and for `medium_range_no_da` channel; 6-hourly channel and daily land for long range. Analysis and short-range land/terrain is hourly.
- **File naming:** `nwm.tCCz.<configuration>.<output_type>[_N].<step>.<domain>.nc`, where `<step>` is `fHHH` (forecast hour) or `tmHH` (hours before the cycle time). Hawaii uses five-digit steps, `fHHHMM` / `tmHHMM`, encoding hours and minutes (e.g. `f00015` is +15 min, `f04800` is +48 h).

---

## Grid and river network
- **Land surface grid:** 1 km on all four domains, on a spherical-earth projection (`a = b = 6,370,000 m`). Lambert conformal for CONUS (standard parallels 30°/60°N, origin 40°N 97°W), Hawaii and Puerto Rico; polar stereographic for Alaska (60°N true latitude, 135°W).
- **Routing geometry:** Hybrid. Gridded overland and subsurface routing on the terrain grid (250 m CONUS and Alaska, 100 m Hawaii and Puerto Rico), and reach-based channel routing on a vector network.
- **Routing network size:** 3,195,916 reaches in total — 2,776,734 CONUS, 391,528 Alaska, 14,017 Puerto Rico, 13,637 Hawaii (live-verified `feature_id` dimensions).
- **Hydrography / DEM source:** NHDPlus Version 2 within CONUS. The `feature_id` attribute in every channel file reads "NHDPlusv2 ComIDs within CONUS, arbitrary Reach IDs outside of CONUS".
- **Hydrofabric changes at v3.1:** location refinements for 311 USGS gauges, eight reaches with altered flow paths, about 80 gauges added and one dropped (listed in the SCN appendix). The **Old River Control Structure** diversion between the Mississippi and Atchafalaya is now represented, by persisting the last observed flow at gauge 07381482 from reach 19269940 to reach 19406874.
- **Reservoir and lake representation:** **Yes.** Reservoir files carry inflow, outflow, water surface elevation and a `reservoir_type` flag. Live-verified on the 2026-11-18 12Z short-range run (CONUS): 5,422 level-pool, 57 USGS-persistence, and **303 with `reservoir_type` missing** (−9999). The flag table declares only types 1 (`Level_pool`) and 2 (`USGS-persistence`). OWP documents RFC-supplied reservoir outflow forecasts at several hundred locations (392 at v3.0), which may be the undeclared group (TBD). Alaska's file also contains **two reservoirs of type 5**, which the flag table does not define. Long-range files report all CONUS reservoirs as level pool.

---

## Meteorological forcing
Forcing is regridded to the 1 km land grid and published (see *What it provides*). Documented sources by configuration, per OWP's (v3.0-era) technical page and the v3.0/v3.1 notices:

- **CONUS analysis:** MRMS radar and gauge-adjusted QPE, RAP and HRRR. **Extended analysis:** Stage IV RFC multi-sensor precipitation, RAP and HRRR.
- **CONUS short range:** [HRRR](../../../nwp_models/regional/usa/hrrr.md) and RAP. MRMS and NWP precipitation-type information added at v3.1.
- **CONUS medium range:** GFS, as a six-member time-lagged ensemble. **Medium range blend:** NBM precipitation with GFS for other fields.
- **CONUS long range:** CFS forecast members.
- **Alaska:** HRRR-Alaska and NBM (short range), GFS (medium range), Alaska-Pacific RFC precipitation (extended analysis), MRMS (analysis).
- **Hawaii:** MRMS (analysis); NAM-Nest radiation with WRF-ARW for other fields (short range).
- **Puerto Rico / USVI:** MRMS (analysis); NAM-Nest radiation with WRF-ARW for other fields (short range), with **NBM replacing WRF-ARW precipitation from v3.1**.
- **Bias correction / downscaling:** v3.1 introduced dynamic lapse-rate temperature downscaling based on NWP data (CONUS). Earlier releases document bias correction and downscaling without specifics (TBD).

> **Forcing documentation is out of date for Hawaii and Puerto Rico.** NAM-Nest and the WRF-ARW HiresW runs were retired on 3 November 2026 (SCN 26-47), but neither the retirement notice nor any NWM notice mentions NWM. Hawaii and Puerto Rico forcing files are still produced on 2026-11-18, but the forcing files carry no source metadata. What drives them since 3 November is undocumented (TBD).

---

## Initialization and antecedent state
- **Initial state source:** Continuous, self-cycling analysis runs forced by observed meteorology (the `analysis_assim*` configurations). Forecasts start from the analysis state; CONUS long range starts from the separate 12-hour long-range analysis. The CONUS 19Z standard analysis restarts from the daily extended analysis.
- **Assimilates discharge observations:** **Yes.**
- **Observation sources:** USGS streamflow (all analysis configurations); USACE and RFC data in the CONUS standard and extended analyses. The 15-minute USGS observations used are published as `usgs_timeslices/` (8,639 stations per 15-minute file on 2026-11-18).
- **Method / cadence:** Nudging of channel flow towards observations, published as the `nudge` variable ("Amount of stream flow alteration"). On the 2026-11-18 12Z CONUS analysis, 5,631 reaches carry a non-zero nudge.
- **New at v3.1 — forecast assimilation:** USGS observations available when a forecast starts are now assimilated into its first hours (short, medium and long range), so the start of each forecast tracks observed flow.
- **No-DA variant published?** **Yes**, for most analysis, short-range (Hawaii, Puerto Rico) and medium-range configurations (`*_no_da`). These "open-loop" runs skip all assimilation. In the 2026-11-18 12Z open-loop CONUS analysis, `nudge` is zero at every reach and `qBtmVertRunoff` is not published. Use `_no_da` for verifying the model's hydrology; use the standard configurations for guidance.

---

## Ensemble configuration
- **Ensemble size:**
  - **Medium range:** 6 members per cycle (CONUS and Alaska). Member 1 runs to 240 h; members 2–6 run to 204 h.
  - **Long range:** 4 members per cycle at 4 cycles a day, i.e. a 16-member time-lagged daily ensemble (CONUS only).
- **Source of perturbations:** Inherited from the driving atmosphere. Medium-range members are a **time-lagged GFS** ensemble; long-range members come from CFS. Neither the hydrology nor its initial state is perturbed.
- **Resolution / output differences:** Members share the deterministic grids and network. Output cadence differs: medium-range channel output is hourly, land/terrain 3-hourly; long-range channel 6-hourly, land daily, with no terrain output.
- **Member packaging:** One directory per member (`medium_range_mem1` … `mem6`, `long_range_mem1` … `mem4`), with the member number in the output-type name (`channel_rt_3`, `land_3`).
- **Derived products distributed:** None as ensemble statistics. The medium-range **blend** is a separate deterministic configuration, not an ensemble mean.

---

## What it provides

**Channel output** (`channel_rt`, reach-based, per `feature_id`): `streamflow` (m³ s⁻¹), `velocity`, `nudge`, `qSfcLatRunoff` (lateral inflow from terrain routing), `qBucket` (groundwater bucket flux), `qBtmVertRunoff`. Long-range files carry only `streamflow`, `nudge` and `velocity`.

**Reservoir output** (`reservoir`, per lake): `inflow`, `outflow`, `water_sfc_elev`, `reservoir_type`, `reservoir_assimilated_value`, latitude/longitude.

**Land surface output** (`land`, gridded 1 km). **The variable set differs by configuration** (live-verified):
- Short range: snow depth, SWE, snow-cover fraction, accumulated ET, top-layer soil saturation, snow temperature (6 variables)
- Analysis: 15 variables, adding soil moisture and temperature by layer, soil ice, snowmelt, rain and snow rates
- Medium range: 23 variables, adding surface energy fluxes, canopy water, radiative temperature and underground runoff
- Long range: 8 variables, including surface and underground runoff, full soil saturation and canopy water

**Terrain routing output** (`terrain_rt`, gridded 250 m or 100 m): `sfcheadsubrt` (ponded surface water depth) and `zwattablrt` (depth to soil saturation). Not produced for long range.

**Forcing** (`forcing`, gridded 1 km): 10 m wind components, 2 m temperature and specific humidity, surface pressure, downward shortwave and longwave radiation, precipitation rate, and (new at v3.1, CONUS) `LQFRAC` liquid precipitation fraction. Published per configuration, including medium range and blend.

**Coastal total water level** (`total_water`, SCHISM unstructured-mesh nodes): `elevation` (water level), one time step per file, plus SHEF station bulletins.

| Coastal domain | Mesh nodes | Extent (live-verified) | Datum |
|---|---|---|---|
| `atlgulf` | 10,481,055 | 98.0°W–66.8°W, 24.5°N–45.2°N | **NAVD88** |
| `pacific` | 2,729,500 | 124.8°W–116.9°W, 32.0°N–49.0°N (U.S. West Coast only) | **NAVD88** |
| `hawaii` | 721,451 | main Hawaiian Islands | **LMSL** |
| `puertorico` | 3,104,133 | Puerto Rico and USVI | **LMSL** |

  The datum is declared per file in `geopotential_datum_name`. Oceanic boundary forcing comes from **STOFS** — since v3.1 the bias-corrected [STOFS-2D-Global](../../../storm_surge_models/global/usa/stofs-2d-global.md) — and, for tropical events, from [P-Surge](../../../storm_surge_models/regional/usa/psurge.md).

  **P-Surge-forced coastal runs are event-driven.** Directories ending `_psurge` (Atlantic/Gulf short range, medium range and blend) appear only when P-Surge runs: on 6 days between 2026-08-18 and 2026-11-18 (31 Aug–1 Sep and 7–10 Oct). The new P-Surge-forced Puerto Rico short range announced in the v3.1 notice (`short_range_coastal_puertorico_psurge/`) has **not yet appeared** in that period.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD). NOAA requests attribution for use or dissemination of unaltered data, prohibits stating or implying NOAA endorsement, and prohibits presenting modified data as original unaltered NOAA data. The same data on Google Cloud and Microsoft Azure is mirrored under NODD.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Reach-based vector output (channel, reservoir) and genuinely gridded output (land, terrain routing and forcing), plus unstructured-mesh coastal water level and SHEF station bulletins.
- **Real-time availability:** Current, no embargo. Most configurations appear on NOMADS within about 1–2 hours of cycle time. Long-range output lags enough that a cycle can land in the **next** day's directory, so look back one directory for it.
- **Data formats:** NetCDF-4 (CF-1.6 for model output, CF-1.5 for forcing); SHEF text bulletins for coastal stations. Most variables are packed as scaled integers (`scale_factor`/`add_offset`).
- **River network metadata:** Channel files contain **no reach coordinates.** Their `streamflow` variable names `latitude longitude` as coordinates, but no such variables exist in the file. Reach geometry is distributed separately as an ESRI file geodatabase: https://www.nohrsc.noaa.gov/pub/staff/keicher/NWM_live/web/data_tools/NWM_channel_hydrofabric.tar.gz. Reservoir files do carry latitude and longitude.

### Official download locations

**NOMADS (operational, about 2-day retention):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/nwm/prod/nwm.YYYYMMDD/<configuration>/
```
`nwm/v3.1/` is a version-named alias of `prod/` (identical directory listings on 2026-11-18). `nwm/v3.0/` holds only two empty day directories left from the cutover (2026-08-17 and 2026-08-18), and the NOMADS parallel feed `nwm/para/` now returns HTTP 403.

**AWS Open Data (NODD):**
```
s3://noaa-nwm-pds/nwm.YYYYMMDD/
aws s3 ls --no-sign-request s3://noaa-nwm-pds/
```
- Region `us-east-1`; SNS topic `arn:aws:sns:us-east-1:123901341784:NewNWMObject` (Lambda and SQS only)
- **Archive depth (live-verified 2026-11-18):** 687 daily directories from **2025-01-01** to present, with no gaps. The AWS registry describes the bucket as a "rolling four week archive", which the live bucket contradicts. Older directories were rewritten in place (e.g. the 2025-01-01 files carry March 2025 timestamps), and it is unclear whether a rolling deletion is still planned (TBD).
- Kerchunk/Zarr reference files for six configurations (CONUS, Alaska, Hawaii, Puerto Rico short range; CONUS and Alaska medium-range member 1) are in `s3://noaa-nodd-kerchunk-pds/nwm/`, maintained by RPS Tetra Tech.

**Google Cloud (NODD):**
```
gs://national-water-model/nwm.YYYYMMDD/
https://storage.googleapis.com/national-water-model/nwm.YYYYMMDD/...
```
- **Archive depth (live-verified 2026-11-18):** 2,985 daily directories from **2018-09-17** to present, with no gaps — **the longest archive of the four channels**, spanning NWM v1.2 to v3.1.

**Microsoft Azure (NODD / Planetary Computer):**
```
https://noaanwm.blob.core.windows.net/nwm/nwm.YYYYMMDD/
```
- **Archive depth (live-verified 2026-11-18):** 1,895 daily directories from **2021-08-26** to present, with 16 days missing (2024-07-19 to 07-21 and 2024-12-23 to 2025-01-04).

> **Live-verified — the three cloud mirrors carry the same data.** For 2026-11-17, AWS and Google both hold **131,603 files totalling 1,232.4 GB**. Azure holds 131,507 files (96 fewer, 1,232.2 GB). About 121 GB of each day is post-processed subsets (see *Notes*). The largest raw components are `forcing_medium_range` (151 GB per day) and the six medium-range members (89–105 GB each).

---

## Notes
- **Operational status:** v3.1 operational since **18 August 2026**. SCN 26-64 (Updated) gave 12Z on 18 August; the first real-time v3.1 cycle on S3 is **16Z on 18 August**.

> **Live-verified — the S3 archive mixes v3.0 and v3.1 output around the cutover.** Many cycles from 17 August 00Z to 18 August 15Z were rewritten on 18 August as **v3.1 backfill**, while others kept their original **v3.0** files. On 17 August, for example, 01Z, 12Z, 17Z and 23Z short-range CONUS output is v3.0; the other 20 cycles are v3.1, written 18 August 18:49–19:12 UTC. On 18 August, 06Z–09Z and 11Z are v3.0 and 00Z–05Z, 10Z and 12Z–15Z are v3.1. Read `NWM_version_number` per file rather than assuming a single cutover time.

- **Configuration sprawl:** 68 configuration directories on a complete day (2026-11-17), plus `post_processed/` and `usgs_timeslices/`, and occasional event-driven `_psurge` coastal directories. This entry covers all of the model configurations. Directory names, not file names, distinguish domains for some products (e.g. `short_range_alaska/` holds `*.alaska.nc`), and the medium-range member number appears in both the directory and the output type.
- **Units and conventions:**
  - Times are `minutes since 1970-01-01 00:00:00 UTC`.
  - Precipitation in forcing files is a **rate**, `RAINRATE` in `mm s^-1` (not a udunits-standard string), with `cell_methods = time: mean`; other forcing fields are instantaneous.
  - `ACCET` (evapotranspiration) and similar `ACC*` land variables are accumulations from model start, not per-step totals.
  - `qBtmVertRunoff` is a volume (m³), not a flux, despite sitting beside fluxes in m³ s⁻¹.
- **Channel-file CRS metadata contradicts itself.** The global `proj4` attribute gives the Lambert conformal land-grid projection, while the `crs` variable describes WGS84 latitude–longitude. Neither applies to the reach output, which has no coordinates (see *Data availability*).
- **Coastal output needs sanity bounds.** On the 2026-11-18 12Z f001 files:
  - Pacific: water level spans −315.47 m to +321.96 m, with 108 nodes below −10 m.
  - Atlantic/Gulf: maximum 278.56 m.
  - Most values above 20 m are on inland river nodes, which sit well above the datum (99th percentile 21.3 m Atlantic/Gulf, 28.1 m Pacific). The extreme negative values are numerical artifacts.
  - Fewer than half the mesh nodes carry data in any file (e.g. 5,215,405 of 10,481,055 in Atlantic/Gulf); the rest are dry or masked.
- **Coastal SHEF bulletins** are in **feet above NAVD88** for the Atlantic/Gulf (header-verified), and mix several SHEF physical-element codes (`HG`, `HM`, `HC`, `HP`) for the same station. The 2026-11-18 12Z Atlantic/Gulf bulletin has 790 blocks for 516 stations.
- **Scope note — coastal water level.** Under this repository's template rule, the coastal `total_water` component is a storm-surge-type product (hydrodynamic water level), filed here because it is an NWM configuration family sharing cycles, forcing, distribution and versioning with the hydrology. It is comparable to [STOFS-3D-Atlantic](../../../storm_surge_models/regional/usa/stofs-3d-atlantic.md) and [STOFS-3D-Pacific](../../../storm_surge_models/regional/usa/stofs-3d-pacific.md), which also run SCHISM with NWM river inflow. Differences:
  - This one is driven by STOFS at its boundary.
  - It runs hourly rather than daily.
  - It publishes only water level, on NAVD88 (CONUS) or LMSL (islands).
- **Post-processed subsets:**
  - `post_processed/RFC/` holds per-River-Forecast-Center subsets of channel, land and forcing output in NetCDF (13 RFC directories; Alaska and Hawaii under `AP`, Puerto Rico under `SE`).
  - `post_processed/WMS/` holds reformatted fields behind OWP's map services.
  - These are subsets and derivations of the raw output, not separate model runs.
  - The older top-level `nwm/post-processed/` tree on NOMADS stopped updating at the v3.1 cutover.
  - Pre-rendered PNG images were removed at v3.1.
- **`usgs_timeslices/`** contains the 15-minute USGS discharge observations used for assimilation. Observations, not model output.
- **The NOMADS dataset description is also stale.** It is the v3.0-era text, and it differs from the live system in three places:
  - It describes 10-day coastal medium-range forecasts for CONUS, Hawaii and Puerto Rico. Live, coastal medium range runs only for the Atlantic/Gulf and Pacific domains, to 120 h (5 days since v3.1). **No Hawaii or Puerto Rico coastal medium-range directory appears on S3 in samples from January 2025, June 2026 or August 2026, or since v3.1.**
  - It describes the Alaska medium range as a single deterministic 240 h forecast. Live, it is a six-member ensemble (member 1 to 240 h, members 2–6 to 204 h) plus a 240 h blend.
  - It omits the Alaska extended analysis, the Alaska and coastal blends, and the v3.1 Puerto Rico P-Surge configuration.
- **OWP's technical page describes v3.0, not v3.1, and contradicts the live configuration.** It gives:
  - Puerto Rico short range at 06/18Z only (live: four cycles);
  - the Hawaii open-loop short range at 00/18Z (live: 00/12Z);
  - coastal medium range at 10 days (live: 5 days, since v3.1);
  - Alaska medium range as deterministic in one place (live: six members).
- **Relationship to other systems:**
  - Atmospheric drivers: [HRRR](../../../nwp_models/regional/usa/hrrr.md), RAP, GFS, NBM, CFS, MRMS (see *Meteorological forcing*).
  - Downstream: [STOFS-3D-Atlantic](../../../storm_surge_models/regional/usa/stofs-3d-atlantic.md) (one-way NWM coupling for river inflow and overland flow) and [STOFS-3D-Pacific](../../../storm_surge_models/regional/usa/stofs-3d-pacific.md) (river forcing alongside GloFAS and gauges).
  - Upstream for the coastal component: [STOFS-2D-Global](../../../storm_surge_models/global/usa/stofs-2d-global.md) (ocean boundary, bias-corrected since v3.1) and [P-Surge](../../../storm_surge_models/regional/usa/psurge.md) (tropical events).
  - The NWM retrospective simulations (multi-decade reanalysis runs) are historical-only and out of scope.

---

## Recent version history
- **v3.1 — 18 August 2026** (SCN 26-64 Updated; first set for 12 August):
  - **CONUS:** Old River Control Structure diversion; dynamic lapse-rate temperature downscaling; MRMS/NWP precipitation type; 25 new SHEF locations; hydrofabric refinements at 311 gauges; bias-corrected STOFS-2D boundary forcing and updated topobathy for the coastal model.
  - **Coastal:** medium-range coastal forecasts cut from 10 to 5 days.
  - **Puerto Rico:** new 00Z and 12Z cycles; NBM replaces WRF-ARW precipitation; P-Surge-forced coastal short range.
  - **All domains:** USGS observations assimilated into the start of forecasts; faster coastal model runtime.
  - **Output:** new `LQFRAC` forcing variable; post-processed directories reorganised.
- **v3.0 — 20 September 2023** (SCN 23-76):
  - **Coastal:** coastal total water level via SCHISM, forced by STOFS and P-Surge.
  - **Domains:** expansion to south-central Alaska.
  - **Forcing:** NBM forcing for CONUS medium range and Alaska; MRMS for Puerto Rico analysis.
  - **Reservoirs:** RFC reservoir outflow forecasts at 392 sites.
  - **Runoff:** Xinanjiang runoff scheme; recalibration.
- **v2.1 — 20 April 2021** (SCN 20-119):
  - **Domains:** first Puerto Rico/USVI domain; Great Lakes and Lake Champlain basins.
  - **Reservoirs:** USGS/USACE reservoir persistence and RFC reservoir forecasts.
  - **Configurations:** first open-loop (`_no_da`) configurations.
  - **Output:** hourly medium-range streamflow output.
- **v1.2 — 6 March 2018** (SCN 18-16).
- Earlier versions: v1.0 (2016) and v1.1, then v2.0 (2019) (exact dates TBD).

---

## Official documentation
- OWP NWM technical overview (describes v3.0): https://water.noaa.gov/about/nwm
- SCN 26-64 (Updated) — NWM v3.1: https://www.weather.gov/media/notification/pdf_2026/scn26-64_Updated_National_Water_Model_V3.1_aaa.pdf
- SCN 26-64 (original, 12 August date): https://www.weather.gov/media/notification/pdf_2026/scn26-64_National_Water_Model_V3.1.pdf
- PNS 25-77 — NWM v3.1 proposal: https://www.weather.gov/media/notification/pdf_2025/pns25-77_nwm_v3.1.pdf
- SCN 23-76 (Updated) — NWM v3.0: https://www.weather.gov/media/notification/pdf_2023_24/scn23-76_national_water_model_v3.0_aab.pdf
- SCN 20-119 (Updated) — NWM v2.1: https://www.weather.gov/media/notification/pdf2/scn20-119nwm_v2.1_aad.pdf
- SCN 18-16 — NWM v1.2: https://www.weather.gov/media/notification/pdfs/scn18-16national_water_model.pdf
- NCO product inventory: https://www.nco.ncep.noaa.gov/pmb/products/nwm/
- Channel hydrofabric (reach geometry): https://www.nohrsc.noaa.gov/pub/staff/keicher/NWM_live/web/data_tools/NWM_channel_hydrofabric.tar.gz
- NOMADS: https://nomads.ncep.noaa.gov/pub/data/nccf/com/nwm/prod/ (the linked NOMADS dataset description is v3.0-era text; see *Notes*)
- AWS Open Data Registry: https://registry.opendata.aws/noaa-nwm-pds/
- Google Cloud dataset: https://console.cloud.google.com/marketplace/details/noaa-public/national-water-model
- Microsoft Planetary Computer: https://planetarycomputer.microsoft.com/dataset/storage/noaa-nwm
