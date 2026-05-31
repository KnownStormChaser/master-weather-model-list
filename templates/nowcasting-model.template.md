# <SYSTEM NAME>

## What this model is
<One or two plain-language sentences describing what the nowcasting system is, the phenomena it targets (e.g., precipitation, convection, lightning), and its method (extrapolation / blended / ML). Note the lead-time window it serves.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Region name; for radar-based systems this is usually tied to a radar network's coverage>
- **Domain details (optional):** <bounds / grid name / radar network / notable notes>

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** <Observation extrapolation / Seamless extrapolation–NWP blend / Rapid-update NWP / ML-based> — the defining characteristic; pick the closest
- **Technique / algorithm:** <e.g., Lagrangian advection, optical flow, S-PROG, STEPS, deep generative model, MetNet-style> (TBD)
- **Underlying / driving model (optional):** <for blended or NWP-based systems — e.g., AROME-PI, ICON-D2-RUC, HRRR; cross-link to its entry if documented>
- **Probabilistic / ensemble:** <Yes / No> (e.g., STEPS-style stochastic ensemble extrapolation; TBD)
- **Horizontal resolution:** <e.g., 1 km / 0.01°>
- **Vertical structure (optional):** <Most extrapolation nowcasts are 2D single-level products (surface precip rate, composite reflectivity); note levels only if the product is 3D>
- **Lead time:** <e.g., 0–6 h> — the key field for a nowcasting system
- **Update frequency:** <e.g., every 5 / 10 / 15 min> — nowcasts refresh far more often than NWP
- **Temporal output resolution:** <e.g., 5-min steps>
- **Latency:** <e.g., available ~X min after observation valid time> (TBD)

---

## Inputs
The observations and/or model fields the nowcast is built from:
- **Radar:** <network, composite/volume, products used> (TBD)
- **Satellite:** <e.g., geostationary VIS/IR channels> (optional)
- **Lightning:** <network> (optional)
- **Surface / other observations:** <optional>
- **NWP fields (for blended / NWP-based systems):** <source model + how used> (optional)

---

## Blending / seamless transition (optional)
<For systems that merge extrapolation with NWP: describe how the two are weighted across lead time, the crossover window where NWP begins to dominate, and the blending method (e.g., scale-dependent blending). Omit for pure extrapolation or pure rapid-update-NWP systems.>

---

## What it provides
Typical nowcasting outputs:
- precipitation rate / accumulation
- radar reflectivity (composite or 3D)
- probability of precipitation / exceedance probabilities (if probabilistic)
- convective hazards: lightning, hail, severe-storm indicators (optional)
- cloud / visibility / other fields (optional)
- <add notable specialty products if relevant>

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., CC BY 4.0 / CC BY-SA 4.0 / Etalab Open Licence / Public domain (U.S. government work; CC0-equivalent) / KNMI Open Data licence / Copernicus Marine Service licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No — <note: viewer-only nowcast products with no raw data download are out of repository scope>
- **Data formats:** <GRIB2 / NetCDF / HDF5 / BUFR / etc.> (include compression notes if relevant)
- **Official download location:**  
  <URL>

---

## Notes
- <Relationship to siblings: the rapid-update NWP model it blends with or is derived from, ensemble counterpart, parent radar network.>
- <Public-vs-full-ops differences, grid quirks, retention windows (nowcast files are often kept only briefly), etc.>
- <For ML-based or hybrid systems, note the AI approach here and update the repository's [`AI_MODELS.md`](../AI_MODELS.md) index.>

---

## Recent version history (optional)
<Include if the system has documented operational upgrades worth tracking. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
