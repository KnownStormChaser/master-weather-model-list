# INCA (Integrated Nowcasting through Comprehensive Analysis)

## What this model is
INCA (Integrated Nowcasting through Comprehensive Analysis) is GeoSphere Austria's operational analysis and nowcasting system for the near-surface atmosphere. It blends surface station observations, radar, satellite, an NWP first guess, and high-resolution terrain data into a 1 km gridded analysis of the current state, then extrapolates and blends that analysis into the NWP forecast to produce short-range forecasts (nowcasts) of temperature, precipitation, wind, and humidity.

INCA was specifically designed for complex mountainous terrain (the eastern Alps), using a valley-floor vertical coordinate and a terrain-aware "surface-layer index" so that observations are interpreted appropriately for valley-floor, slope, and ridge locations. It is used for flood warning and forecasting and underpins GeoSphere's spatially detailed weather web portals.

---

## Who runs it
- **Organization:** GeoSphere Austria (formerly ZAMG — Zentralanstalt für Meteorologie und Geodynamik; merged into GeoSphere Austria in 2023)
- **Country / region:** Austria

---

## What area it covers
- **Coverage:** Austria and the surrounding eastern Alpine region
- **Domain details:** The public nowcast grid is **701 × 431 points at 1 km** (~700 km × 430 km), on the MGI / Austria Lambert projection (EPSG:31287; standard parallels 46°N / 49°N, origin 47.5°N / 13.333°E). Bounding box ≈ 45.50–49.48 °N, 8.10–17.74 °E (confirmed from a distributed nowcast file). The hourly analysis product spans a slightly wider 45.77–49.48 °N, 7.1–17.74 °E.

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Seamless extrapolation–NWP blend, built on a comprehensive multi-source analysis. The analysis combines an NWP first guess with station, radar, and satellite data; nowcasts extrapolate the analysis and merge into the NWP forecast across the lead-time window.
- **Technique / algorithm:** For precipitation and cloudiness, Lagrangian persistence (classical advection with motion vectors from cross-correlation of consecutive analyses). For temperature, humidity, and wind, an Eulerian nowcast that applies the NWP model trend on top of the latest analysis. Terrain handling uses a valley-floor reference surface, a nondimensional surface-layer index, inverse-distance-squared interpolation in physical and potential-temperature space, and elevation-dependent precipitation combining rain gauges with radar.
- **Underlying / driving model:** The NWP first guess and trend are provided by GeoSphere's operational [AROME Austria](../../../nwp_models/regional/austria/arome-austria.md) (AROME-Aut). The foundational INCA papers (2011) describe the then-operational driver as ALADIN-Austria (9.6 km, 60 levels, 4× daily to +72 h); GeoSphere has since transitioned its operational limited-area model from ALADIN to AROME.
- **Probabilistic / ensemble:** No (deterministic)
- **Horizontal resolution:** 1 km
- **Vertical structure:** The public products are 2D near-surface fields (2 m temperature, dew point, and humidity; 10 m wind speed, direction, and gust; precipitation amount and type). Internally the analysis is three-dimensional, using a z-coordinate measured above a slowly varying valley-floor surface (temperature analysis on 21 levels at Δz = 200 m to ~4000 m above the valley floor; wind analysis on 32 levels at Δz = 125 m).
- **Lead time:** 0–3 h (public nowcast product). Internally the nowcast blends into the NWP forecast over a longer window, and INCA continues to add value to the NWP forecast through downscaling at longer ranges.
- **Update frequency:** Every 15 minutes (nowcast product). The companion analysis product updates hourly.
- **Temporal output resolution:** 15-minute steps (nowcast); 1-hour (analysis)
- **Latency:** Not documented (TBD)

---

## Inputs
- **NWP fields:** First-guess and trend fields from AROME Austria (historically ALADIN-Austria). 3D temperature, humidity, and wind plus 2 m / 10 m surface fields, precipitation, cloudiness, and ground temperature are used.
- **Radar:** The Austrian radar composite (C-band radars operated by Austro Control, the civil aviation authority), supplemented with data from neighbouring countries; the maximum constant-altitude plan position indicator (max-CAPPI) product is the main radar input. (The 2011 papers describe a four-radar composite, with a fifth radar then being added.)
- **Satellite:** Meteosat Second Generation (MSG) NWC-SAF cloud-type product plus the visible (VIS) image, at a 15-minute time step, used for the cloudiness analysis and nowcast.
- **Surface / other observations:** GeoSphere's network of ~250 TAWES semi-automated weather stations (average spacing ~18 km, spanning ~100–3800 m elevation), plus ~100 hydrometeorological stations. Observed quantities include 2 m temperature, relative humidity, dewpoint, 10 m wind, precipitation amount, and sunshine duration.
- **Terrain:** A high-resolution 1 km digital elevation model and derived fields (valley-floor surface, surface-layer index).

---

## Blending / seamless transition
For precipitation and cloudiness, the observation-based extrapolation receives full weight over roughly the first 2 hours and is then weighted down linearly to zero by about 6 hours, where the NWP forecast takes over. For temperature, humidity, and wind, the NWP trend is blended with the analysis over a stability-dependent timescale (about 3 hours in well-mixed conditions, up to ~12 hours under strong inversions). Beyond the nowcasting range, INCA continues to improve the NWP temperature forecast through terrain downscaling. The publicly distributed nowcast is capped at a +3 h horizon.

---

## What it provides
The public 15-minute nowcast product provides 0–3 h forecasts (13 steps at 15-min spacing, including the +0 analysis) of the following near-surface fields:
- 2 m air temperature (`TT`)
- 2 m dew point temperature (`TD`)
- 2 m relative humidity (`RH`)
- 10 m wind speed (`FF`), direction (`DD`), and gust (`FX`)
- 15-min accumulated precipitation (`RR`)
- precipitation type (`PT`)

(Variable list read directly from a distributed nowcast NetCDF file.) Precipitation type **is** included in the public nowcast dataset; in the inspected file it is an integer-coded field with 255 as the no-precipitation/fill value — the exact code table is not embedded in the file (flag).

The fuller INCA system additionally analyses and nowcasts cloudiness, global radiation, snowfall line, and ground temperature, and derives convective parameters (e.g. CAPE, CIN, LCL); those do not appear in the public 15-minute nowcast dataset.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-1.7)
- **File naming:** `nowcast_yyyyMMddHHmm.nc` (e.g. `nowcast_202607122230.nc`)
- **Official download location (nowcast, 15-min, +3 h):**
  https://public.hub.geosphere.at/public/datahub.html?id=nowcast-v1-15min-1km/filelisting
- **Companion analysis product (hourly, 1 km, archive from 2011):**
  https://public.hub.geosphere.at/public/datahub.html?id=inca-v1-1h-1km/filelisting
- **API access:** GeoSphere Austria's Dataset API provides programmatic access with subsetting (by area, time, parameter):
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing pages:**
  - Nowcast: https://data.hub.geosphere.at/en/dataset/nowcast-v1-15min-1km
  - Analysis: https://data.hub.geosphere.at/en/dataset/inca-v1-1h-1km
- **DOIs:**
  - Nowcast: https://doi.org/10.60669/ahad-4y43
  - Analysis: https://doi.org/10.60669/6akt-5p05

---

## Notes
- **Two related public products, one system.** The nowcast product (`nowcast-v1-15min-1km`) is the short-range forecast (+3 h, 15-minute updates) and is the subject of this entry. The analysis product (`inca-v1-1h-1km`) is the comprehensive analysis half of INCA — hourly gridded fields with an archive back to March 2011, useful for verification, downscaling, and reanalysis-style applications. It is analysis rather than forecast data, but shares the same system, grid, license, and API.
- **Relationship to the NWP suite:** INCA sits downstream of GeoSphere's NWP models, using [AROME Austria](../../../nwp_models/regional/austria/arome-austria.md) as its first guess and trend source; the [C-LAEF](../../../ensemble_models/regional/austria/c-laef.md) ensemble and AROME-RUC rapid-update system are separate products in the same operational suite.
- **INCA family:** INCA was developed at ZAMG and has been adopted beyond Austria (the EU "INCA-CE" Central European initiative; a Swiss configuration, INCA-CH, uses COSMO as its first guess). Several INCA domains exist across central Europe. Empirical coefficients in the analysis are tuned to the NWP first guess and may need recalibration for a different driving model.
- **Documented skill (eastern Alps):** Per the validation papers, the INCA temperature analysis has an MAE around 1 °C (larger, ~1.5–2.5 °C, in deep Alpine valleys where inversion height is uncertain), with humidity MAE/RMSE around 5 %/7 % and 10 m wind-speed MAE around 0.5–0.8 m s⁻¹. Nowcasts add value over the raw NWP forecast mainly in the first 2–3 h for precipitation and the first ~6 h for temperature. Independent verification against the high-density WegenerNet station network confirmed the analysis skill without evidence of overfitting.
- **Organizational note:** GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications and documentation refer to ZAMG.

---

## Official documentation
- Nowcast dataset (landing page): https://data.hub.geosphere.at/en/dataset/nowcast-v1-15min-1km
- Analysis dataset (landing page): https://data.hub.geosphere.at/en/dataset/inca-v1-1h-1km
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Haiden, T., Kann, A., Wittmann, C., Pistotnik, G., Bica, B., Gruber, C. (2011). *The Integrated Nowcasting through Comprehensive Analysis (INCA) System and Its Validation over the Eastern Alpine Region.* Weather and Forecasting, 26(2), 166–183. https://doi.org/10.1175/2010WAF2222451.1
- Kann, A., Haiden, T., von der Emde, K., Gruber, C., Kabas, T., Leuprecht, A., Kirchengast, G. (2011). *Verification of Operational Analyses Using an Extremely High-Density Surface Station Network.* Weather and Forecasting, 26(4), 572–578. https://doi.org/10.1175/WAF-D-11-00031.1
