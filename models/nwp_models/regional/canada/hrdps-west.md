# HRDPS-West (High-Resolution Deterministic Prediction System – West)

> ⚠️ **Experimental system.** HRDPS-West is distributed as experimental data through the MSC alpha datamart, not the standard operational MSC datamart. It is not a replacement for the operational [HRDPS](./hrdps.md), which continues to provide 2.5 km guidance across Canada including Southern BC.

## What this model is
HRDPS-West is an experimental kilometric-scale numerical weather prediction system operated by Environment and Climate Change Canada (ECCC), providing ~1 km horizontal resolution forecasts over Southern British Columbia.

It is built on the Global Environmental Multiscale (GEM) atmospheric model and is piloted by the operational HRDPS national system, which provides initial and boundary conditions including hydrometeor fields. Surface initial conditions come from the Canadian Land Data Assimilation System (CaLDAS).

The current experimental version is HRDPS-West 1.5.0, released November 6, 2024.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Southern British Columbia
- **Domain details:**  
  1350 × 1200 grid points covering Southern BC, with the domain extending into adjacent regions of Alberta, Washington State, and the northeast Pacific

---

## Basic details
- **Model type:** Experimental deterministic NWP (convection-permitting)
- **Model system / core:** GEM (Global Environmental Multiscale), version 5.2.0
- **Horizontal resolution:**  
  ~1 km
- **Vertical levels:** 62
- **Forecast length:**  
  Up to 48 hours
- **Update frequency / cycles:**  
  2× daily (00, 12 UTC)
- **Temporal output resolution:**  
  Hourly

---

## Model characteristics
HRDPS-West 1.5.0 includes several notable configuration choices:

- **SLEVE vertical coordinate system:** Smooths vertical levels to reduce numerical errors and improve model stability over regions with complex topography — particularly relevant given the mountainous terrain across Southern BC.
- **Bourgouin precipitation phase partitioning:** Improves surface precipitation phase partitioning and enhances consistency with precipitation type diagnostics.
- **Updated geophysical fields:** Improved representation of surface features and properties.
- **Sea/lake ice processing:** Updated initialization and processing of sea and lake ice conditions.

---

## Initial and boundary conditions
- **Atmospheric initial and boundary conditions:** HRDPS national version 7.0.0 (provides initial and boundary conditions for atmospheric fields including hydrometeors)
- **Surface initial conditions:** Canadian Land Data Assimilation System (CaLDAS)

---

## What it provides
Experimental high-resolution deterministic forecasts of:
- Near-surface temperature, wind, and humidity
- Precipitation (with improved rain/snow phase partitioning)
- Localized hazardous weather over complex terrain
- Boundary-layer and topographically-driven effects (relevant for BC's mountain ranges, fjords, and coastal interfaces)

The 1 km resolution is intended to better resolve mesoscale features in Southern BC's complex topography that the operational 2.5 km HRDPS cannot fully represent.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location (alpha/experimental):**  
  https://dd.alpha.weather.gc.ca/model_hrdps/west/1km/grib2/
- **Documentation:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart-alpha_en/
- **Changelog:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/changelog_hrdps-west_en/

---

## Version history

### HRDPS-West 1.5.0 — released November 6, 2024 (current)
- GEM upgraded to version 5.2.0, introducing updated physics and bug fixes
- SLEVE vertical coordinate system implemented
- Bourgouin approach adopted for surface precipitation phase partitioning
- Pilot system upgraded to HRDPS national version 7.0.0
- Updated geophysical fields for better representation of surface features and properties
- Sea/lake ice initialization and processing changes

---

## Status
- HRDPS-West is distributed as **experimental data** via the MSC alpha datamart.
- It is not operationally supported in the same sense as HRDPS, GDPS, RDPS, or other operational ECCC prediction systems.
- Users requiring operational guidance over Southern BC should continue to use the operational [HRDPS](./hrdps.md) at 2.5 km resolution.
- HRDPS-West is one of several experimental ECCC systems alongside [GDPS-SN](../../global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn) — see the repository's [`STATUS.md`](../../../STATUS.md) for the current list.

---

## Notes
- HRDPS-West is convection-permitting at 1 km resolution and does not use a deep convection parameterization.
- The Southern BC domain reflects the operational priorities of the region: complex terrain, atmospheric river events, mountain meteorology, and dense population in the Lower Mainland and southern Vancouver Island.
- As an experimental system, configuration, domain, resolution, and product availability may change without the formal change-management process used for operational ECCC systems.
- The "alpha" designation in the datamart URL indicates the distribution channel itself is experimental, not just the model — users building automated pipelines should be aware that alpha endpoints may change without notice.
- HRDPS-West is the convection-permitting western counterpart to the pan-Canadian operational [HRDPS](./hrdps.md). Whether HRDPS-West eventually becomes operational, expands to additional regional domains (e.g., a hypothetical HRDPS-East), or is folded into a future revision of HRDPS itself is not publicly documented.

---

## Official documentation
- HRDPS-West alpha datamart README:  
  https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart-alpha_en/
- HRDPS-West changelog:  
  https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/changelog_hrdps-west_en/
- HRDPS-West 1.5.0 factsheet (Environment and Climate Change Canada, November 2024)
