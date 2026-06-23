# COSMO-IL

## What this model is
COSMO-IL is the operational, convection-permitting regional NWP model run by the Israel Meteorological Service (IMS) for the Eastern Mediterranean / Levant. It is a COSMO (Consortium for Small-scale Modeling) limited-area configuration adapted for the region, focused on short-range high-impact weather. IMS joined the COSMO consortium in 2017 and develops the model through its R&D department.

---

## Who runs it
- **Organization:** Israel Meteorological Service (IMS)
- **Country / region:** Israel

---

## What area it covers
- **Coverage:** Eastern Mediterranean / Levant
- **Domain details:** The IMS COSMO domain figure spans roughly 26–38°E, 27–36°N (exact bounds not stated in text)

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** COSMO
- **Dynamical formulation:** Non-hydrostatic (COSMO; Runge–Kutta split-explicit)
- **Convection-allowing:** Yes — deep convection explicitly resolved; shallow convection parameterized (see Khain et al., 2021)
- **Horizontal resolution:** 2.8 km
- **Vertical levels:** 60
- **Model top:** ~23 km
- **Forecast length:**
  - Up to 90 hours (operational 00/12 UTC runs)
  - Up to 12 hours (hourly rapid-update runs on rainy days)
- **Update frequency / cycles:**
  - 2× daily (00/12 UTC) for full-range forecasts
  - Hourly during rainy / high-impact events
- **Temporal output resolution:** TBD

Map-update timing (operational runs): first maps ~06:05 / 18:05 UTC; full 90-hour map sets ~06:40 / 18:40 UTC.

---

## Data assimilation
- **Data assimilation:** Yes — real-time, on the rapid-update (rainy-day) runs
- **Observations assimilated:** radar, surface observations (Israel and surroundings), radiosondes, aircraft, and ship reports
- **Purpose:** improves short-range forecast skill for the following few hours

---

## Initial and boundary conditions
- **Boundary conditions:** ECMWF IFS

---

## What it provides
Deterministic forecasts of:
- temperature
- relative humidity
- wind
- pressure
- precipitation
- cloudiness
- snow accumulation
- additional diagnostic fields

---

## Data availability
- **Is the data free?** Yes — open to the public, **upon signing a Terms of Use form** (see access requirements below)
- **License:** IMS Terms of Use ("Rights and terms of use" form on the Data Access page) — https://ims.gov.il/sites/default/files/2023-05/terms%20of%20use.pdf
- **Is the data downloadable?** Yes
- **Data formats:** TBD — not stated on the IMS Data Access page; COSMO output is natively GRIB. Verify.
- **Data retention:** Portal holds the **last month** of data; older data by request to ims@ims.gov.il
- **Official download location:**
  https://ims.gov.il/en/node/179 (Radar & Models Products)

### Access requirements
Access is "open to the public, upon signing term of use." To obtain permissions, download the Terms of Use form, complete it, and send it to IMS (ims@ims.gov.il). See the repository scope note below regarding the permission step.

---

## Notes
- **Related ECMWF-HPC suite:** IMS also runs a 2.5 km COSMO suite on ECMWF's HPC facility under the Optional Programme for Co-operating States — the deterministic **CO-IL2.5** and the 20-member ensemble **CO-IL-EPS** (Eastern Mediterranean, 25–39°E / 26–36°N, twice daily, ICs/LBCs from ECMWF ENS+HRES; developed with Arpae-SIMC, using MeteoSwiss FieldExtra). This is a distinct configuration from the downloadable 2.8 km COSMO-IL; the resolution difference (2.8 km vs 2.5 km) reflects the two separate setups. CO-IL-EPS would warrant its own ensemble entry if/when its raw data access is confirmed.
- **Sibling model:** the IMS deterministic ICON-IL (ICON-LAM) covers a much larger southeast-Europe domain (separate entry).
- **Same portal:** the Data Access page also offers INCA nowcasts (1 km, 72 h) — tracked separately under nowcasting — alongside radar and rain-analysis graphics and meteograms (rendered images, out of scope).

---

## Recent version history

### 2014 — earlier local configuration (COSMO V4.26, 7 km + nested 2.8 km)
Semi-operational IMS setup: COSMO V4.26 at 7 km with a nested 2.8 km domain, 50 vertical levels, driven by IFS/GME, run on a local SGI Linux cluster / AMD cores (Khain et al., COSMO poster, Sep–Oct 2014).

---

## Official documentation
- IMS Radar & Models Products (data access): https://ims.gov.il/en/node/179
- IMS COSMO page: https://ims.gov.il/en/COSMO
- ECMWF Newsletter 171 — "Israel uses ECMWF supercomputer to advance regional forecasting" (2022): https://www.ecmwf.int/en/newsletter/171/earth-system-science/israel-uses-ecmwf-supercomputer-advance-regional-forecasting
- Khain, P. et al., 2021: Effect of shallow convection parametrization on cloud-resolving NWP forecasts over the Eastern Mediterranean. *Atmos. Res.* 247, 105213.
