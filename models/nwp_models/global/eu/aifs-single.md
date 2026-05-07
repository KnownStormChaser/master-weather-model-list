# AIFS Single (Artificial Intelligence Forecasting System – Deterministic)

## What this model is
AIFS Single is ECMWF's operational machine-learning-based global deterministic weather forecast model.

Unlike the physics-based [IFS](./ifs.md), AIFS Single does not solve the equations of fluid dynamics explicitly. Instead, it uses a trained neural network to predict the evolution of the atmosphere directly from historical weather data, producing medium-range forecasts at a fraction of the computational cost of traditional NWP.

AIFS Single runs operationally alongside the physics-based IFS and is designed to complement rather than replace it. It became the world's first machine-learning-driven forecast model fully supported in operations on 25 February 2025, and as of v1.1 has demonstrated forecast skill gains of approximately 12 to 24 hours over IFS in the medium range for several variables.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic (AI-based)
- **Architecture:** Encoder–processor–decoder with attention-based graph neural networks (encoder/decoder) and a sliding-window transformer (processor)
- **Dynamical formulation:** Not applicable — AIFS Single does not solve dynamical equations. It is a trained neural network that predicts atmospheric evolution directly from learned patterns in historical weather data.
- **Convection-allowing:** No (AIFS does not represent or parameterize convection explicitly; convective behaviour is implicit in what the network has learned at ~31 km resolution)
- **Training framework:** Anemoi (open-source AI-NWP framework co-developed with ECMWF Member States)
- **Training data:** ERA5 reanalysis (1979–2022) plus ECMWF operational analyses (fine-tuning)
- **Native grid:** N320 reduced Gaussian grid (~31 km, ~0.25°)
- **Initialization grid:** IFS analyses are interpolated from their native O1280 grid (~0.1°) down to N320 (~0.25°) for input to the AIFS model
- **Open Data resolution:** 0.25° regular latitude–longitude
- **Pressure levels:** 13 in v1.1 (50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa); 14 in v2 (adds 10 hPa)
- **Forecast timestep:** 6 hours
- **Forecast length:** Up to 15 days
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Initialization:** ECMWF operational analyses (the same analyses used to initialize the deterministic IFS, regridded from O1280 to N320)

---

## What it provides
Deterministic global forecasts of:
- Temperature (surface and upper-air pressure levels)
- Wind components (10 m, 100 m, and upper-air)
- Geopotential height
- Specific humidity
- Mean sea level pressure
- Total precipitation, convective precipitation, and snowfall
- Surface short-wave (solar) and long-wave radiation downwards
- Cloud cover (total, high, mid, low)
- Soil moisture and soil temperature (two layers)
- Total column water and runoff water equivalent

AIFS Single v2 (operational 12 May 2026) additionally provides:
- A new pressure level at **10 hPa** for geopotential, temperature, U, V, and vertical velocity (improving stratospheric representation)
- A new **fraction of snow cover** parameter
- A new **wave stream** providing significant wave height, mean wave period, mean wave direction, drag coefficient, and significant wave height partitioned across six period bands (10–12, 12–14, 14–17, 17–21, 21–25, and 25–30 seconds). This is the first time AIFS produces ocean wave forecasts.

---

## Relationship to other models
AIFS Single is the **deterministic AI companion** to ECMWF's physics-based [IFS](./ifs.md). It does not replace IFS — ECMWF continues to run both operationally and views them as complementary.

AIFS Single shares the same **initial conditions** as IFS (both start from ECMWF's operational analyses), which means their forecast differences reflect model behavior rather than initialization differences.

[AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md) is the ensemble counterpart trained with probabilistic (CRPS-based) methods.

ECMWF stops running its experimental machine learning model suite (Aurora, FourCastNet, GraphCast, Pangu-Weather) as of the joint AIFS/IFS upgrade on 12 May 2026.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (with CCSDS compression)
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution required
- **Retention:** Rolling archive of the most recent 12 forecast runs (~2–3 days)
- **Official download locations:**
  - https://data.ecmwf.int/forecasts/
  - https://registry.opendata.aws/ecmwf-forecasts/
  - Microsoft Azure and Google Cloud mirrors also available

Unlike IFS, AIFS Single is distributed at its native resolution (0.25°) — there is no higher-resolution version behind a paywall. The AIFS Single dissemination delay was removed when the model became operational on 25 February 2025; AIFS data is now released as soon as it is produced rather than at the end of the IFS dissemination schedule.

---

## Version history

### AIFS Single v0.2.1 — pre-operational, October 2023
First publicly available AIFS, distributed via ECMWF Open Data as an experimental model running 4× daily. Used a 13-pressure-level vertical structure and was initialized from regridded IFS analyses.

### AIFS Single v1.0 — operational 25 February 2025
First operational version. Marked a historic milestone as the first machine-learning-driven forecast model to be made fully operational by any major weather centre.

### AIFS Single v1.1 — operational 27 August 2025 (current)
Upgraded to address a precipitation forecast issue (point rain artefacts) caused by an over-emphasis of soil moisture in v1.0's training. The fix reduced the soil moisture training contribution by a factor of 100, yielding equivalent skill and bias but without the unphysical artefacts.

Initially implemented on 31 July 2025 but reverted on 1 August due to a `stepRange` GRIB2 metadata issue affecting six accumulated parameters (`cp`, `sf`, etc.). Re-implemented on 27 August 2025 after the metadata issue was fixed. The technical paper documenting v1.1 was published in September 2025 (Moldovan et al., arXiv:2509.18994).

### AIFS Single v2 — scheduled 12 May 2026
Major upgrade deployed jointly with [IFS Cycle 50r1](./ifs.md#upcoming-changes) and [AIFS ENS v2](../../../ensemble_models/global/eu/aifs-ens.md). Key changes:

- **New 10 hPa pressure level** for geopotential, temperature, horizontal winds, and vertical velocity. Sudden stratospheric warmings are now represented in AIFS forecasts.
- **New wave stream** — the first data-driven wave forecasts ECMWF has issued. Significant wave height forecasts improve by ~10% compared to the physics-based IFS Cycle 50r1 wave model.
- **New snow cover parameter** (FSCOV) — fraction of snow cover, with improved performance over Cycle 50r1.
- **Vertical velocity (W) changed from prognostic to diagnostic**, producing more physically consistent vertical velocities (Hadley cells now properly formed and reasonable in strength).
- **Improved training regime** — fine-tuning extended from 2018–2022 to 2018–2024 (two additional years of data, including IFS Cycle 50r1 esuite analyses), over 7,900 fine-tuning training steps. Pre-training is unchanged from v1.1 (ERA5 1979–2022 over 260,000 steps).
- **No changes to model architecture** — v2 is the same network as v1.1, just retrained.
- **Dissemination priority changes** — AIFS Single output priority drops from 90 to 20 on ECPDS (the new wave stream uses priority 30, matching IFS HRES-WAM).

The release candidate phase started 14 April 2026; test data is available via MARS (expver=105) and the open data testdata endpoint. v2 is being upgraded jointly with IFS Cycle 50r1 because v1.1 shows degraded performance when initialized from 50r1 esuite analyses — v2 has been fine-tuned specifically on 50r1 data.

Known v2 limitations include 2 m temperature degradations in the Arctic during winter (linked to changes in IFS Cycle 50r1 sea-ice–atmosphere coupling that v2 has not yet seen enough training examples of), some smoothing along the Antarctic sea-ice edge in summer wave forecasts, and continued limited skill at 50 and 100 hPa in the tropics.

---

## Notes
- AIFS Single runs more than 10× faster than the physics-based IFS at comparable or better skill for many variables, with roughly 1,000× lower energy consumption per forecast.
- A known limitation of MSE-trained AI models is under-prediction of distribution tails — for example, AIFS Single produces a flatter distribution for total cloud cover than observations, under-representing the frequencies of completely clear and fully overcast conditions. AIFS v2 addresses this somewhat by being less smooth than v1.1, though this also produces small "skill" degradations at longer lead times for some variables.
- Forecasts blur at longer lead times (a known issue with weighted-MSE-trained AI models). The blurring is reduced but not eliminated in v2.
- Stratospheric skill has historically been a weakness due to the model's coarse vertical structure (13 pressure levels in v1.1, with the highest at 50 hPa). v2's addition of the 10 hPa level significantly improves skill at 50 and 100 hPa in the extratropics, though the tropical stratosphere remains a known weakness.
- Tropical cyclone intensity has historically been under-predicted; AIFS does not currently fully resolve high-impact storm intensity.
- Due to the non-determinism of GPU computation, users running AIFS Single themselves cannot exactly reproduce ECMWF's official forecasts.
- AIFS Single is distributed under the same ECMWF Open Data access terms as IFS Open Data. Like IFS, the entire ECMWF Real-time Catalogue (including AIFS) became CC-BY-4.0 licensed on 1 October 2025.

---

## Official documentation
- AIFS Single v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v2
- AIFS Single v1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v1
- ECMWF AIFS overview: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- AIFS Blog: https://www.ecmwf.int/en/about/media-centre/aifs-blog
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data portal: https://www.ecmwf.int/en/forecasts/datasets/open-data

### Key references
- Moldovan et al. (2025). *An update to ECMWF's machine-learned weather forecast model AIFS.* arXiv:2509.18994. https://arxiv.org/abs/2509.18994
- Lang et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv:2406.01465. https://arxiv.org/abs/2406.01465
