# ICON-CH-EPS (Switzerland Ensemble Prediction System)

## What this model is
A high-resolution regional ensemble weather forecast system that predicts weather conditions such as temperature, wind, precipitation, and pressure over Switzerland.

It runs multiple forecasts with small variations to estimate forecast uncertainty.

---

## Who runs it
- **Organization:** : MeteoSwiss
- **Country / region:** Switzerland

---

## What area it covers
- **Coverage:** Switzerland and nearby Alpine regions

---

## Basic details
- **Model type:** Regional (ensemble)
- **Typical resolution:**  
  - **ICON-CH1-EPS:** ~1 km (Switzerland-focused domain)  
  - **ICON-CH2-EPS:** ~2 km (larger surrounding domain)
- **Forecast length:** Up to ~33 hours for CH1 and 120 hours for CH2
- **Update frequency:** 8x daily for CH1 and 4x daily for CH2

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://www.meteoswiss.admin.ch/services-and-publications/service/open-data.html

---

## Notes
ICON-CH-EPS is MeteoSwiss’s highest-resolution ensemble forecasting system and is especially useful for complex terrain such as the Alps.

The two configurations differ mainly in resolution and domain size but are part of the same ensemble system.

---

## Official documentation
- https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model
