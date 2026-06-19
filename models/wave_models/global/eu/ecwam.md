# ECWAM (ECMWF Wave Model)

## What this model is
ECWAM is the ocean wave component of ECMWF's **Integrated Forecasting System (IFS)**. It is a third-generation spectral wave model (a descendant of the original WAM) that forecasts significant wave height, wave period, and wave direction, together with wind-sea and swell partitions in the full operational output.

Within the IFS, ECWAM is two-way coupled to the atmospheric model (and, in the coupled Earth-system configuration, to the NEMO ocean model), so the sea state feeds back on surface roughness and air–sea fluxes rather than being a passive downstream product.

A reduced subset of ECWAM output is published through ECMWF's open data service at 0.25° in GRIB2, alongside the open IFS atmospheric fields.

> **Naming note:** This system was previously catalogued here as "ECMWF Wave Model (MFWAM)." That was a misnomer — ECMWF's wave model is **ECWAM**. **MFWAM** is the closely related *Météo-France* wave model, which is built on the ECWAM-IFS source code. See the two MFWAM entries for that distribution: [MFWAM Global (Copernicus)](../france/mfwam-copernicus.md) and [MFWAM GLOB01](../france/mfwam-global-france.md).

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Country / region:** International (intergovernmental organisation)

---

## What area it covers
- **Coverage:** Global oceans
- **Domain details (optional):** Global wave grid; open-data products delivered on a regular 0.25° lat-lon grid (~28 km)

---

## Basic details
- **Model type:** Deterministic wave model
- **Core wave model:** ECWAM (IFS wave component; third-generation WAM derivative)
- **IFS cycle:** Cycle 50r1 (operational since 12 May 2026)
- **Horizontal resolution:** 0.25° (~28 km) in the open-data subset; higher resolution available via the Product Requirements Catalogue under a service agreement
- **Forecast length:**
  - 240 h (10 days) — main HRES-WAM stream (`stream=wave`, 00/12 UTC)
  - 90 h — short cut-off wave stream (`stream=scwv`, 06/18 UTC)
- **Update frequency / cycles:** 4 cycles daily, split across two streams (00/12 UTC main; 06/18 UTC short cut-off)
- **Temporal output resolution:**
  - Main stream: 3-hourly to +144 h, then 6-hourly to +240 h
  - Short cut-off stream: 3-hourly to +90 h

---

## Forcing and nesting
- **Wind forcing:** IFS atmospheric model (10 m winds), two-way coupled — the wave model and atmosphere exchange fields each timestep rather than the waves being forced offline
- **Ice forcing (if applicable):** IFS sea-ice concentration (wave computation masked in ice-covered cells)
- **Current forcing (if applicable):** In the coupled Earth-system configuration, surface currents from the NEMO ocean component influence wave propagation (TBD: confirm whether current coupling is active in the operational HRES wave run vs. ENS only)
- **Nested inside / parent for:** Global parent; provides lateral boundary wave spectra to several regional systems (e.g., ARCWAM receives boundary spectra from ECMWF)

---

## Data assimilation (optional)
- **Assimilates wave observations:** Yes
- **Observation sources (if yes):** Satellite altimeter significant wave height; ECMWF also assimilates spectral wave observations (TBD: confirm current mission list — e.g. CFOSAT SWIM, Sentinel-1 — against the latest IFS cycle documentation)
- **Method / cadence (optional):** Wave analysis within the IFS assimilation cycle (TBD: confirm exact scheme/cadence)

---

## What it provides
Open-data wave fields include:
- significant height of combined wind waves and swell
- mean wave period
- peak wave period
- mean zero-crossing wave period
- mean wave direction

The full operational ECWAM additionally provides 2D wave spectra and wind-sea / swell partitions; these are **not** part of the free open-data subset.

---

## Data availability
- **Is the data free?** Yes (open-data subset)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0), plus the ECMWF Terms of Use. Redistribution and commercial use permitted with attribution to ECMWF.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (CCSDS compression since July 2023)
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/ifs/0p25/wave/<...>-wave-fc.grib2`
  - AWS Open Data mirror: https://registry.opendata.aws/ecmwf-forecasts/

---

## Notes
- **Open subset vs. full operational model.** The free data is a reduced-parameter, 0.25° extract of ECWAM, analogous to the open IFS atmospheric subset. The full-resolution, full-parameter wave products (including spectra and partitions) require a Real-time Dissemination service agreement.
- **Relationship to MFWAM (shared codebase).** Météo-France's MFWAM runs on the ECWAM-IFS source code (documented as ECWAM-IFS-38R2 in the Copernicus Marine global product). The lineage is bidirectional: the Ardhuin/Météo-France wind-input and deep-water dissipation parametrizations were adopted *back into* ECWAM in IFS Cycle 46r1 (June 2019). So ECWAM and MFWAM are cross-pollinated relatives rather than a simple fork — see [MFWAM Global (Copernicus)](../france/mfwam-copernicus.md) and [MFWAM GLOB01](../france/mfwam-global-france.md).
- **Companion ensemble stream.** ECMWF also publishes an open wave ensemble (`stream=waef`) at all four cycles, extending to +360 h (15 days) at 00/12 UTC. If catalogued, it belongs in a separate ensemble-wave entry rather than here.
- **Upcoming resolution change.** ECMWF has announced plans to raise the open-data resolution from 0.25° toward 0.125° (~14 km) to match the ERA6 grid. Monitor and update resolution/forecast specs when implemented.

---

## Recent version history (optional)
### IFS Cycle 50r1 — 12 May 2026
- Current operational cycle (see repository [`STATUS.md`](../../../../STATUS.md) for the IFS 50r1 / AIFS v2 changeset).

### March 2024
- Open-data resolution increased to 0.25° (~28 km); parameter subset expanded.

### IFS Cycle 46r1 — June 2019
- New wind-input and deep-water dissipation parametrizations (Ardhuin et al., via Météo-France) incorporated into ECWAM.

---

## Official documentation
- ECMWF open data (real-time forecasts from IFS and AIFS): https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Open data licence and parameter subset: https://www.ecmwf.int/en/forecasts/datasets/open-data
- IFS documentation (wave model / ECWAM): https://www.ecmwf.int/en/forecasts/documentation-and-support
