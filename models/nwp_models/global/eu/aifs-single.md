# AIFS Single (Artificial Intelligence Forecasting System – Deterministic)

## What this model is
AIFS Single is ECMWF's operational machine-learning-based global deterministic weather forecast model.

Unlike the physics-based IFS, AIFS Single does not solve the equations of fluid dynamics explicitly. Instead, it uses a trained neural network to predict the evolution of the atmosphere directly from historical weather data, producing medium-range forecasts at a fraction of the computational cost of traditional NWP.

AIFS Single runs operationally alongside the physics-based IFS and is designed to complement rather than replace it.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium)

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic (AI-based)
- **Architecture:** Encoder–processor–decoder with attention-based graph neural networks (encoder/decoder) and a sliding-window transformer (processor)
- **Training data:** ERA5 reanalysis plus ECMWF operational analyses (fine-tuning)
- **Training framework:** Anemoi (open-source AI-NWP framework co-developed with ECMWF Member States)
- **Native grid:** N320 reduced Gaussian grid (~0.25°, ~31 km)
- **Processor grid:** O96 octahedral reduced Gaussian (~1°)
- **Open Data resolution:** 0.25°
- **Forecast timestep:** 6 hours
- **Forecast length:** Up to 15 days
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Initialization:** ECMWF operational analyses (the same analyses used to initialize IFS)

---

## What it provides
Deterministic global forecasts of:
- Temperature (surface and upper-air pressure levels)
- Wind (10 m and upper-air)
- Geopotential height
- Specific humidity
- Mean sea level pressure
- Total precipitation
- Additional surface variables (added in v1.1.0), including short-wave downward radiation and 100 m wind speed

AIFS Single has shown forecast skill gains of approximately one day in the medium range for several variables when compared to the physics-based IFS, particularly for upper-air fields and surface radiation.

---

## Relationship to other models
AIFS Single is the **deterministic AI companion** to ECMWF's physics-based IFS. It does not replace IFS — ECMWF continues to run both operationally and views them as complementary.

AIFS Single shares the same **initial conditions** as IFS (both start from ECMWF's operational analyses), which means their forecast differences reflect model behavior rather than initialization differences.

AIFS ENS is the ensemble counterpart trained with probabilistic (CRPS-based) methods — see separate entry.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (with CCSDS compression)
- **Official download locations:**
  - https://data.ecmwf.int/forecasts/
  - https://registry.opendata.aws/ecmwf-forecasts/

---

## Version history

### AIFS Single v1.0.0 — operational February 25, 2025
First operational version, replacing the pre-operational AIFS 0.2.1 that had been running since October 2023.

### AIFS Single v1.1.0 — operational August 27, 2025
Upgraded to address a precipitation forecast issue in v1.0.0. Introduced physical consistency constraints via bounding layers, an updated training schedule, and an expanded set of output variables.

### AIFS Single v2 — scheduled May 12, 2026
Adds a new **10 hPa pressure level** to the stratospheric component. Deploys on the same day as IFS Cycle 50r1 and AIFS ENS v2.

---

## Notes
- AIFS Single runs more than 10× faster than the physics-based IFS at comparable skill for many variables, with roughly 1,000× lower energy consumption per forecast.
- A known limitation of MSE-trained AI models is under-prediction of distribution tails — for example, AIFS Single produces a flatter distribution for total cloud cover than observations, under-representing the frequencies of completely clear and fully overcast conditions.
- The Open Data distribution is a curated subset of AIFS Single products. Higher-resolution versions may be available via ECMWF's Real-time Dissemination Service under separate agreements.
- AIFS Single is distributed under the same ECMWF Open Data access terms as IFS Open Data.

---

## Official documentation
- ECMWF AIFS overview: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- AIFS Single v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v2
- AIFS v1.1.0 technical paper: https://arxiv.org/abs/2509.18994
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data portal: https://www.ecmwf.int/en/forecasts/datasets/open-data
