# <MODEL NAME>

## What this model is
<Plain-language description of the tropical cyclone / hurricane model and what it's for.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country>

---

## When this model runs (optional)
<Many TC models run on demand for active storms rather than on a fixed schedule. If so, state what triggers a run (e.g., NHC / JTWC tasking) and that forecasts are storm-centered rather than tied to a fixed domain. See `hmon.md`, `hwrf.md`, `hafs.md` for examples. Omit if the model runs on a fixed synoptic schedule.>

---

## What area it covers
- **Coverage:** <Basins / global TC capability / regional>
- **Storm selection:** <How storms are chosen / initialized> (TBD)

---

## Basic details
- **Model type:** Tropical cyclone forecast model (deterministic / ensemble / both)
- **Model system / core:** <e.g., HAFS, HWRF, HMON> (TBD)
- **Horizontal resolution:** <inner nest / outer domain> (TBD)
- **Vertical levels:** <TBD>
- **Model top (optional):** <e.g., ~10 hPa> (TBD)
- **Forecast length:** <e.g., up to 126 h>
- **Update frequency / cycles:** <e.g., 4× daily aligned with synoptic cycles> (TBD)
- **Temporal output resolution:** <e.g., 1h / 3h> (TBD)

---

## Initialization and data assimilation (important)
- **Initialization approach:** <vortex initialization / relocation / coupling> (TBD)
- **Data assimilation:** <Yes/No + brief> (TBD)
- **Boundary conditions:** <source global model if applicable> (TBD)

---

## Ocean / wave coupling (optional)
- **Ocean coupling:** <Coupled ocean model + basins where active, or none> (TBD)
- **Wave coupling:** <e.g., one-way to WAVEWATCH III in selected basins, or none> (TBD)

---

## What it provides
Typical outputs include:
- track forecasts
- intensity forecasts (max winds, min pressure)
- precipitation and wind structure
- storm-centric fields (if available)
- gridded forecast fields (if available)

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Public domain (U.S. government work; CC0-equivalent) / CC BY 4.0 / Open Government Licence / Etalab Open Licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Data formats:** <GRIB2 / NetCDF / ATCF / both> (as applicable)
- **Official download location:**  
  <URL>

---

## Notes
- <Operational status (active vs legacy), and how to interpret availability.>
- <If runs depend on active storms, state that clearly.>
- <Relationship to other models: predecessor/successor systems (e.g., GFDL → HMON/HWRF → HAFS), dual-deterministic pairings, parent global model for ICs/BCs, and consensus/multi-model context.>
- <For AI-based or hybrid TC systems, note the AI approach here and update [`AI_MODELS.md`](../AI_MODELS.md). Note that some global AI models (e.g., AIGFS) provide notable TC track guidance without being dedicated TC models.>

---

## Recent version history (optional)
<Include this section if the model has documented operational upgrades worth tracking — TC systems change nest design, coupling, and assimilated observations (e.g., the SFMR change in HAFSv2.1) often enough that this is frequently warranted. See `hafs.md` for an example. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
