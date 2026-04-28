# ICON-ART-EU (European Pollen Forecast)

## What this model is
ICON-ART-EU is DWD's operational regional pollen forecast system, built on the European nest of ICON (ICON-EU) extended with the ART (Aerosols and Reactive Trace gases) module. It produces 5-day pollen forecasts across Europe, four times daily, for five allergenic species: **alder, birch, grasses, ragweed, and hazel**.

The system was developed jointly by DWD and the Karlsruhe Institute of Technology (KIT), with substantial collaboration from MeteoSwiss who use a closely related ICON-ART configuration for their own pollen forecasting service. DWD's pollen forecast became operational in **September 2021**, making it one of the earliest operational national-scale pollen forecasting systems based on online-coupled meteorology-aerosol modelling.

ICON-ART-EU represents a meaningful step beyond simpler statistical pollen forecasting: it explicitly models pollen emission (driven by phenology, vegetation distribution, and meteorological readiness), atmospheric transport (using the same advection scheme as the underlying ICON-EU NWP forecast), sedimentation, and wet/dry deposition. The "online-coupled" design means meteorology and pollen fields evolve together within a single model run, allowing detailed representation of how weather conditions modulate pollen release and dispersal.

DWD shares the core ICON-ART pollen modelling capability with MeteoSwiss, who operate a similar configuration over Switzerland. The ART module's pollen capabilities were largely co-developed by the two services together with KIT.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT) and shared with MeteoSwiss
- **Country:** Germany
- **Operational since:** September 2021

---

## What area it covers
- **Coverage:** Europe (matching the ICON-EU nest domain)
- **Domain bounds (approx.):** 23.5°W – 62.5°E, 29.5°N – 70.5°N (same as ICON-EU)
- **Native grid:** R3B08 icosahedral grid (~6.5 km), inherited from ICON-EU

---

## Basic details
- **Model type:** Regional pollen forecast (online-coupled meteorology-aerosol)
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) — limited-area mode, same configuration as ICON-EU
- **ART module developer:** Karlsruhe Institute of Technology (KIT)
- **Coupling:** Online — pollen and meteorological fields share the same grid, advection scheme, and time integration
- **Horizontal resolution:** ~6.5 km (matching ICON-EU)
- **Vertical levels:** 74 (matching ICON-EU)
- **Forecast length:** 120 hours (5 days)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 3-hourly
- **Operational since:** September 2021

---

## Forecast species
ICON-ART-EU forecasts five allergenic pollen species:

| Species | Typical bloom season (Europe) |
|---|---|
| **Hazel** (Corylus) | Late winter — earliest pollen of the year |
| **Alder** (Alnus) | Late winter / early spring |
| **Birch** (Betula) | Spring — major allergen across northern and central Europe |
| **Grasses** (Poaceae) | Late spring / summer — most widespread allergen |
| **Ragweed** (Ambrosia) | Late summer / autumn |

The five species cover the full pollen calendar for central European allergy sufferers, with substantial overlap during transition periods (e.g., late spring birch–grass overlap; late summer grass–ragweed overlap).

For each species, the model produces 3-hourly concentration forecasts driven by:
- **Plant distribution maps** for the relevant vegetation
- **Phenological state** — bloom start dates calculated from antecedent weather (cumulative temperature, day length, precipitation)
- **Meteorological emission drivers** — wind, humidity, precipitation
- **Atmospheric transport, sedimentation, and deposition** computed online

---

## What it provides
- 3D pollen concentration fields for each of the five species
- Surface (near-ground) pollen counts at 3-hourly temporal resolution
- 120-hour (5-day) forecast horizon, refreshed four times daily
- Phenological diagnostics (e.g., calculated bloom start dates)
- Inputs to DWD's public-facing pollen warning service

---

## Online coupling and atmospheric transport
The online-coupling architecture means pollen tracers are advected by the same dynamical core that produces ICON-EU's meteorological forecast — using identical grid-scale advection and subgrid-scale transport algorithms. This is structurally different from offline-coupled pollen models that ingest pre-computed wind fields from a separate NWP run.

ART-specific processes for pollen include:
- **Emission parameterization:** EMPOL-based emission scheme (Zink et al., 2013) accounting for meteorological readiness, plant distribution, and bloom phenology
- **Transport:** Same advection scheme as ICON-EU's water vapor transport
- **Sedimentation:** Gravitational settling at species-specific terminal velocities
- **Wet deposition:** Pollen washout by precipitation
- **Dry deposition:** Surface removal processes

The system explicitly accounts for the fact that pollen forecasts depend critically on the underlying weather forecast — ICON-EU's wind, temperature, precipitation, and humidity forecasts directly drive both pollen emission and dispersal.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** DWD Open Data Server
  - ICON-ART-EU: https://opendata.dwd.de/weather/nwp/v1/m/icon-art-eu/
- **Distribution path note:** ICON-ART-EU is distributed under `/weather/nwp/v1/m/` rather than alongside the main ICON-EU directory at `/weather/nwp/icon-eu/`. The `v1/m/` path appears to be a distribution tier introduced in 2025 and also hosts the global ICON-ART (mineral dust), ICON-D2-RUC, and the ensemble counterparts of these systems.
- **Licence:** DWD Open Data licence (CC-BY 4.0-compatible; attribution required)
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours).

---

## Relationship to other models

### Companion DWD systems
- **[ICON-EU](../../../nwp_models/regional/germany/icon-eu.md):** The base meteorological model that ICON-ART-EU extends. Same grid, same dynamical core, same physics — with ART pollen modules added.
- **[ICON-ART](../../global/germany/icon-art.md):** The global companion for mineral dust forecasting. The two ICON-ART configurations cover different operational use cases — pollen requires regional resolution and European species coverage, while dust requires global advection (notably for trans-Saharan transport).

### Peer pollen forecasting systems
ICON-ART-EU is one of several pollen forecasting systems operating in Europe, with different operational scopes:

- **[CAMS Regional](../eu/cams-regional.md):** The Copernicus Atmosphere Monitoring Service distributes pollen forecasts (alder, birch, grass, mugwort, olive, ragweed) as part of its 11-model regional ensemble. CAMS Regional pollen runs at ~10 km on the ENSEMBLE grid and is produced as a multi-model median.
- **[SILAM Global](../../global/finland/silam-global.md):** FMI's SILAM atmospheric composition model, distributed at 20 km global resolution, is used both as a contributor to CAMS Regional and as a standalone forecast.
- **MeteoSwiss pollen forecast:** Operates ICON-ART at 2.1 km over Switzerland with the same five species. Methodologically very similar to DWD's system (same ART module, same KIT collaboration), but at higher resolution over a smaller domain.

ICON-ART-EU's distinctive features in this peer group are: **online-coupling with ICON-EU NWP** (rather than offline coupling to a separate driving model), **6.5 km resolution across the full European domain** (finer than CAMS Regional's 10 km), and **4× daily update frequency** (more frequent than the 1× daily CAMS pollen forecast).

### Architectural lineage
The ART module's pollen capabilities were jointly developed by KIT, DWD, and MeteoSwiss, with the pollen emission scheme based on EMPOL (Zink et al., 2013). MeteoSwiss's operational pollen forecasting service uses the same fundamental ART pollen module, configured for Swiss conditions and species relevant to the Swiss domain.

---

## Notes
- The 4× daily update cadence is unusually frequent for an operational pollen forecast — most peer systems (CAMS Regional, SILAM-based national services) update once daily. This reflects ICON-ART-EU's tight integration with ICON-EU's operational cycle: each pollen forecast inherits the same atmospheric initial conditions as ICON-EU at the same cycle, so refreshing pollen four times per day adds value at a relatively low marginal cost.
- DWD's pollen warning service uses ICON-ART-EU forecasts as its primary modeling input, combined with expert evaluation of plant readiness and observed pollen counts where available.
- The species set (alder, birch, grasses, ragweed, hazel) is broadly aligned with what most central European allergy sufferers encounter, but does **not** include some Mediterranean species like olive (covered by CAMS Regional) or mugwort (also CAMS Regional). Users in Mediterranean regions or with specific non-listed allergens should look to additional sources.
- Real-time pollen observation networks (notably the AutoPollen network coordinated by MeteoSwiss under EUMETNET) are providing near-real-time validation data for systems like ICON-ART-EU, with the longer-term goal of supporting pollen data assimilation. As of operational ICON-ART-EU configuration, pollen observations are not directly assimilated.
- The ICON-ART codebase used at DWD is the same fundamental code used at MeteoSwiss for Swiss pollen forecasting, jointly developed and maintained.

---

## Official documentation
- DWD ICON-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- KIT ICON-ART website: https://www.icon-art.kit.edu/
- ICON-ART module documentation: https://docs.icon-model.org/atmosphere/art/art.html
- DWD Open Data Server v1/m subtree: https://opendata.dwd.de/weather/nwp/v1/m/

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev. Discuss.*, 8, 567–614.
- Schröter, J., et al. (2018). ICON-ART 2.1: a flexible tracer framework and its application for composition studies in numerical weather forecasting and climate simulations. *Geosci. Model Dev.*, 11, 4043–4068.
- Zink, K., et al. (2013). EMPOL 1.0: a new parameterization of pollen emission in numerical weather prediction models. *Geosci. Model Dev.*, 6, 1961–1975.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.
