# <MODEL NAME>

## What this model is
<Plain-language description of the fire danger system and what it forecasts. **State which danger rating system or systems it implements** — Canadian FWI (CFFDRS), US NFDRS, Australian McArthur, or a national scheme — since several engines compute more than one, and the index names collide across systems. State also whether the output is danger *indices* only, or extends to fire *behaviour* quantities such as rate of spread and head fire intensity, which require a fuel map and are a different class of product.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Global / Continental / National / Regional>
- **Domain details (optional):** <bounds / projection extent>
- **Masked areas (optional):** <water, ice, non-vegetated or non-burnable cells are commonly masked — state how, since a nil value in a fire danger raster usually means "not burnable" rather than "missing"> (TBD)

---

## Basic details
- **Model type:** <Deterministic fire danger model / Ensemble fire danger model / Deterministic + ensemble>
- **Core fire danger engine:** <e.g., GEFF (Global ECMWF Fire Forecast) / CFFDRS / in-house implementation> (TBD)
- **Rating system(s) implemented:** <Canadian FWI / US NFDRS / Australian McArthur Mark 5 / national scheme> (TBD)
- **Forecast length:** <e.g., 10 days / 12 days / 216 days>
- **Update frequency / cycles:** <e.g., 1× daily> (TBD)
- **Valid time convention:** <Fire danger indices are conventionally computed for local solar noon or mid-afternoon conditions rather than for a synoptic hour. State the convention explicitly — it is not usually recoverable from the file metadata and it determines what a daily field actually represents.> (TBD)

---

## Grid and projection
- **Grid type:** <Regular lat/lon / projected raster> (TBD)
- **Projection / CRS:** <e.g., EPSG:4326 / EPSG:3978 Canada Atlas Lambert — projected national grids are common in this category and worth stating explicitly, since most other entries in this repository are geographic> (TBD)
- **Horizontal resolution:** <e.g., 0.25° / 2 km> (TBD)
- **Grid dimensions (optional):** <e.g., 2709 × 2281> (TBD)

---

## Meteorological forcing
- **Driving atmospheric model:** <name the NWP, ensemble, or seasonal system and cross-link its entry> (TBD)
- **Required inputs:** <the danger codes need temperature, relative humidity, wind speed and 24-hour accumulated precipitation at the valid time; note any substitutions or additional variables the implementation uses> (TBD)
- **Observed weather input (optional):** <station or analysed weather used for the current-conditions run and for spinup> (TBD)
- **Bias correction / downscaling (optional):** (TBD)

---

## Moisture code carryover and initialization
<Distinctive to this model class and easy to overlook. The slow-response moisture codes — Drought Code, Duff Moisture Code, and their equivalents — integrate weather over weeks to months, so a fire danger forecast is not a stateless function of the forecast weather. It inherits an antecedent moisture state from a continuous run driven by observed conditions.>

- **Carryover codes:** <which codes persist between days, e.g., FFMC, DMC, DC> (TBD)
- **State source for forecast runs:** <the analysis or current-conditions run that supplies the starting moisture codes> (TBD)
- **Seasonal startup / overwintering:** <when the season is initialized each spring and how the Drought Code is overwintered — conventions differ by agency and materially affect early-season values> (TBD)
- **Greenup / curing state (optional):** <whether vegetation phenology modulates the indices, and from what source> (TBD)

---

## Fuel and static inputs (optional — fire behaviour outputs only)
<**Delete this section for entries that publish danger indices only.** Fire behaviour quantities require a fuel type map; danger indices do not.>

- **Fuel type map:** <source, classification scheme, resolution> (TBD)
- **Elevation / slope inputs:** (TBD)
- **Static layer distribution:** <whether the fuel and terrain layers are themselves downloadable alongside the forecasts> (TBD)

---

## Ensemble configuration (ensemble systems only)

<**Delete this entire section for deterministic entries.** Do not leave it in place filled with TBDs — an empty ensemble block in a deterministic entry reads as missing research rather than as "not applicable.">

- **Ensemble size:** (TBD)
- **Source of perturbations:** <usually inherited from the driving atmospheric ensemble — name it; state explicitly if the fire danger computation is itself perturbed, which is uncommon> (TBD)
- **Resolution / output differences vs deterministic sibling:** (TBD)
- **Member packaging:** (TBD)
- **Derived products distributed:** <ensemble mean, probability of exceeding danger class thresholds, percentiles — list only what is published as raw data> (TBD)

---

## Long-range configuration (sub-seasonal and seasonal systems only)

<**Delete this entire section for medium-range entries.** Retain it for seasonal or sub-seasonal fire danger systems, which are filed here beside their medium-range siblings under the phenomenon-over-forecast-range convention rather than under `climate_models/`. Cross-link the driving seasonal system's entry in `climate_models/` rather than duplicating its configuration here.>

- **Driving long-range system:** <cross-link the entry, e.g., SEAS5> (TBD)
- **Initialization cadence:** <e.g., 1st of each month> (TBD)
- **Hindcast (reforecast) period:** (TBD)
- **Reference climatology period:** <period used for anomaly and ranking products, if stated separately> (TBD)
- **Hindcasts distributed alongside forecasts?** <Yes / No / Separate dataset> (TBD)
- **Real-time status:** <Some seasonal fire datasets are explicitly not real-time services, with the operational forecast delivered through a separate portal that may not publish raw grids at all. State this plainly if so.> (TBD)

---

## What it provides
Fire danger forecasts including (as available):

**Canadian FWI system components**
- Fine Fuel Moisture Code (FFMC)
- Duff Moisture Code (DMC)
- Drought Code (DC)
- Initial Spread Index (ISI)
- Buildup Index (BUI)
- Fire Weather Index (FWI)
- Daily Severity Rating (DSR)

**Other rating systems (as implemented)**
- US NFDRS indices
- Australian McArthur Forest Fire Danger Index
- national danger class ratings

**Fire behaviour outputs (fuel-map dependent)**
- rate of spread
- head fire intensity
- crown fraction burned
- fuel consumption

**Derived**
- danger class categories
- anomalies and rankings relative to model climatology

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Open Government Licence – Canada / CC BY 4.0 / Copernicus licence (registration and licence acceptance required)> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Output geometry:** <Gridded raster / vector danger-class polygons / both> — see the scope note below
- **Access mechanism:** <Direct file download from a directory tree / OGC Web Coverage Service (WCS) / data store API> (TBD)
- **Data formats:** <NetCDF / GeoTIFF / GRIB2> (TBD)
- **Layer or coverage naming (if OGC):** <the naming pattern for dated forecast layers, which is often the only way to tell forecast layers from current-conditions ones> (TBD)
- **Official download location:**
  <URL>

> **Scope note.** This category has two access patterns that need care. First, several operators publish fire danger through OGC web services rather than as files in a directory tree, and the distinction between WMS and WCS is decisive: **WMS returns rendered images and is out of scope; WCS GetCoverage returns the underlying raster and is in scope.** A service advertising fire danger layers on WMS does not necessarily expose them on WCS — tile-indexed raster layers frequently lack the metadata WCS requires, and the layer list on the two endpoints can differ substantially. Verify with an actual GetCoverage request before treating a WMS layer list as evidence of data availability. Second, agency fire danger is often published twice: as a gridded model raster and as a coarser vector polygon layer of administrative danger classes. The raster is in scope; the polygon layer is a derived cartographic product and is not. Record clearly under **Output geometry** which is actually retrievable.

---

## Notes
- <Operational status (active / pre-operational / legacy), and how to interpret availability.>
- <Which quantities have dated forecast layers versus current-conditions layers only — this frequently differs from what the documentation implies, and the split determines how much of the system is genuinely forecast data.>
- <Index scaling and units: several codes are unbounded and open-ended, and danger class breakpoints are agency-specific rather than intrinsic to the index. Note the breakpoints if published.>
- <Relationship to other models: the meteorological driver (cross-link the NWP, ensemble or climate entry), the current-conditions analysis sibling, and any smoke or air quality system that consumes fire activity — note that smoke dispersion models are filed under `air_quality_models/`, not here.>
- <If the public data is a subset of the full operational output, state that here.>
- <For AI-based or hybrid fire danger systems, note the approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the system has documented operational upgrades worth tracking — fire danger systems change driving NWP model, fuel map, and rating system implementation. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
