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
- **Model type:** Air quality / atmospheric composition
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

## What it provides
Forecasts of atmospheric composition and air quality, typically including:
- Ozone (O3)
- Particulate matter (PM2.5, PM10)
- Nitrogen oxides (NO, NO2)
- Carbon monoxide (CO)
- Sulfur dioxide (SO2)
- <Add specialty outputs if applicable: wildfire smoke, dust, volcanic ash, pollen, etc.>
- <Derived indices: AQI, health-related indices if produced>

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **Is the data downloadable?** Yes / No
- **Data formats:** <GRIB2 / NetCDF / both> (include compression notes if relevant)
- **Official download location:**  
  <URL>

---

## Notes
- <Anything important: scope of public data vs full operational, unusual update timing, quirks of domain coverage, known biases or limitations.>
- <Note whether the model is part of a broader system (e.g., coupled with a specific NWP suite, or part of a national air quality program).>
- <Mention any seasonal or episodic variation in product availability, such as wildfire-only runs.>

---

## Official documentation
- <URL>
- <URL>
