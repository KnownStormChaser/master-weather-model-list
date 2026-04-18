# GDPS (Global Deterministic Prediction System)

## What this model is
The Global Deterministic Prediction System (GDPS) is Canada’s primary global numerical weather prediction system, providing deterministic forecasts of atmospheric conditions worldwide.

GDPS is based on the Global Environmental Multiscale (GEM) atmospheric model and operates as part of a coupled Earth-system framework, supplying large-scale guidance and boundary conditions for Canada’s regional and high-resolution forecast systems.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Deterministic global NWP
- **Model system / core:** GEM (Global Environmental Multiscale)
- **Horizontal resolution:**  
  ~15 km (quasi-uniform Yin–Yang grid)
- **Vertical levels:** 84 hybrid staggered levels
- **Model top:** ~0.1 hPa
- **Forecast length:**  
  Up to ~10 days
- **Update frequency / cycles:**  
  2× daily (00, 12 UTC)
- **Temporal output resolution:**  
  Typically 3-hourly

---

## What it provides
Deterministic global forecasts of:
- Atmospheric temperature, wind, humidity, and pressure
- Surface and near-surface weather variables
- Large-scale circulation patterns
- Boundary and initial conditions for downstream systems

GDPS provides the large-scale deterministic guidance that underpins Canada’s regional (RDPS) and high-resolution (HRDPS) deterministic prediction systems.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-datamart_en/

---

## Notes
- GDPS uses a global **Yin–Yang grid**, a distinctive feature of modern GEM configurations.
- Data assimilation is performed using a **4D ensemble–variational (4DEnVar)** system, with background-error covariances derived from the GEPS ensemble.
- GDPS is part of a tightly integrated suite of Canadian operational prediction systems.

---

## Experimental AI-hybrid configuration (GDPS-SN)

ECCC has developed an experimental hybrid configuration of GDPS that integrates AI-based weather prediction into the physics-based forecast. In this system:

- The GEM physics model continues to run the full forecast integration.
- Large-scale temperature and horizontal wind components are spectrally nudged toward predictions from GEML (Global Environmental eMuLator), ECCC's data-driven AI weather model derived from GraphCast and fine-tuned on GDPS analyses.
- Nudging is applied only to mid-tropospheric large scales (approximately 400–250 hPa), leaving boundary-layer and mesoscale dynamics to GEM.

The hybrid system is referred to as GDPS-SN (spectral nudging). Published research (Husain et al., 2024/2025) reports meaningful skill improvements over the pure physics GDPS at medium-range lead times.

As of early 2026, GDPS-SN is distributed as **experimental data** through MSC GeoMet web services and the MSC Datamart at approximately 15 km (0.15°) horizontal resolution on ~30 vertical levels, run twice daily to 10-day lead time.

Experimental data access:
- MSC Datamart (experimental GDPS): https://dd.weather.gc.ca/today/model_gdps/
- MSC Open Data documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps_en/

ECCC's April 2026 supercomputer upgrade to H100 GPU infrastructure is the enabling hardware for operational AI-hybrid NWP — running neural network inference inside the operational forecast cycle requires GPU acceleration that was not previously available at CMC.

GDPS-SN is not yet an operational replacement for the standard GDPS described above, but represents the clearest indication of the direction Canadian global NWP is heading.

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps_en/
