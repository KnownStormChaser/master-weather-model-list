# AROME France

## What this model is
AROME France is the operational convection-permitting regional deterministic numerical weather prediction model run by Météo-France over metropolitan France and surrounding Western Europe.

It is designed for **short-range forecasting and nowcasting** of high-impact weather such as thunderstorms, heavy precipitation, fog, local wind effects, and convection over complex terrain. The system has been operational since December 2008, with the most recent major upgrade — a transition to a 3DEnVar data assimilation scheme — operationalized in October 2024.

AROME France is one model in a wider AROME family that also includes [AROME France HD](#related-arome-systems), 15-minute rapid-update variants, and the AROME Outre-Mer suite covering France's overseas territories.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and surrounding regions of Western Europe
- **Operational domain:** EURW1S40
  - Approximate bounds: **55.4°N – 37.5°N**, **12°W – 16°E**
  - Native grid is trapezoidal (not strictly rectangular)

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core)
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 1.3 km native resolution; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude
- **Vertical levels:** 90 (lowest model level at ~5 m above ground; model top at 10 hPa)
- **Forecast length:** Up to 51 hours
- **Update frequency:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** Hourly
- **Data assimilation:** 3DEnVar in the OOPS framework, 1-hour cycle, with Incremental Analysis Update (IAU); operational since October 2024 (cy48t1_op1). Previously 3D-Var.
- **Time step:** 50 s
- **Numerical precision:** Uncycled forecasts run in single precision

---

## What it provides
Deterministic forecasts of a wide range of atmospheric fields, including:
- Temperature, humidity, wind
- Surface and mean sea-level pressure
- Precipitation (rain, snow, graupel)
- Cloud cover and cloud microphysics
- Convective indices (e.g. CAPE, CIN)
- Boundary-layer and radiation parameters
- Wind gusts at 10 m
- Simulated radar reflectivity

Data are provided on:
- Surface levels (2 m temperature/humidity, 10 m wind, etc.)
- Standard isobaric pressure levels
- Selected height levels above ground (typically 20–3000 m)

Lateral boundary conditions are provided by the parent [ARPEGE global model](../../global/france/arpege-global.md).

---

## Data availability

AROME France data is openly licensed under the Etalab Open Licence and is available through three parallel channels, all carrying the same underlying forecasts. Météo-France distributes outputs as **parameter group packages** following WMO GFNC naming conventions (typically SP1, SP2, SP3 for surface fields and HP1, IP1 for upper-air fields).

### 1. Météo-France public API (`public-api.meteofrance.fr`)
The official developer portal provides AROME data through both bulk GRIB packages and an OGC Web Coverage Service (WCS) for single-variable subsetting.

- **Bulk packages:**  
  `https://public-api.meteofrance.fr/previnum/DPPaquetAROME/v1/models/AROME/grids/0.025/packages/{SP1|SP2|SP3|HP1|IP1}/productARO?...`
- **WCS single-variable:**  
  `https://public-api.meteofrance.fr/public/arome/1.0/wcs/MF-NWP-HIGHRES-AROME-0025-FRANCE-WCS/GetCoverage?...`
- **Authentication:** Free API key required. Register and manage keys at https://portail-api.meteofrance.fr
- **Documentation:** https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/

### 2. data.gouv.fr open-data portal (`object.data.gouv.fr`)
The French government's open-data S3-style object storage hosts the same GRIB packages with no authentication required.

- **URL pattern:**  
  `https://object.data.gouv.fr/meteofrance-pnt/pnt/{run}/arome/0025/{package}/arome__0025__{package}__{packageTime}__{run}.grib2`  
  where `{run}` is in ISO-8601 form (e.g. `2026-05-01T00:00:00Z`).
- **Dataset landing page:**  
  https://meteo.data.gouv.fr/datasets/65bd1247a6238f16e864fa80
- **Authentication:** None
- **Use case:** Best for users who want unauthenticated bulk access without registering for an API key.

### 3. Community AWS mirror (`mf-models-on-aws.s3.amazonaws.com`)
A community-run mirror, not affiliated with Météo-France, that redistributes the same data on AWS. Publicly listed in the AWS Open Data Registry.

- **URL pattern:**  
  `https://mf-models-on-aws.s3.amazonaws.com/arome-france/v1/{YYYY-MM-DD}/{HH}/{PACKAGE}/{TIMEPACK}.grib2`
- **Inventory files:** Companion `.inv` files (replace `.grib2` with `.inv` in the URL) support partial downloads via HTTP Range requests.
- **AWS Open Data Registry listing:** https://registry.opendata.aws/meteo-france-models/
- **Documentation:** https://mf-models-on-aws.org/en/doc/datasets/v1/
- **Authentication:** None
- **Caveat:** The mirror currently distributes a forecast range up to 42 hours rather than the full 51 hours available from the official channels. Operated independently and not subject to Météo-France's operational change-management process.

### Summary
- **Is the data free?** Yes (Etalab Open Licence)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (compressed)

---

## Related AROME systems

AROME France is one of several closely related AROME configurations operated by Météo-France:

- **AROME France HD** — Higher-resolution version covering France at ~1.5 km (0.01°), with a smaller selection of variables. Forecast length up to 51 hours, 8× daily.
- **AROME France PI 15min** (and HD 15min) — Rapid-update 15-minute output variants providing 6-hour forecasts updated hourly, intended for nowcasting use cases.
- **[AROME Outre-Mer](./arome-outre-mer.md)** — Tropical and equatorial configuration covering five overseas domains (Antilles, French Guiana, New Caledonia, French Polynesia, and Réunion–Mayotte), all at 0.025° (~2.5 km) public distribution.

All AROME variants share the same core dynamical model, physics packages, and distribution architecture; they differ in domain, resolution, forecast range, and update cadence.

---

## Notes
- The native horizontal resolution of 1.3 km is finer than the public distribution grid of 0.025° (~2.5 km). Forecasts on the open-data servers are interpolated from the native grid; users requiring full native resolution should consult Météo-France about their commercial/research data services.
- The native AROME grid is trapezoidal; GRIB files distributed on the regular latitude–longitude grid may contain **missing values** outside the effective domain. Users may need to crop or mask data when post-processing.
- AROME France's operational record dates from December 2008, with major upgrades in 2015 (resolution increased from 2.5 km to 1.3 km native, vertical levels from 60 to 90, hourly DA cycling), 2022 (additional refinements), and October 2024 (transition from 3D-Var to 3DEnVar data assimilation).
- The companion ensemble system **AROME-EPS** (16 members) provides probabilistic guidance over the same domain.
- For the relationship between AROME France and its larger global parent model, see [ARPEGE](../../global/france/arpege-global.md).

---

## Official documentation
- Météo-France public API documentation: https://portail-api.meteofrance.fr
- API Paquets Modèles overview (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- AROME technical description (PDF): https://donneespubliques.meteofrance.fr/client/documentation/descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf
- Météo-France public data portal: https://donneespubliques.meteofrance.fr
- AWS Open Data Registry — Atmospheric Models from Météo-France: https://registry.opendata.aws/meteo-france-models/
- Community mirror documentation: https://mf-models-on-aws.org/en/doc

### Key references
- Seity et al. (2011), *The AROME-France Convective-Scale Operational Model*, Monthly Weather Review 139(3): 976–991.
- Brousseau et al. (2016), *Improvement of the forecast of convective activity from the AROME-France system*, Quarterly Journal of the Royal Meteorological Society 142(699): 2231–2243.
