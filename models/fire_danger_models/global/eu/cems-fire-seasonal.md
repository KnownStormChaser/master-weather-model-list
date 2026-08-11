# CEMS Fire Seasonal (GEFF seasonal fire danger forecast)

## What this model is
CEMS Fire Seasonal is the long-range fire danger forecast of the Copernicus Emergency Management Service. It drives the **Global ECMWF Fire Forecast (GEFF)** model with ECMWF's SEAS5 seasonal ensemble, producing daily fire danger indices at 12:00 local time on a global land grid, initialised monthly with a lead time of 215 days.

GEFF implements **three national fire danger rating systems in parallel** — the Canadian Forest Service Fire Weather Index system, the US Forest Service National Fire-Danger Rating System, and the Australian McArthur Mark 5 system — so a single retrieval can return indices from all three for the same grid cell and date. The output is **danger indices only**; no fire behaviour quantities are produced, because no fuel map enters this chain.

ECMWF produces the dataset as the computational centre for fire danger forecasting within CEMS, on behalf of the Joint Research Centre as managing entity. The same GEFF machinery underlies the operational products shown on EFFIS and GWIS.

> **This entry is filed under `fire_danger_models/` rather than `climate_models/`**, following the repository's convention that seasonal forecasts of a phenomenon use the phenomenon's template. It uses `fire-danger-model.template.md` with the optional **Long-range configuration** section retained, and points upstream to [SEAS5](../../../climate_models/global/eu/seas5.md) rather than duplicating that system's coupled configuration.

---

## Who runs it
- **Organization:** Copernicus Emergency Management Service (CEMS) — European Commission Joint Research Centre (JRC), computed and distributed by ECMWF
- **Country / region:** International (European Union programme)

---

## What area it covers
- **Coverage:** Global land
- **Domain details:** regular latitude–longitude grid. **TBD — the domain bounds need file verification**: the EWDS catalogue reports `bbox: [-179.95, -89.95, 179.95, 89.95]`, which is a cell-centre convention consistent with a 0.1° grid and **inconsistent with the 1° resolution the same record documents**. See *Notes*.
- **Masked areas:** ocean cells are excluded ("Global land"). Nil-value handling is unverified.

---

## Basic details
- **Model type:** Ensemble fire danger model (seasonal range)
- **Core fire danger engine:** GEFF — Global ECMWF Fire Forecast
- **Rating systems implemented:** Canadian FWI (7 indices), US NFDRS (4 indices), Australian McArthur Mark 5 (3 indices) — 14 variables total
- **Forecast length:** 5148 h, **215 daily steps** — verified against the EWDS request constraints
- **Update frequency / cycles:** monthly, initialised on the 1st
- **Publication:** documented as monthly on the 5th, one month behind real time. **Observed lag is longer** — see *Notes*.
- **Valid time convention:** **daily at 12:00 local time.** Lead-time hours run 12, 36, 60 … 5148, i.e. offset 12 hours from initialisation and stepping 24 hours thereafter. This is the fire danger convention of computing indices for local solar noon rather than for a synoptic hour, and it is encoded directly in the lead-time axis.
- **Release version:** 5 (the only value offered in the request form)

---

## Grid and projection
- **Grid type:** regular latitude–longitude
- **Projection / CRS:** EPSG:4326
- **Horizontal resolution:** **1° × 1°** per the EWDS data description table. Note this is considerably coarser than the ~0.25° figure associated with other GEFF products, and coarser than SEAS5's own ~36 km — flagged as **TBD pending file verification** given the bounding-box conflict described in *Notes*.
- **Grid dimensions:** TBD

---

## Meteorological forcing
- **Driving atmospheric model:** [SEAS5](../../../climate_models/global/eu/seas5.md) — ECMWF's seasonal forecasting system
- **Required inputs:** the fire danger codes need temperature, relative humidity, wind speed and 24-hour accumulated precipitation at the valid time; GEFF derives all four from the SEAS5 fields
- **Bias correction / downscaling:** TBD

---

## Moisture code carryover and initialization
- **Carryover codes:** the FWI system's Fine Fuel Moisture Code, Duff Moisture Code and Drought Code all persist between days, as do the McArthur Keetch-Byram Drought Index and the NFDRS Energy Release Component. A seasonal forecast at day 200 therefore reflects an integrated moisture trajectory, not a same-day diagnosis of the forecast weather.
- **State source for forecast runs:** **this is the key initialisation split in the dataset.** Reforecast simulations covering 1981–2016 were initialised from **ERA-Interim** analysis; forecast simulations from 2016 onward were initialised from **ECMWF operational analysis**. ECMWF advises treating 1981–2016 as a reference period and 2017-to-present as the real-time forecast period, and they should not be concatenated into a single homogeneous series.
- **Seasonal startup / overwintering:** TBD — not documented on the public dataset pages
- **Greenup / curing state:** not applicable; this chain has no vegetation phenology input

---

## Ensemble configuration
- **Ensemble size:** 25 members through December 2016 (reforecast mode), **51 members from 2017 onward**
- **Source of perturbations:** inherited entirely from SEAS5. GEFF itself is not perturbed.
- **Member packaging:** TBD — the request form exposes no product-type or member selector, so control and perturbed members are not separable at request time
- **Derived products distributed:** none as raw data. Danger class categorisations and anomaly maps on EFFIS and GWIS are viewer products — out of scope.

---

## Long-range configuration
- **Driving long-range system:** [SEAS5](../../../climate_models/global/eu/seas5.md)
- **Initialization cadence:** 1st of each month (the request form's `day` selector offers only `01`)
- **Hindcast (reforecast) period:** 1981–2016, 25 members, ERA-Interim-initialised — distributed **within this same dataset** rather than as a separate collection, which is unusual and easy to miss
- **Reference climatology period:** ECMWF suggests 1981–2016 as the reference period; no separately published climatology baseline
- **Hindcasts distributed alongside forecasts?** **Yes** — the archive is continuous from 1981-02-01, with the initialisation-source and ensemble-size break at the end of 2016 as the only marker of the transition
- **Sources of predictability:** TBD
- **Real-time status:** **This is explicitly not a real-time service.** ECMWF states that real-time forecasts are accessible through the EFFIS web services instead. Those services expose fire danger over WMS only and provide no coverage retrieval, so the operational daily GEFF forecast has no in-scope distribution channel — see *Notes*.

---

## What it provides

**Canadian Forest Service Fire Weather Index Rating System**
- Fine Fuel Moisture Code
- Duff Moisture Code
- Drought Code
- Initial Fire Spread Index
- Build Up Index
- Fire Weather Index
- Fire Daily Severity Rating

**U.S. Forest Service National Fire-Danger Rating System**
- Ignition Component
- Spread Component
- Energy Release Component
- Burning Index

**Australian McArthur Mark 5 Rating System**
- Drought Factor
- Fire Danger Index
- Keetch-Byram Drought Index

No fire behaviour outputs (rate of spread, intensity, fuel consumption) — those require a fuel map that this chain does not use.

---

## Data availability
- **Is the data free?** Yes, with free self-registration on the CEMS Early Warning Data Store
- **License:** **CC-BY-4.0**, declared with an SPDX identifier in the catalogue. Attribution required; no share-alike or non-commercial restriction. This is a materially cleaner licence than the CEMS-FLOODS terms attached to the GloFAS and EFAS datasets on the same data store.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields only
- **Access mechanism:** EWDS API request against the processes endpoint with an account key, or the web form. No anonymous directory tree.
- **Data formats:** GRIB, and NetCDF-4 flagged **experimental** in the request form
- **Official download location:**
  https://ewds.climate.copernicus.eu/datasets/cems-fire-seasonal
- **DOI:** https://doi.org/10.24381/cds.b9c753f1
- **Archive extent:** 1981-02-01 to 2026-06-01 (latest initialisation available when checked on 2026-08-11)
- **FAIR score:** 92 per the catalogue, against 84 for the CEMS-Flood datasets

> **Verification status.** Verified live against the EWDS catalogue, request form, constraints and layout endpoints. **No GRIB or NetCDF file was decoded** — EWDS retrieval requires an authenticated account key — so resolution, grid dimensions, domain bounds, land mask handling and GRIB parameter encoding are documentation-derived and unconfirmed. The resolution conflict noted below makes a decode pass the highest-value follow-up for this entry.

---

## Notes

- **Resolution and bounding box contradict each other.** The EWDS data description table states 1° × 1°, but the STAC record's bounding box of ±179.95 / ±89.95 is a cell-centre convention that fits a **0.1°** grid exactly and fits 1° not at all (1° cell centres would run −179.5 to 179.5). One of the two is wrong. Given how routinely the CEMS bounding boxes on this data store are wrong — the GloFAS and EFAS records carry global 0–360 boxes that match neither their grids nor their domains — the resolution table is the more likely of the two to be correct, but neither should be trusted without reading a file header.

- **Documented forecast horizon is off by one day.** The dataset description states a prediction horizon of "216 days (equivalent to 7 months)". The request constraints expose **215 steps**, running 12 h to 5148 h, which is day 215. Minor, but it will produce an off-by-one in any request builder generating the lead-time list arithmetically.

- **Publication is running behind its own schedule.** The data description states "monthly on the 5th of each month, one month behind real time." As of 2026-08-11 the latest initialisation available was **2026-06-01**, and the catalogue's `updated` timestamp was 2026-07-05. The August 5 update, which would have added the July initialisation, had not appeared. Whether this is a one-off delay or a drift in the schedule is unresolved — worth re-checking rather than treating the documented cadence as reliable.

- **The reforecast is inside this dataset, not beside it.** Unlike the CEMS-Flood products, where reforecasts live in separate collections, this dataset's archive runs continuously from 1981. The 1981–2016 portion is a 25-member reforecast initialised from ERA-Interim; 2017 onward is a 51-member forecast initialised from operational analysis. There is no flag in the request form distinguishing the two — only the year. Anyone slicing across 2016/2017 changes ensemble size and initialisation source simultaneously.

- **The operational daily fire danger forecast is not available anywhere in scope.** ECMWF directs real-time users to EFFIS web services. Those expose the GEFF fire danger fields (`ecmwf.fwi.fwi`, `.ffmc`, `.dmc`, `.dc`, `.isi`, `.bui`, `.anomaly`, `.ranking`, plus Météo-France and NASA GEOS-5 variants) over **WMS only**. A WCS GetCapabilities request against the EFFIS endpoint returns an explicit error stating the raster layers are tile-indexed without WCS virtual dataset metadata; falling back to WCS 1.0.0 exposes only `corine.l2` and `fuel_map`, both static. There is no path to the underlying grids, so the medium-range GEFF forecast is excluded from this catalogue under the rendered-images rule. This seasonal dataset is the only openly retrievable GEFF product.

- **Three rating systems, colliding index names.** The Canadian and Australian systems both produce something called a drought-related index (Drought Code and Keetch-Byram Drought Index respectively), and "Fire Weather Index" (Canadian) and "Fire Danger Index" (McArthur) are different quantities on different scales. Retrieving across groups requires care with variable naming; the EWDS form separates them into labelled groups for exactly this reason.

- **Relationship to other datasets.** Companion product on EWDS: `cems-fire-historical-v1`, the reanalysis-driven fire danger record — historical-only and out of scope as a standalone entry, though note it is licensed CC-BY-4.0 and updated *daily*, unlike this one. The Canadian national counterpart, which does publish an operational gridded forecast, is [CWFIS](../../regional/canada/cwfis.md).

- **SEAS6 dependency.** ECMWF states the dataset will be updated with the next ECMWF seasonal system once SEAS5 operations cease. This entry will need revisiting at that point, alongside the SEAS6 entry already tracked as a pending item.

---

## Recent version history
- **Release version 5** is the only version offered in the request form; no earlier release versions are retrievable. The dataset was published to the data store on 2023-10-17.

---

## Official documentation
- Dataset page: https://ewds.climate.copernicus.eu/datasets/cems-fire-seasonal
- EFFIS: https://forest-fire.emergency.copernicus.eu/
- GWIS: https://gwis.jrc.ec.europa.eu/
- Licence: https://spdx.org/licenses/CC-BY-4.0

### Key reference
- Vitolo, C. et al. (2020). ERA5-based global meteorological wildfire danger maps. *Scientific Data*, 7, 216.
- Di Giuseppe, F. et al. (2016). The potential predictability of fire danger provided by numerical weather prediction. *Journal of Applied Meteorology and Climatology*, 55(11), 2469–2491.
