# UK Met Office Global Wave Model

## What this model is
The UK Met Office Global Wave Model is an **operational numerical wave forecast system** used to predict ocean wave conditions for marine forecasting and safety applications.

It is based on the third-generation spectral wave model **WAVEWATCH III™ (WW3)** and is forced by winds from the Met Office Global Atmospheric High-Resolution Model.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** Global oceans

---

## Basic details
- **Model type:** Global deterministic wave model
- **Core model:** WAVEWATCH III™ (WW3)
- **Grid:** Regular latitude–longitude (WGS84)
- **Typical resolution:**  
  - N512 grid (~25 km at 50°N)  
  - 0.3515625° longitude × 0.234375° latitude
- **Forecast length:**  
  - Up to 144 hours (6 days) for 00Z and 12Z runs  
  - Up to 66 hours for 06Z and 18Z runs
- **Update frequency:** 4× daily (cycle-dependent forecast length)

---

## What it predicts
- Significant wave height
- Mean and peak wave period
- Mean wave direction
- Wind-sea and swell components (including primary, secondary, and tertiary swell)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **Official download location:**  
  https://registry.opendata.aws/met-office-global-wave/

---

## Notes
The Met Office Global Wave Model uses a refined grid system to better represent fetch in constrained seas and the blocking effects of islands and headlands.

Only a subset of the full operational wave products is publicly available via the Met Office Open Data programme.

---

## Official documentation
- https://www.metoffice.gov.uk/services/data
