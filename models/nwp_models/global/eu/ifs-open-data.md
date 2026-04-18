# IFS (Integrated Forecasting System) – Open Data

## What this model is
A global weather forecast model that predicts weather conditions such as temperature, wind, precipitation, and pressure for the entire Earth.

This entry refers specifically to the **publicly available open-data version** of the ECMWF IFS.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** European Union / international

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global
- **Typical resolution:** ~25 km (reduced compared to full ECMWF model)
- **Forecast length:** Up to 15 days
- **Update frequency:** 2× daily

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://data.ecmwf.int/forecasts/

  https://registry.opendata.aws/ecmwf-forecasts/

---

## Notes
The open-data version provides fewer variables and lower resolution than ECMWF's full operational model, but it is still widely used and considered high quality.

ECMWF's operational IFS runs on a roughly annual major cycle upgrade schedule. Each cycle can shift skill characteristics, biases, and variable distributions — users running long backtests or climatologies should split evaluation windows around cycle transition dates.

---

## Upcoming changes

### IFS Cycle 50r1 — operational May 12, 2026
ECMWF has scheduled IFS Cycle 50r1 for operational implementation on May 12, 2026, affecting both the atmospheric and wave components of the medium-range forecast.

Key points for Open Data users:
- **No change in horizontal resolution, vertical resolution, or forecast steps** for either the atmospheric or wave model.
- Ocean and sea-ice core moves from NEMO 3.4 + LIM2 to NEMO4-SI3, with fully active coupling.
- Coupled data assimilation becomes more central, with a 12-hour ocean analysis alongside the atmospheric analysis.
- Physics and assimilation improvements target the quasi-biennial oscillation (QBO), stratospheric winds, stratospheric humidity, and 10 m wind ensemble spread.
- Stream/archive handling changes: ENS control forecasts move under the operational stream, and wave control forecasts move under the wave stream. This primarily affects MARS/dissemination users rather than Open Data users.

### IFS Cycle 50r2 — full GRIB2 transition (tentative Q4 2026)
Cycle 50r2 will complete ECMWF's migration to a GRIB2-only representation for all parameters. This affects Open Data users directly.

Key points:
- All Open Data parameters will move from mixed GRIB1/GRIB2 to GRIB2 only.
- Legacy GRIB1-style parameter references (e.g., `165.128`) move to GRIB2-native parameter identifiers.
- Some level-type handling changes.
- GRIB2 output may use CCSDS compression, which requires decoder support (for example, `libaec`).
- Users with custom extraction, parameter-matching, or decoder logic should validate against ECMWF's test datasets prior to cutover.

ECMWF has made migration resources available:
- Static test dataset: available since September 2025
- Migration documentation: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB+edition+2+Information+page
- GRIB2 encoding changes: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB2+-+changes+to+encoding+of+parameters

## Official documentation
- Open Data dataset page: https://www.ecmwf.int/en/forecasts/datasets/open-data
- Changes to the forecasting system: https://confluence.ecmwf.int/display/FCST/Changes+to+the+forecasting+system
- Planned model changes: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- IFS Cycle 50r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+50r1
- ECMWF Newsletter 185 (Cycle 50r1 overview): https://www.ecmwf.int/en/newsletter/185/earth-system-science/upgrade-ifs-cycle-50r1
