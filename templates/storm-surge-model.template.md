# <MODEL NAME>

## What this model is
<Plain-language description of the storm surge / water level model and what it forecasts. State clearly whether it predicts total water level, surge residual only, or both — this is the single most common source of confusion in surge products.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## When this model runs (optional)
<Some surge systems run on a fixed synoptic schedule; others are triggered on demand by tropical cyclone advisories or by exceedance of a storm threshold (e.g., SLOSH / P-Surge style operation). If runs are event-driven, state what triggers them and whether the domain is storm-selected rather than fixed. Omit if the model runs on a fixed cycle.>

---

## What area it covers
- **Coverage:** <Global / Basin / Shelf sea / Coastal or estuarine domain>
- **Domain details (optional):** <bounds / named seas / coastline extent covered>
- **Inundation coverage:** <Does the domain extend onto land for overland flooding, or does it stop at the coastline?> (TBD)

---

## Basic details
- **Model type:** <Deterministic storm surge model / Ensemble storm surge model / Deterministic + ensemble>
- **Core hydrodynamic model:** <e.g., ADCIRC / SCHISM / Delft3D-FM / GTSM (Delft3D-FM based) / DCSM / NEMO-surge / ROMS / SLOSH / in-house> (TBD)
- **Dimensionality:** <2D depth-averaged (barotropic) / 3D baroclinic> (TBD)
- **Forecast length:** <e.g., 48 h / 120 h / 240 h>
- **Update frequency / cycles:** <e.g., 4× daily (00/06/12/18 UTC)>
- **Temporal output resolution:** <e.g., 10 min / 1 h> (TBD)

---

## Grid and bathymetry
- **Grid type:** <Structured regular grid / curvilinear / unstructured triangular mesh> (TBD)
- **Horizontal resolution:** <For unstructured meshes give the range and where each applies, e.g., ~50 m in estuaries to ~25 km offshore; for structured grids give the spacing> (TBD)
- **Mesh size (optional):** <e.g., ~3.1 M nodes / 6.2 M elements> (TBD)
- **Bathymetry source:** <e.g., GEBCO, EMODnet, national hydrographic survey, blended local LiDAR> (TBD)
- **Wetting and drying:** <Yes / No — whether cells can flood and re-dry, which determines whether overland inundation is meaningful> (TBD)

---

## Vertical datum and reference level (important)
- **Vertical datum:** <e.g., MSL / LAT / NAVD88 / Chart Datum / model mean sea level / national datum name> (TBD)
- **What the water level field is measured relative to:** <state explicitly — e.g., "total water level above LAT" vs "surge residual above the tidal prediction" vs "anomaly relative to model mean">
- **Datum conversion offsets provided?** <Yes / No — some operators ship a static datum-offset field or per-station conversion table> (TBD)
- **Mean sea level trend / SLR handling (optional):** <Is a sea level rise offset or steric contribution included, or is the model referenced to a fixed epoch?> (TBD)

> This section is worth filling in even when the answer is "not documented." Datum ambiguity is the most consequential undocumented property of a surge product — a forecast in the wrong datum can be off by more than the surge itself.

---

## Tide handling
- **Are tides included?** <Yes, run with tidal forcing / No, surge-only (meteorological residual) / Both variants distributed> (TBD)
- **Tidal forcing source:** <e.g., FES2014, TPXO, national harmonic constituent set, boundary forcing from a global tide model> (TBD)
- **Separation of components:** <Does the operator distribute tide, surge residual, and total water level as separate fields or files, or only the combined total?> (TBD)
- **Tide–surge interaction:** <Modelled nonlinearly within the run, or linearly superposed after the fact?> (TBD)

---

## Forcing and coupling
- **Meteorological forcing — wind:** <source model + wind level (usually 10 m)> (TBD)
- **Meteorological forcing — pressure:** <source model MSLP; note that the inverse barometer contribution is co-equal with wind stress for surge> (TBD)
- **Wave contribution:** <None / one-way wave setup via radiation stress or wave-induced stress / two-way coupled; name the source wave model> (TBD)
- **River discharge / freshwater forcing:** <source + whether climatological or live observations; relevant for estuarine and coastal domains> (TBD)
- **Ocean forcing / boundary conditions:** <source of open-boundary water levels and any baroclinic contribution> (TBD)
- **Ice forcing (if applicable):** <source + how ice cover modifies wind stress transfer> (TBD)
- **Nested inside / parent for:** <optional>

---

## Data assimilation (optional)
- **Assimilates water level observations:** <Yes / No> (TBD)
- **Observation sources (if yes):** <e.g., tide gauge networks, satellite altimetry> (TBD)
- **Method / cadence (optional):** <e.g., Kalman filter on the surge residual, bias correction against gauges, daily cycle> (TBD)

---

## Ensemble configuration (ensemble systems only)
- **Ensemble size:** <e.g., 20 perturbed + 1 control> (TBD)
- **Source of perturbations:** <Almost always inherited from the driving atmospheric ensemble — name it. State explicitly if the surge model itself is perturbed (e.g., wind drag coefficient, bathymetry, or tidal phase perturbations), which is uncommon but does occur in TC surge systems.> (TBD)
- **Deterministic counterpart:** <Name and cross-link the sibling deterministic system if one exists.> (TBD)
- **Resolution / domain / output differences vs deterministic sibling:** <Ensemble surge systems are frequently regional where the deterministic sibling is global, and often coarser or shorter-range.> (TBD)
- **Member packaging:** <Separate file per member vs member dimension in one file; GRIB2 `perturbationNumber` / `typeOfEnsembleForecast` encoding, or NetCDF member dimension name.> (TBD)
- **Derived products distributed:** <Ensemble mean, spread, water level exceedance probabilities, percentiles — list only what is actually published as raw data, not what appears in web viewers.> (TBD)

---

## What it provides
Water level forecasts including (as available):
- total water level
- storm surge residual (meteorological component)
- tidal water level (if distributed separately)
- depth-averaged current components (U, V)
- inundation depth over land (if the domain includes overland cells)
- maximum envelope of water / peak surge over the run (if provided)
- wave setup contribution (if provided separately)

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Open Government Licence – Canada / CC BY 4.0 / Public domain (U.S. government work; CC0-equivalent) / Copernicus Marine Service licence (registration required) / Etalab Open Licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Output geometry:** <Gridded fields / station (point) time series at named gauge locations / both> — see the scope note below
- **Data formats:** <GRIB2 / NetCDF / both> (note CF conventions or unstructured-mesh conventions such as UGRID if relevant)
- **Station list (if point output):** <URL or description of the station metadata file> (TBD)
- **Official download location:**  
  <URL>

> **Scope note.** Many operational surge systems publish station time series at tide gauge locations as the primary product, with gridded fields secondary or absent. Point NetCDF output is admitted for storm surge entries as an explicit, documented exception to the repository's gridded-data rule, because it is the dominant native distribution form for this model class. Record clearly in this section which geometry is actually available. Rendered water level graphics, viewer-only dashboards, and gauge-plot images remain out of scope as usual.

---

## Notes
- <Operational status (active / pre-operational / legacy), and how to interpret availability.>
- <Datum, tide-inclusion, or sign-convention quirks that are not obvious from the file metadata.>
- <Relationship to other models: deterministic/ensemble sibling pair, global parent providing boundary water levels, regional nests, the meteorological driver model (cross-link the NWP entry), and the wave model if coupled.>
- <If the public data is a subset of the full operational output — e.g., only the surge residual is published while the operator runs a full tide-plus-surge system — state that here.>
- <For AI-based or hybrid surge emulators, note the approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the system has documented operational upgrades worth tracking. Surge systems change mesh, driving NWP model, tidal forcing dataset, and wave coupling often enough that this is frequently warranted. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
