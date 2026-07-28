# SEAS5 (ECMWF Seasonal Forecasting System 5)

## What this model is
SEAS5 is ECMWF's operational seasonal forecasting system — a coupled atmosphere–ocean–land–sea-ice configuration of the Integrated Forecasting System (IFS) producing 51-member seasonal predictions to seven months, plus an annual configuration extending to 13 months. Operational since 5 November 2017, it is ECMWF's own long-range system and simultaneously the core ECMWF contribution to the C3S seasonal multi-system. Its most prominent products are ENSO predictions (the Niño-3.4 plumes).

Note that SEAS5 is an *operational seasonal prediction* system, not a climate-projection model.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Country / region:** Intergovernmental (ECMWF Member and Co-operating States); global product
- **Coordinating body / programme:** Standalone ECMWF system. Also contributes to the C3S seasonal multi-system (CDS `system=51`); a significant share of SEAS5 production cost, notably the reforecasts, is funded by C3S.

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Native atmospheric resolution ~36 km (TCO319). The C3S-distributed subset is regridded to 1° × 1° (grid points centred at half-degree latitude/longitude).

---

## Basic details
- **Model type:** Long-range coupled forecast system — ensemble (deterministic ensemble mean plus probabilistic products); single model
- **Model system / core:** ECMWF IFS Cycle 43r1, coupled to NEMO ocean and LIM2 sea ice
- **Range class:** Seasonal to interannual
- **Forecast length:** 7 months (215 days) monthly; extended to 13 months in the annual configuration, run every three months
- **Initialization cadence:** Monthly, 1st of the month (annual/13-month runs quarterly)
- **Ensemble generation:** Burst ensemble from a single start date, using SST and atmospheric perturbations plus stochastic physics
- **Ensemble size:** 51 members (real-time); 25 members (hindcast)
- **Temporal output resolution:** Sub-daily (6-hourly) and daily fields; monthly means
- **Output aggregation levels:** Raw ensemble fields; monthly and seasonal (3-month) means; anomalies relative to the hindcast climatology; tercile probabilities

---

## Coupled configuration
- **Atmosphere:** IFS Cy43r1, TCO319 cubic octahedral grid (dynamics), O320 Gaussian grid for physics (~36 km); 91 vertical levels to 0.01 hPa (~80 km)
- **Ocean:** NEMO v3.4.1 on the ORCA 0.25° grid; 75 vertical levels
- **Sea ice:** LIM2
- **Land surface:** HTESSEL
- **Coupling notes:** Fully coupled Earth-system configuration sharing modelling and initialization methods with ECMWF's medium- and sub-seasonal-range ensembles; the seasonal system is upgraded far less frequently (roughly every four to six years).

---

## Initialization
- **Atmosphere IC:** ECMWF operational analysis
- **Ocean / sea-ice IC:** OCEAN5 (the operational ocean and sea-ice analysis system built on the ORAS5 reanalysis)
- **Land IC:** Land-surface initialization from ECMWF analyses (upgraded relative to System 4)
- **Perturbation method:** SST perturbations, atmospheric initial-condition perturbations, and stochastic physics (SPPT/SKEB), consistent with ECMWF ensemble practice

---

## Hindcasts (reforecasts)
- **Hindcast period:** 1981–2016 (36 years) — extended from the 1981–2010 period used for System 4.
- **Hindcast ensemble size:** 25 members per start date.
- **Reference climatology period:** The 1981–2016 reforecast set defines the model climatology used for anomaly and probability products.
- **Distributed alongside forecasts?** Yes — the reforecast dataset is publicly distributed through the C3S Climate Data Store alongside the 1° real-time forecasts. In the CDS the hindcast `system` value must match the real-time forecast's (`system=51`).

---

## Sources of predictability
ENSO is the dominant driver, and SEAS5 delivered substantial improvements in tropical prediction — particularly equatorial Pacific sea-surface temperature — over System 4. Additional predictability comes from ocean heat content, land-surface and snow memory, sea ice, and the stratospheric state.

---

## What it provides
- 51-member ensemble forecasts of atmospheric, ocean, land, and sea-ice fields
- Ensemble-mean and probabilistic products (anomalies, tercile probabilities)
- ENSO/Niño-region SST plumes (seasonal and annual/13-month)
- Reforecast (hindcast) fields for calibration
- Formats: GRIB (native and via CDS); ocean variables in netCDF via the CDS `seasonal-monthly-ocean` dataset

Viewer-only siblings (out of scope): the ECMWF seasonal charts and Niño plume graphics on `charts.ecmwf.int`.

---

## Data availability
- **Is the data free?** Yes — openly licensed. Access to the full-resolution real-time stream, however, is service-gated (see below).
- **License:** CC-BY-4.0, plus the ECMWF Terms of Use. Since 1 October 2025 the entire ECMWF Real-time Catalogue is CC-BY-4.0 at maximum resolution with no information cost, permitting redistribution and commercial use with attribution. The C3S-distributed subset additionally carries the Copernicus licence terms.
- **Is the data downloadable?** Yes — via the C3S CDS. Not currently via ECMWF's own open-data portal (see access notes).
- **Data formats:** GRIB (primary); netCDF for CDS ocean variables
- **Official download location:**  
  - **C3S Climate Data Store (practical open route, 1° subset):** https://cds.climate.copernicus.eu/datasets/seasonal-original-single-levels — select ECMWF with `system=51`. Requires a free CDS account and per-dataset licence acceptance; retrieval via web form or the `cdsapi` client.
  - **ECMWF Open Data portal:** https://data.ecmwf.int/forecasts/ — **seasonal products are listed but marked "Not yet available"** (only a sea-surface temperature field, 1–6 months, is catalogued as planned). The portal currently serves IFS/AIFS medium-range and AIFS data only.
  - **Full-resolution real-time stream:** via the Product Requirements Catalogue (Set V-i-a), subject to a Real-time Dissemination Service Agreement and possible service charges.
- **Access route notes:** ECMWF's own release is 12 UTC on the 5th of each month (brought forward from the 8th); the C3S release of the 1° dataset is 12 UTC on the 6th. There is no anonymous bulk FTP or directory listing for SEAS5 — the CDS API is the open programmatic route. All SEAS5 data served through the CDS is held in ECMWF's MARS archive in GRIB.

---

## Notes
- **Open licence, gated delivery.** This is the key nuance: SEAS5 is *openly licensed* (CC-BY-4.0) but the full-resolution real-time stream is *not openly delivered* — it needs a dissemination service agreement. The openly accessible artifact is the 1° subset via the CDS. Worth recording explicitly, since "freely accessible" and "openly licensed" diverge here in the same way flagged for CPTEC/INPE.
- **Relationship to the C3S entry.** SEAS5 appears inside the C3S seasonal multi-system as `system=51`. This entry documents the ECMWF system in its own right — including the 13-month annual configuration and the native ~36 km resolution, neither of which is part of the C3S 1° multi-system distribution. Cross-reference rather than duplicate.
- **`system=5` is deprecated.** From November 2022 only `system=51` real-time data is served via the CDS; the older `system=5` hindcasts use a different 1° interpolation and grid definition and should not be mixed with `system=51` forecasts.
- **SEAS6 is upcoming, not yet operational.** ECMWF's next seasonal system (SEAS6) has been in preparation for several years, tied to IFS Cycle 49r2/50r1 development alongside ERA6 and initialized from the ORAS6 ocean reanalysis, and is expected to move to twice-monthly production. Implementation has slipped from earlier targets (early 2025, then Q1 2026). **This entry should be revisited on the SEAS6 switch** — resolution, hindcast period, ensemble configuration, and release schedule are all expected to change.
- **Sub-seasonal sibling.** ECMWF's extended-range ENS (day 15–46, "EC46") is a separate system and a separate entry candidate; it shares the open-data licensing situation described above.
- **Related ECMWF entries** (wire relative links once placement is set): IFS (medium-range), C3S Seasonal Multi-System.

---

## Recent version history
- **5 November 2017:** SEAS5 became operational, replacing System 4 — upgrades to the ocean model, atmospheric resolution, and land-surface initialization; reforecast period extended from 1981–2010 to 1981–2016; release date moved from the 8th to the 5th of the month.
- **November 2022:** C3S-distributed version re-encoded as `system=51` (new 1° interpolation and grid, plus added variables) following ECMWF's migration to the Bologna data centre.
- **Upcoming — SEAS6:** in development; not yet operational as of this writing.

---

## Official documentation
- ECMWF seasonal forecasts overview: https://www.ecmwf.int/en/forecasts/documentation-and-support/seasonal
- SEAS5 user guide: https://www.ecmwf.int/en/elibrary/81237-seas5-user-guide
- IFS Cycle 43r1 (the cycle SEAS5 uses): https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+43r1
- C3S contribution description (SEAS5-v20171101): https://confluence.ecmwf.int/display/CKB/Description+of+SEAS5-v20171101+C3S+contribution
- ECMWF Open Data (licence and available subset): https://www.ecmwf.int/en/forecasts/datasets/open-data
- Open data transition announcement (1 October 2025): https://www.ecmwf.int/en/about/media-centre/news/2025/ecmwf-achieve-fully-open-data-status-2025
- Johnson et al. (2019), *SEAS5: the new ECMWF seasonal forecast system*, GMD: https://doi.org/10.5194/gmd-12-1087-2019
- ECMWF Newsletter 154 — SEAS5 introduction: https://www.ecmwf.int/en/newsletter/154/meteorology/ecmwfs-new-long-range-forecasting-system-seas5
