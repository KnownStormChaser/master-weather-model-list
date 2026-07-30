# FMI-ENFUSER (Helsinki Metropolitan Area urban air quality forecast)

## What this model is
FMI-ENFUSER (the Finnish Meteorological Institute's **ENvironmental information FUsion SERvice**) is an urban-scale Gaussian puff-and-plume dispersion model with continuous air-quality-measurement-driven data assimilation. It forecasts near-surface pollutant concentrations at breathing height on a **~13 m grid** — NO2, O3, PM10, PM2.5, an air quality index, and three health-exposure metrics (black carbon, particle number concentration, and lung-deposited surface area). Its primary use is public health information for the Helsinki metropolitan area, feeding the HSY air quality map and the info screens on local tram and metro lines.

Its defining feature is the fusion layer rather than the dispersion physics: the system continuously ingests data from the local AQ monitoring network to drive both real-time adaptation for accuracy and longer-term learning mechanisms, with regional chemical background supplied by [SILAM](./silam-hires.md).

ENFUSER is operational in **both Helsinki and Turku**, but only the Helsinki domain is publicly distributed. FMI states the Turku data is provided continuously for the city's own use and is not publicly available.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI) — Atmospheric Dispersion Modelling group, Atmospheric Composition Research
- **Country / region:** Finland (domain: Helsinki metropolitan area)
- **Service partners:** Helsinki Region Environmental Services Authority (HSY) operates the AQ monitoring network that drives the assimilation and publishes the public-facing air quality map

---

## What area it covers
- **Coverage:** Helsinki metropolitan area (Helsinki, Espoo, Vantaa, Kauniainen and immediate surroundings)
- **Domain details (live-verified 2026-07-29/30):** Regular latitude–longitude grid, **2632 × 2019 points**, bounds **24.58°E–25.1998°E, 60.1321°N–60.368°N**. Grid spacing measured from the NetCDF coordinate arrays: 0.00011695° latitude (**13.02 m**) and 0.00023567° longitude (**13.05 m** at 60.25°N) — square ~13 m cells. This sits at the fine end of the 10–15 m range FMI documents for the modelling system.
  - Note: the WFS `gml:offsetVector` values (0.0262 and 0.0130) are both degrees × 111.32 with no cosine-of-latitude correction applied to the longitude axis, so the WFS metadata implies 26 m east–west. The 13 m figure above is from the delivered coordinate arrays and is the correct one.

---

## Basic details
- **Model type:** Urban-scale air quality dispersion with data assimilation (surface concentrations at breathing height)
- **Model system / core:** FMI-ENFUSER — Gaussian puff and plume dispersion, combined with information fusion algorithms and statistical/machine-learning calibration (Johansson et al. 2022)
- **Horizontal resolution:** ~13 m delivered. The model additionally uses **5 m resolution GIS data** to describe the urban landscape, vegetation and ground elevation.
- **Vertical levels:** Surface only — near-surface concentrations at breathing height. The `levels=` query parameter is accepted but **ignored**; `levels=` 0, 1, 2 and 10 all return byte-identical files.
- **Model top:** Not applicable (surface dispersion product)
- **Forecast length:** **39 h** (T+0 to T+39, 40 hourly steps). Live-verified: T+39 returns data, T+40 returns an empty response.
- **Update frequency / cycles:** **2-hourly at even UTC hours** (00/02/04/06/08/10/12/14/16/18/20/22 UTC — odd hours return nothing). Nominally 12× daily.
- **Temporal output resolution:** 1 hour
- **Data retention:** **Seven cycles** (~14 h). No archive.

> ⚠ **Cycle currency needs re-verification.** During two checks 8 hours apart — 19:00 UTC on 2026-07-29 and 03:22 UTC on 2026-07-30 — the WFS returned the identical set of seven runs, the newest being **14 UTC on 29 July** in both cases. That is 13+ hours with no new cycle. This could be a temporary production stall, a scheduled overnight gap, or a longer-running outage; two observations cannot distinguish them (**TBD**). Confirm the update cadence against the live feed before relying on the 2-hourly figure above, and consider raising it with FMI open data support if it persists.

---

## Meteorological driver
- **Driving NWP model:** FMI's model description page names **HIRLAM and ECMWF** as meteorological data sources. **This is very likely stale** — HIRLAM has been retired (the HIRLAM consortium dissolved at the end of 2025) and FMI's operational NWP is now the MetCoOp HARMONIE-AROME production, so the current driver is most plausibly [MEPS](../../../nwp_models/regional/finland/harmonie-fmi.md) plus ECMWF [IFS](../../../nwp_models/global/eu/ifs.md). **Not confirmed (TBD)** — worth asking FMI directly, as was done for the [SILAM Hires](./silam-hires.md) driver.
- **Coupling:** Offline (one-way)
- **Update source frequency:** TBD
- **High-resolution wind (other domains only):** In the Turku domain, ENFUSER has been modified to consume building-resolving wind fields pre-computed with the **PALM** large-eddy simulation model at 4 m and 8 m for a set of wind directions, since running LES operationally is not computationally feasible. This LES-assisted configuration runs operationally in Turku for particulate matter. **It is not part of the publicly distributed Helsinki product.**

---

## Chemistry and aerosols
> ENFUSER is a **dispersion-and-fusion** system, not a chemical transport model. It does not carry a gas-phase chemical mechanism of its own; photochemistry and regional secondary aerosol enter through the SILAM background rather than being computed locally.

- **Gas-phase chemical mechanism:** None internal. Regional chemistry supplied by coupling to [SILAM](./silam-hires.md) (**TBD** which SILAM configuration provides the background — the 0.833 km hires Finland run is the obvious candidate but is not stated)
- **Number of chemical species:** Not applicable — eight output quantities (see *What it provides*)
- **Aerosol treatment:** Not applicable in the CTM sense. Particulate output is carried as PM10, PM2.5, black carbon, particle number and lung-deposited surface area rather than as a speciated aerosol state.
- **Aerosol components represented:** TBD — no speciated aerosol breakdown is distributed
- **Heterogeneous/aqueous chemistry:** Not applicable (inherited via the SILAM background)

---

## Emissions
- **Anthropogenic inventory:** Local-scale emissions derived from high-resolution GIS and **OpenStreetMap** data describing the road network, buildings, vegetation and terrain. Road traffic is the dominant modelled local source.
- **Residential heating:** A residential heating inventory supplied by local authorities is used in the Helsinki area
- **Shipping:** Local shipping emissions can be included via FMI's **STEAM** ship emission model (Johansson et al. 2017)
- **Biogenic emissions:** TBD
- **Wildfire emissions:** TBD — long-range smoke would enter through the SILAM background rather than being modelled locally
- **Dust scheme:** Road dust is addressed in the ENFUSER context by published machine-learning-assisted dispersion work (Kassandros et al. 2023); whether that configuration is in the operational Helsinki chain is **TBD**
- **Sea salt scheme:** TBD (inherited via SILAM background)

---

## Data assimilation
- **Assimilates AQ observations:** **Yes — this is the system's defining feature.** FMI describes the novelty of ENFUSER as "the continuous utilization of AQ measurement data," serving two purposes: real-time adaptation for better accuracy, and longer-term learning mechanisms.
- **Observation sources:** The local air quality monitoring network in the Helsinki metropolitan area (operated by HSY), plus archived historical concentration time series used for model calibration
- **Method:** Information fusion / statistical assimilation rather than a variational or ensemble scheme. Full method in Johansson et al. (2022). Exact formulation **TBD**.
- **Byproduct capabilities:** Because the assimilation continuously compares model and sensor values, FMI cites AQ sensor benchmarking and online drift correction, and optimal measurement-site selection, as documented secondary use cases.

---

## What it provides

Eight quantities, all live-verified as populated with plausible values (2026-07-29 14 UTC cycle). **The eight are split across two time axes**, which changes their interpretation:

### Backward hourly means (`time_h` axis, with `time_bounds_h`)

| `param` | NetCDF variable | Units |
|---|---|---|
| `NO2Concentration` | `mass_concentration_of_nitrogen_dioxide_in_air_4902` | µg/m³ |
| `O3Concentration` | `mass_concentration_of_ozone_in_air_4903` | µg/m³ |
| `PM10Concentration` | `mass_concentration_of_pm10_ambient_aerosol_in_air_4904` | µg/m³ |
| `PM25Concentration` | `mass_concentration_of_pm2p5_ambient_aerosol_in_air_4905` | µg/m³ |

The bounds run `[-1,0]`, `[0,1]`, `[1,2]`, so each value is the mean over the **preceding** hour — and the first step averages the hour *before* the nominal analysis time.

### Instantaneous values (plain `time` axis)

| `param` | NetCDF variable | Units |
|---|---|---|
| `AQIndex` | `index_of_airquality_194` | *(no units attribute)* |
| `BlackCarbonConcentration` | `BlackCarbonConcentration_4899` | µg/m³ |
| `ParticleNumberConcentration` | `ParticleNumberConcentration_4897` | cm⁻³ |
| `LungDepositedSurfaceArea` | `LungDepositedSurfaceArea_4898` | `um2/cm` |

**Availability caveat on the health metrics.** FMI states that black carbon, particle number concentration and lung-deposited surface area "may also be provided, **depending on AQ measurement data availability**." All three were populated in the cycle tested, but they should be treated as conditionally available rather than guaranteed.

**The AQ index is not the categorical Finnish AQI.** Values are quantized to 0.1 and spanned 1.0–2.5 in the sample (15 distinct values). It is a continuous or interpolated index, not the five-class Finnish air quality index banding (good / satisfactory / fair / poor / very poor). No `flag_values` or `flag_meanings` attributes are present, and the index definition and scale endpoints are **TBD**.

**No CO, SO2, NO, PM1, meteorological or pollen fields.** Probes for `COConcentration`, `SO2Concentration`, `NOConcentration`, `PM1Concentration`, `Temperature`, `WindSpeedMS`, `AQIndexFI` and `PollenIndex` all returned empty. The eight above are the complete set.

---

## Data availability
- **Is the data free?** Yes — no registration or account required; the user must accept the Creative Commons licence before using the open data interfaces
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution to the Finnish Meteorological Institute required.
- **Is the data downloadable?** Yes
- **Data formats:** **NetCDF only.** `format=grib2` and `format=grib1` return zero-byte responses — live-verified. This makes ENFUSER the only producer on the FMI download service with no GRIB path. Output is classic `NETCDF3_64BIT_OFFSET`, CF-1.6.
- **File size:** ~11.3 MB for all eight parameters over a 0.08° × 0.04° box at 3 timesteps. The full 2632 × 2019 domain at 40 steps across 8 parameters would be very large; server-side `bbox` subsetting is effectively mandatory.
- **Official download location:**
  - WFS stored query (lists available runs): `https://opendata.fmi.fi/wfs?service=WFS&version=2.0.0&request=getFeature&storedquery_id=fmi::forecast::enfuser::airquality::helsinki-metropolitan::grid&`
  - Binary download service: `https://opendata.fmi.fi/download` with `producer=enfuser_helsinki_metropolitan`
- **Delivery mechanism:** HTTP GET against the SmartMet-based binary download service. The WFS returns a `gml:fileReference` URL rather than embedding the grid; the download URL can be constructed directly without the WFS step. Request limits: 20,000 download requests/day, 10,000 view requests/day, 600 combined per 5 minutes.
- **Source code:** Openly available at https://github.com/johanssl/EnfuserMIT — unusual for an operational national-service AQ model and worth noting for anyone wanting to reproduce or port the system.

---

## Notes
- **Resolution is the headline.** At ~13 m this is by a wide margin the finest gridded forecast product in this catalog — roughly 60× finer than [SILAM Hires](./silam-hires.md) at 0.833 km and 75× finer than [uEMEP](../norway/uemep.md) at 1 km. It resolves individual streets, which is the point: the model exists to support personal exposure estimation and route planning (e.g. the Green Paths work at the University of Helsinki).
- **Health-exposure metrics are distinctive.** Lung-deposited surface area, particle number concentration and black carbon are health-relevant exposure measures rather than regulatory pollutants, and are not distributed by any other model in this catalog. LDSA in particular is rarely available as a gridded forecast field.
- **`LungDepositedSurfaceArea` units are wrong as written.** The NetCDF `units` attribute reads `um2/cm`, which is dimensionally inconsistent. LDSA is conventionally expressed in **µm²/cm³**; the exponent appears to have been dropped. Values (6.1–62.9 in the sample) are consistent with µm²/cm³. Treat the attribute as a typo, not a different quantity.
- **All fields are quantized on ingest.** Live-measured minimum steps between distinct values: PM10 and PM2.5 0.1 µg/m³, NO2 0.2 µg/m³, O3 0.3 µg/m³, black carbon 0.1 µg/m³, LDSA 0.2 µm²/cm³, AQ index 0.1. Particle number concentration is the most severely affected — sample values were exact integer multiples of 386.9 cm⁻³ (77 discrete levels spanning 386.9–29791.7), consistent with lossy quantization rather than model output precision. The same precision loss appears across FMI's SmartMet-delivered products, including the [NEMO Baltic feed](../../../ocean_models/regional/finland/nemo-baltic-fmi.md).
- **NetCDF metadata is incomplete.** Global attributes retain unfilled template placeholders (`title = <title>`, `source = <producer>`), and — unlike FMI's SILAM and NEMO NetCDF output — **no variable carries a `standard_name`**. Only `units` and the FMI parameter-ID suffix are present. CF-1.6 is declared but not fully honoured.
- **Not on AWS S3, despite the model page.** FMI's ENFUSER page states that "for most modelling areas the modelled AQ data provided by the system is also made available on Amazon S3 cloud storage." FMI's own AWS S3 page lists exactly three datasets — radar, SILAM, and gridded observations — and probes for plausible ENFUSER bucket names returned HTTP 404. Whichever S3 storage the model page refers to is not part of FMI's public open-data S3 offering. The WFS/download service is the only verified public route.
- **Turku is operational but not public.** FMI runs ENFUSER for Turku as well, including the LES-assisted high-resolution PM configuration, but explicitly states the Turku data is for the city's use and not publicly available. Probes for `enfuser_turku` and variants returned nothing. Out of catalog scope until a public feed appears.
- **International deployments.** ENFUSER has been designed for portability using global open-access inputs (notably OpenStreetMap) and has been applied in Nanjing and Langfang (China) and Delhi (India). None of these are publicly distributed.
- **Relationship to FMI's other composition products:** [SILAM Hires](./silam-hires.md) (0.833 km Finland/Baltic full chemistry), [SILAM Europe](./silam-europe.md) (0.1° Europe), [SILAM Global](../../global/finland/silam-global.md) (0.2°). ENFUSER is the urban downscaling layer beneath these, analogous to how [uEMEP](../norway/uemep.md) downscales EMEP MSC-W in Norway — though ENFUSER's assimilation-driven design differs substantially from uEMEP's local-fraction dispersion approach.
- **Development history.** FMI dates the system to 2011, beginning with an environmental information fusion module in the EU PESCaDO project, with subsequent development through CLEEN-MMEA, TEKES-INKA, TEKES-NAQT, CLIMOB, UIA-HOPE, Smart & Clean HAQT, CITYZER, RESPONSE, GIANT and the ACCC Flagship. No operational version history is published for the open-data feed (**TBD**).

---

## Official documentation
- FMI-ENFUSER modelling system: https://en.ilmatieteenlaitos.fi/environmental-information-fusion-service
- FMI local-scale air quality modelling: https://en.ilmatieteenlaitos.fi/local-air-quality-modeling
- FMI air quality forecasts: https://en.ilmatieteenlaitos.fi/air-quality-forecasts
- FMI Open Data — forecast models manual: https://en.ilmatieteenlaitos.fi/open-data-manual-forecast-models
- FMI Open Data — licence (CC BY 4.0): https://en.ilmatieteenlaitos.fi/open-data-licence
- FMI WFS GetCapabilities: https://opendata.fmi.fi/wfs?request=GetCapabilities
- ENFUSER source code: https://github.com/johanssl/EnfuserMIT
- HSY Helsinki metropolitan air quality map: https://www.hsy.fi/en/air-quality-and-climate/air-quality-in-the-helsinki-metropolitan-area/air-quality-map/

### Key references
- Johansson, L., Karppinen, A., Kurppa, M., Kousa, A., Niemi, J. V., Kukkonen, J. (2022). *An operational urban air quality model ENFUSER, based on dispersion modelling and data assimilation.* Environmental Modelling & Software, 156, 105460. https://doi.org/10.1016/j.envsoft.2022.105460
- Johansson, L., Epitropou, V., Karatzas, K., Karppinen, A., Wanner, L., Vrochidis, S., Bassoukos, A., Kukkonen, J., Kompatsiaris, I. (2015). *Fusion of meteorological and air quality data extracted from the web for personalized environmental information services.* Environmental Modelling & Software, 64, 143–155.
- Johansson, L., Jalkanen, J.-P., Kukkonen, J. (2017). *Global assessment of shipping emissions in 2015 on a high spatial and temporal resolution.* Atmospheric Environment, 167, 403–415. https://doi.org/10.1016/j.atmosenv.2017.08.042
- Kassandros, T., Bagkis, E., Johansson, L., Kontos, Y., Katsifarakis, K. L., Karppinen, A., Karatzas, K. (2023). *Machine learning-assisted dispersion modelling based on genetic algorithm-driven ensembles: An application for road dust in Helsinki.* Atmospheric Environment, 119818.
