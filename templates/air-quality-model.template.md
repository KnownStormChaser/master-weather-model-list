# <MODEL NAME>

## What this model is
<One or two plain-language sentences describing what the model is and what it forecasts. Mention the main pollutants or species, and the primary intended use (public health, aviation, wildfire response, etc.).>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Global / Region name>
- **Domain details (optional):** <bounds / grid name / notable notes>

---

## Basic details
- **Model type:** <Air quality / atmospheric composition — append "(ensemble)" for ensemble configurations, e.g. "Air quality / atmospheric composition (ensemble)">
- **Model system / core:** <e.g., CMAQ, GEM-MACH, MONARCH, CHIMERE, IFS-COMPO, FV3-Chem, WRF-Chem> (TBD)
- **Horizontal resolution:** <e.g., ~12 km / 0.4° / ~40 km> (note native vs published grid if different)
- **Vertical levels:** <e.g., 35> (TBD if unknown)
- **Model top (optional):** <e.g., ~10 hPa> (TBD)
- **Forecast length:** <e.g., up to 72 h / 5 days> (mention per-cycle differences if applicable)
- **Update frequency / cycles:** <e.g., 2× daily (00/12 UTC), 4× daily, etc.>
- **Temporal output resolution:** <e.g., 1h / 3h> (TBD)

---

## Meteorological driver
- **Driving NWP model:** <e.g., GFS / IFS / GEM / HRRR> (TBD)
- **Coupling:** <Offline (one-way) / online (two-way)> (TBD)
- **Update source frequency (if offline):** <e.g., hourly meteorology from HRRR>

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** <e.g., CB6r3, SAPRC-07, MOZART-T1, RACM> (TBD)
- **Number of chemical species:** <e.g., ~150 gas-phase species> (TBD, optional)
- **Aerosol treatment:** <Bulk / sectional / modal> (TBD)
  - <If sectional: number of size bins>
  - <If modal: number of modes>
- **Aerosol components represented:** <e.g., sulfate, nitrate, ammonium, organic carbon, black carbon, dust, sea salt, primary PM>
- **Heterogeneous/aqueous chemistry:** <Yes/No and brief note if relevant> (TBD, optional)

---

## Emissions
- **Anthropogenic inventory:** <e.g., NEI, CAMS-GLOB-ANT, APEI> (TBD)
- **Biogenic emissions:** <e.g., MEGAN, BEIS> (TBD)
- **Wildfire emissions:** <e.g., GBBEPx, FINN, GFAS, CFFEPS> (TBD)
- **Dust scheme:** <Online / prescribed> (TBD, if applicable)
- **Sea salt scheme:** <Online / prescribed> (TBD, if applicable)
- **Other sources (if relevant):** <e.g., volcanic, lightning NOx, aircraft>

---

## Data assimilation (optional)
- **Assimilates AQ observations:** <Yes/No> (TBD)
- **Observation sources (if yes):** <e.g., surface monitor networks, satellite retrievals such as TROPOMI NO2 / MODIS AOD> (TBD)
- **Method:** <e.g., 3D-Var, hybrid EnKF> (TBD, optional)

---

## Ensemble configuration (ensemble systems only)

<**Delete this entire section for deterministic entries.** Do not leave it in place filled with TBDs — an empty ensemble block in a deterministic entry reads as missing research rather than as "not applicable.">

- **Ensemble type:** <Perturbation-based (one model, perturbed members) / multi-model (independently developed models run as members) / hybrid> (TBD)

  > State this before anything else. Air quality has a strong multi-model tradition — see [`cams-regional.md`](../models/air_quality_models/regional/eu/cams-regional.md), whose 11 members are separate operational models from different institutions. Spread there represents structural uncertainty between modelling approaches, not initial-condition sensitivity, and the two kinds are not interchangeable for downstream use.

- **Ensemble size:** <e.g., 20 perturbed + 1 control; for a multi-model system, the member count plus the constituent models and their operators> (TBD)
- **Source(s) of perturbations:** <List every source, not just the meteorological one. Common sources: the driving atmospheric ensemble (name it, and state member correspondence — is member *n* the composition response to atmospheric member *n*?); perturbed emissions; perturbed chemistry, deposition, or aerosol parameters; stochastic physics inside the composition model. State explicitly when the composition side is **not** independently perturbed, which is the common case.> (TBD)
- **Chemistry / emissions differences vs deterministic sibling:** <Ensemble configurations are often run with a reduced mechanism, fewer tracked species, or a simplified aerosol treatment to fit the member count into the compute budget. Record it here, or state that the configuration is identical.> (TBD)
- **Resolution, species, and output differences vs deterministic sibling:** <Ensemble composition output is frequently coarser, less frequent, and carries a thinner parameter set. Where the published parameter sets differ, name the missing fields rather than only giving counts. Note cycle-schedule differences too — the ensemble may publish fewer runs per day than the deterministic feed.> (TBD)
- **Member packaging:** <Separate file per member vs a member dimension in one file; GRIB2 `perturbationNumber` / `typeOfEnsembleForecast` encoding, or the NetCDF member dimension name. Note whether a control member exists and how it is distinguished — several operators publish none at all.> (TBD)
- **Derived products distributed:** <Ensemble mean, spread, exceedance probabilities against health or hazard thresholds, percentiles — list only what is published as raw gridded data, not what the operator renders in a viewer.> (TBD)

---

## What it provides
Forecasts of atmospheric composition and air quality, typically including:
- Ozone (O3)
- Particulate matter (PM2.5, PM10)
- Nitrogen oxides (NO, NO2)
- Carbon monoxide (CO)
- Sulfur dioxide (SO2)
- <Add specialty outputs if applicable: wildfire smoke, dust, volcanic ash, pollen, etc.>
- <Derived indices: AQI, AQHI, health-related indices if produced>

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., CC BY 4.0 / Open Government Licence – Canada / Public domain (U.S. government work; CC0-equivalent) / Copernicus licence (registration and licence acceptance required) / Etalab Open Licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Data formats:** <GRIB2 / NetCDF / both> (include compression notes if relevant)
- **Official download location:**  
  <URL>

---

## Notes
- <Anything important: scope of public data vs full operational, unusual update timing, quirks of domain coverage, known biases or limitations.>
- <Note whether the model is part of a broader system or programme (e.g., coupled with a specific NWP suite, a component of a national air quality programme like NAQFC, or a member of a multi-model ensemble like CAMS Regional).>
- <Mention any seasonal or episodic variation in product availability, such as wildfire-only runs.>
- <For AI-based or hybrid composition systems, note the AI approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the model has documented operational upgrades worth tracking — air quality systems change driving NWP model, emissions inventories, and chemistry versions often enough that this is frequently warranted. See `raqdps.md` and `aqm.md` for examples. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
