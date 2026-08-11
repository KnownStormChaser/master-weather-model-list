# <MODEL NAME>

## What this model is
<Plain-language description of the hydrological system and what it forecasts. **State clearly whether it predicts river discharge, water level, or both** — this is the single most common source of confusion in hydrological products, and it also determines whether the entry belongs here or under `storm_surge_models/`. A system that solves depth-averaged hydrodynamics to predict water level and current in a channel or estuary is a surge model in everything but name; a system that generates runoff from a land surface and routes it as discharge is a hydrological model. If it does both, say so.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Global / Continental / National / Basin>
- **Domain details (optional):** <bounds / named basins / river network extent>
- **Excluded areas (optional):** <e.g., glaciated catchments, endorheic basins, drainage areas below a minimum threshold — many systems silently drop small catchments> (TBD)

---

## Basic details
- **Model type:** <Deterministic hydrological model / Ensemble hydrological model / Deterministic + ensemble>
- **Core hydrological model:** <e.g., LISFLOOD / WRF-Hydro / MESH / GEM-Hydro / mHM / VIC / in-house> (TBD)
- **Routing scheme:** <e.g., kinematic wave, Muskingum-Cunge, diffusive wave; note whether reservoirs and lakes are represented> (TBD)
- **Forecast length:** <e.g., 30 days / 10 days / 215 days>
- **Update frequency / cycles:** <e.g., 1× daily (00 UTC) / 4× daily / monthly initialization>
- **Temporal output resolution:** <e.g., 1 h / 3 h / 6 h / 24 h> (TBD)

---

## Grid and river network
<Hydrological systems carry two distinct geometries — the land surface grid where water is generated, and the routing network where it is moved. They frequently differ in resolution and sometimes in type. Record both.>

- **Land surface grid:** <resolution and projection, e.g., 0.05° regular lat/lon / 1 km Lambert conformal> (TBD)
- **Routing geometry:** <Gridded routing on the same or a finer grid / reach-based vector network / hybrid> (TBD)
- **Routing network size (if reach-based):** <e.g., ~2.7 M river reaches> (TBD)
- **Hydrography / DEM source:** <e.g., MERIT Hydro, HydroSHEDS, NHDPlus HR, national hydrographic dataset> (TBD)
- **Catchment delineation (optional):** <number of catchments or sub-basins, and how they map to routing elements> (TBD)
- **Reservoir and lake representation:** <Yes / No — and whether reservoir operations are simulated, prescribed, or ignored> (TBD)

---

## Meteorological forcing
- **Driving atmospheric model:** <name the NWP, ensemble, or seasonal system and cross-link its entry> (TBD)
- **Forcing blend (optional):** <many systems splice observed or analysed precipitation into the near term before switching to forecast forcing — state the splice point if so> (TBD)
- **Bias correction / downscaling:** <method applied to the meteorological forcing before it reaches the hydrological model> (TBD)
- **Observed precipitation input (optional):** <gauge, radar, or satellite product used in the analysis or spinup period> (TBD)

---

## Initialization and antecedent state
<More consequential here than in most model classes. Hydrological skill at short lead times is dominated by the initial state of soil moisture, snowpack and channel storage rather than by the meteorological forecast, so how the system is initialized deserves its own section.>

- **Initial state source:** <e.g., continuous run forced by observed meteorology; a separate analysis/assimilation configuration; cold start> (TBD)
- **Assimilates discharge observations:** <Yes / No> (TBD)
- **Observation sources (if yes):** <gauge networks, satellite altimetry, snow products; name them if documented> (TBD)
- **Method / cadence (optional):** <e.g., nudging, ensemble Kalman filter, daily cycle> (TBD)
- **No-DA variant published?** <Some systems publish a parallel configuration with assimilation disabled — note it and explain which one is appropriate for verification> (TBD)

---

## Ensemble configuration (ensemble systems only)

<**Delete this entire section for deterministic entries.** Do not leave it in place filled with TBDs — an empty ensemble block in a deterministic entry reads as missing research rather than as "not applicable.">

- **Ensemble size:** <e.g., 51 members / 4 members> (TBD)
- **Source of perturbations:** <usually inherited from the driving atmospheric ensemble — name it; state explicitly if the hydrological model or its initial state is also perturbed> (TBD)
- **Resolution / output differences vs deterministic sibling:** (TBD)
- **Member packaging:** <separate file or directory per member vs member dimension in one file> (TBD)
- **Derived products distributed:** <ensemble mean, exceedance probabilities against return-period thresholds, percentiles — list only what is published as raw data> (TBD)

---

## Long-range configuration (sub-seasonal and seasonal systems only)

<**Delete this entire section for medium-range entries.** Retain it for seasonal or sub-seasonal hydrological systems, which are filed here beside their medium-range siblings under the phenomenon-over-forecast-range convention rather than under `climate_models/`. Cross-link the driving seasonal system's entry in `climate_models/` rather than duplicating its configuration here.>

- **Driving long-range system:** <cross-link the entry, e.g., SEAS5> (TBD)
- **Initialization cadence:** <e.g., 1st of each month> (TBD)
- **Hindcast (reforecast) period:** <e.g., 1993–2016> (TBD)
- **Reference climatology period:** <period used for anomaly and tercile products, if stated separately> (TBD)
- **Hindcasts distributed alongside forecasts?** <Yes / No / Separate dataset> (TBD)
- **Sources of predictability (optional):** <ENSO, snowpack, soil moisture memory, ocean heat content — hydrological seasonal skill often leans more on initial storage than on atmospheric predictability, which is worth stating where documented> (TBD)

---

## What it provides
Hydrological forecasts including (as available):
- river discharge
- river water level or stage
- soil moisture / soil wetness index
- snow water equivalent
- surface and subsurface runoff
- ponded water depth
- reservoir inflow, outflow, or storage
- flood exceedance probabilities against return-period thresholds
- anomalies relative to model climatology (long-range systems)

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Public domain (U.S. government work; CC0-equivalent) / Open Government Licence – Canada / CC BY 4.0 / Copernicus licence (registration and licence acceptance required)> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Output geometry:** <Gridded fields / reach-based vector output on a river network / station (point) time series at gauge locations / a combination> — see the scope note below
- **Real-time availability:** <state plainly whether the public feed is current or embargoed, and by how much — some systems restrict real-time access to partner institutions and release openly only after a delay> (TBD)
- **Data formats:** <NetCDF / GRIB2 / GeoTIFF> (note CF conventions if relevant)
- **River network metadata (if reach-based):** <URL or description of the reach ID and geometry files needed to interpret the output> (TBD)
- **Official download location:**
  <URL>
- **Access route notes (optional):** <e.g., cloud object store vs agency HTTPS; API and registration requirements; which channel is more reliable in practice>

> **Scope note.** Hydrological systems commonly publish channel output on a vector river network — one value per reach or per gauge, indexed by reach ID rather than by grid cell. Reach-based and station output is admitted for hydrological entries as an explicit, documented exception to the repository's gridded-data rule, in the same way and for the same reason as station time series in storm surge entries: it is a dominant native distribution form for this model class. Note, however, that many systems publishing reach output **also** publish genuinely gridded land surface and terrain routing fields from the same cycle, so check before assuming the vector product is all there is. Record clearly under **Output geometry** which forms are actually available. Rendered hydrographs, flood maps and viewer-only dashboards remain out of scope as usual.

---

## Notes
- <Operational status (active / pre-operational / legacy), and how to interpret availability.>
- <Units and sign conventions that are not obvious from file metadata — discharge in m³ s⁻¹ vs mm, accumulation vs instantaneous, and the interval a timestamp refers to.>
- <Whether the published product is a subset of the full operational output.>
- <Relationship to other models: deterministic/ensemble sibling pair, medium-range/seasonal sibling pair, the meteorological driver (cross-link the NWP, ensemble or climate entry), and any coastal or surge system this model feeds.>
- <Configuration sprawl: several hydrological systems publish many named configurations from one cycle (short/medium/long range, regional domains, blended forcing, assimilation-disabled variants). If the entry covers only some of them, say which and why.>
- <For AI-based or hybrid hydrological systems, note the approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the system has documented operational upgrades worth tracking. Hydrological systems change routing scheme, calibration, hydrography dataset, and driving NWP model often enough that this is frequently warranted. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
