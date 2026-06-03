# AIGFS (Artificial Intelligence Global Forecast System)

## What this model is
The Artificial Intelligence Global Forecast System (AIGFS) is NOAA's operational machine-learning-based global deterministic weather forecast model.

It is a fine-tuned productionization of Google DeepMind's GraphCast architecture (Lam et al., 2023), retrained by NOAA's Environmental Modeling Center (EMC) using NOAA's own Global Data Assimilation System (GDAS) analyses paired with ECMWF ERA5 reanalysis as training targets. Unlike traditional physics-based NWP, AIGFS does not solve the equations of fluid dynamics explicitly; instead, it predicts atmospheric evolution directly from learned patterns in historical weather data, producing 16-day forecasts in roughly 40 minutes on GPU hardware.

AIGFS is the operational descendant of NOAA's experimental EAGLE SOLO program (which itself was based on the experimental [GraphCastGFS](./graphcastgfs.md) system) and runs alongside the physics-based [GFS](./gfs.md) as a complement, not a replacement. It became operational at 12 UTC on December 17, 2025, alongside its ensemble counterparts [AIGEFS](../../../ensemble_models/global/usa/aigefs.md) and [HGEFS](../../../ensemble_models/global/usa/hgefs.md), as the first AI-based global weather forecast systems implemented by NOAA in operations.

The current operational version is AIGFS v1.0.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States
- **Development:** NOAA Environmental Modeling Center (EMC), in collaboration with the Earth Prediction Innovation Center (EPIC) under Project EAGLE (Experimental AI Global and Limited-area Ensemble forecast system)
- **Architecture origin:** Derived from Google DeepMind's GraphCast (Lam et al., 2023)

---

## What area it covers
- **Coverage:** Global
- **Grid:** Uniform latitude–longitude grid
- **Horizontal resolution:** 0.25° (~28 km)

---

## Basic details
- **Model type:** Global deterministic (AI-based)
- **Architecture:** Graph neural network (GNN), derived from GraphCast in an encode-process-decode configuration with ~37 million learned parameters
- **Training data:**
  - Pre-trained weights from DeepMind's GraphCast (originally trained on ERA5 reanalysis 1979–2017)
  - Fine-tuned by NOAA EMC using NCEP's GDAS analysis data as inputs, with ERA5 / HRES / GDAS as training targets (Tabas et al., 2025)
- **Input data:** Two model states (current time and 6 hours prior) from NCEP 0.25° GDAS analysis data; unit conversions applied to match GraphCast's expected inputs
- **Forecast timestep:** 6 hours (autoregressive)
- **Forecast length:** 384 hours (16 days)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Computational efficiency:** A complete 16-day global forecast runs in approximately 40 minutes on GPU hardware, requiring approximately 0.3% of the computing resources used by the operational physics-based [GFS](./gfs.md)
- **Initialization:** GDAS analysis (the same analysis stream that initializes operational GFS)

---

## Vertical structure and variables

### Pressure levels (13)
1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa

### Prognostic variables
**At pressure levels (6 variables × 13 levels):**
- Temperature
- Geopotential height
- U (zonal) component of wind
- V (meridional) component of wind
- Vertical velocity
- Specific humidity

**At surface (5 variables):**
- 2 m temperature
- 10 m U component of wind
- 10 m V component of wind
- Mean sea level pressure
- Total precipitation

This 13-pressure-level vertical structure is the canonical GraphCast operational configuration, identical to the one used by [GraphCastGFS](./graphcastgfs.md), ECCC's [GEML](../canada/gdps-geml.md), and ECMWF's [AIFS Single](../eu/aifs-single.md) v1 (which has since added a 14th level at 10 hPa in v2). Compared with the physics-based [GFS](./gfs.md) (which provides ~30 isobaric levels and a much wider parameter set), AIGFS is a deliberately stripped-down dataset focused on the variables the GraphCast architecture directly predicts.

---

## What it provides
Deterministic global forecasts of:
- Temperature (surface and upper-air pressure levels)
- Wind components (10 m and upper-air, including vertical velocity)
- Geopotential height
- Specific humidity
- Mean sea level pressure
- Total precipitation

Forecasts are output every 6 hours out to 16 days ahead.

AIGFS is intended primarily for **synoptic-scale and large-scale forecasting**, including improved tropical cyclone track guidance. AIGFS does not include cloud variables, radiation fluxes, soil moisture/temperature, ozone, wave forecasts, or the broader land/aviation/composition fields that GFS provides — users with downstream pipelines built around GFS's wider parameter set will need to either continue using GFS or merge AIGFS output with GFS for missing variables.

---

## Performance characteristics

### Strengths (per NOAA evaluation)
- **Synoptic-scale skill:** Significantly improved synoptic patterns compared to operational GFS at medium range
- **Tropical cyclone track:** Reduced track errors at longer forecast lead times — one of the headline results from the EAGLE program
- **Computational efficiency:** ~0.3% of GFS computing resources for an equivalent forecast, enabling far faster delivery of guidance to forecasters

### Known limitations
- **Tropical cyclone intensity:** AIGFS v1.0 shows degraded intensity forecasts compared to GFS — better track prediction is paired with less accurate wind-speed prediction. This trade-off is a primary reason NOAA operates AIGFS as a complement to GFS rather than a replacement.
- **Distribution tails:** Like other MSE-trained AI weather models, AIGFS exhibits some smoothing at longer lead times and under-prediction of extreme values.
- **Limited variable set:** As listed above, AIGFS does not produce many fields downstream pipelines have historically expected from operational global guidance.
- **Stratospheric coverage:** With the highest pressure level at 50 hPa, AIGFS has limited ability to represent stratospheric processes including sudden stratospheric warmings — comparable to AIFS Single v1 (before its v2 upgrade added a 10 hPa level) and GEML.

---

## Relationship to other models

### Lineage within NOAA's AI program
- **[GraphCastGFS](./graphcastgfs.md)** (NCEP, experimental) — earlier productionization that AIGFS evolved from. GraphCastGFS v2.0 introduced GDAS-based fine-tuning; AIGFS v1.0 inherits this approach with refinements.
- **EAGLE SOLO** (decommissioned operationally) — the experimental demonstration system that AIGFS replaced. EAGLE SOLO v1.0 ran from April 24, 2024 to December 18, 2025. The EAGLE SOLO AWS bucket continues to host **experimental AIGFSdev forecasts** for ongoing AIGFS development (see Notes below).
- **[AIGEFS](../../../ensemble_models/global/usa/aigefs.md)** (operational) — the 31-member AI-based ensemble counterpart to AIGFS, implemented on the same date.
- **[HGEFS](../../../ensemble_models/global/usa/hgefs.md)** (operational) — the hybrid "grand ensemble" combining 31 GEFS physics members with 31 AIGEFS AI members, also implemented on the same date.

### Architectural peers (GraphCast family)
AIGFS is part of a broader family of operational productionizations of the GraphCast architecture. All share the same ~37M-parameter GNN architecture and the 13-pressure-level vertical structure, but differ in training data, fine-tuning procedures, and operational role:
- **GraphCast** (Google DeepMind, 2023) — the original research architecture
- **[GraphCastGFS](./graphcastgfs.md)** (NCEP, experimental) — predecessor of AIGFS
- **[GEML](../canada/gdps-geml.md)** (ECCC, experimental) — Canadian productionization, fine-tuned on ERA5 + ECMWF HRES analyses; also serves as the spectral nudging target for ECCC's operational hybrid [GDPS](../canada/gem-global.md) (since v10.0.0, May 26, 2026)
- **AIGFS** (NOAA, operational; this entry)

### Architectural peers (different lineages)
- **[AIFS Single](../eu/aifs-single.md)** (ECMWF, operational) — encoder-processor-decoder architecture with attention-based GNN encoder/decoder and sliding-window transformer processor; trained with the Anemoi framework
- **[FourCastNetGFS](./fourcastnetgfs.md)** (NCEP, experimental) — productionization of NVIDIA's FourCastNet using Spherical Fourier Neural Operators

### Companion physics-based system
- **[GFS](./gfs.md)** (NOAA, operational) — the physics-based global model that AIGFS complements. AIGFS is initialized from the same GDAS analyses and runs at the same 4× daily cadence; the two systems are intended to be used together rather than as substitutes.

---

## Data assimilation
AIGFS does **not** perform its own data assimilation. It is initialized from analyses produced by NOAA's Global Data Assimilation System ([GDAS](./gfs.md#data-assimilation)), the same hybrid 4DEnVar analysis stream that initializes operational GFS. This means AIGFS forecasts inherit any analysis-quality differences from GDAS, and pre- and post-GDASv17 (proposed October 2026) AIGFS forecasts will be initialized from materially different analyses.

GraphCast-style models like AIGFS require **two consecutive analysis times** (T and T-6h) to produce a forecast — the architecture uses both as input to step the forecast forward in 6-hour increments. A single analysis time is not sufficient to initialize the model.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/aigfs/prod/ (real-time, ~10-day rolling)
  - **AWS Open Data:** Available alongside other NOAA AI weather products
  - **NSF Unidata IDD/CONDUIT:** Available via the Unidata feed for users with CONDUIT access; appears in IDV's default catalog as "Artificial Intelligence GFS - Global Coverage"
- **Single-cycle data volume:** Approximately 5.4 GB per run (uncompressed)
- **Latency:** GRIB files begin transferring 3–4 hours after the initial forecast time and take roughly 40 minutes to fully arrive
- **NCEP Product Inventory:** https://www.nco.ncep.noaa.gov/pmb/products/aigfs/

---

## Notes
- AIGFS is a **stripped-down dataset compared to GFS**. Users running WRF or other downstream models that depend on AIGFS for boundary conditions have reported that the limited variable set is insufficient on its own and have had to merge AIGFS data with GFS to fill gaps. Users planning to swap AIGFS in as a drop-in replacement for GFS in existing pipelines should verify their required variables are actually produced.
- The `forecast_13_levels` naming convention used in NOAA's AIGFS distribution reflects the GraphCast operational configuration (13 pressure levels), distinguishing it from earlier 37-level GraphCast experiments that NOAA decommissioned for storage and accuracy reasons.
- The pressure-level structure (1000 to 50 hPa, 13 levels) is the GraphCast-canonical set and is identical to the levels used by [GraphCastGFS](./graphcastgfs.md), ECCC's [GEML](../canada/gdps-geml.md), and AIFS Single v1. ECMWF's AIFS Single v2 (May 2026) extended this to 14 levels by adding 10 hPa for stratospheric coverage. AIGFS v1.0 retains the 13-level configuration; whether future AIGFS versions follow AIFS into the stratosphere has not been publicly stated.
- **Ongoing AIGFS development continues experimentally** in the EAGLE SOLO AWS bucket, versioned as `AIGFSdev{X.Y}`. The first such experiment, **AIGFSdev2.1** (started 19 December 2025), updated GraphCast's loss function from grid-point mean squared error to a **spectral harmonic-based mean squared error** designed to mitigate the "double penalty" problem common to MSE-trained data-driven forecasts (cf. arXiv:2501.19374). The dev forecasts are not operational AIGFS — they are research iterations being evaluated for incorporation into a future operational AIGFS upgrade.
- Due to the non-determinism of GPU computation, users running AIGFS or its underlying GraphCast weights themselves cannot exactly reproduce NOAA's official forecasts.
- AIGFS (along with AIGEFS and HGEFS) is part of the broader **Project EAGLE**, a joint effort between NOAA Research Laboratories, EPIC (within OAR), and the National Weather Service. EAGLE continues to develop AI-based regional, sub-seasonal, and seasonal forecast systems beyond the operational AIGFS / AIGEFS / HGEFS deterministic and ensemble suite.
- Implementation note: the December 17, 2025 operational launch was preceded by an evaluation window opened on December 9, 2025, during which NOAA distributed evaluation products via NOMADS so external users could compare AIGFS outputs against legacy baselines before cutover.

---

## Status
- AIGFS is **operational** as of 12 UTC on December 17, 2025.
- It runs alongside, not as a replacement for, the operational physics-based [GFS](./gfs.md).
- Future AIGFS upgrades are being staged through experimental AIGFSdev configurations on the EAGLE SOLO AWS bucket; no operational version successor has been announced as of May 2026.

---

## Official documentation
- NCEP AIGFS product inventory: https://www.nco.ncep.noaa.gov/pmb/products/aigfs/
- NOAA press release (December 2025): https://www.noaa.gov/news-release/noaa-deploys-new-generation-of-ai-driven-global-weather-models
- EPIC EAGLE AI overview: https://epic.noaa.gov/ai/eagle-overview/
- EPIC EAGLE AI changelog: https://epic.noaa.gov/ai/eagle-change-log/
- EAGLE program early-look post: https://epic.noaa.gov/noaa-project-eagle-to-accelerate-ai-weather-prediction-advances-for-the-united-states/
- EAGLE SOLO / AIGFSdev experimental data on AWS: https://registry.opendata.aws/noaa-nws-graphcastgfs-pds/
- NSF Unidata announcement: https://www.unidata.ucar.edu/news/ai-driven-global-model-output-availability

### Key references
- Tabas, S. S., J. Wang, W. Lei, M. Row, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *GFS-Powered Machine Learning Weather Prediction: A Comparative Study on Training GraphCast with NOAA's GDAS Data for Global Weather Forecasts.* NCEP Office Note 521, 33 pp. https://doi.org/10.25923/xd3y-wy31
- Wang, J., S. S. Tabas, B. Fu, L. Cui, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *Development of a Hybrid ML and Physical Model Global Ensemble System.* NCEP Office Note 522. https://doi.org/10.25923/7kpr-(suffix)
- Lam, R., et al. (2023). *Learning skillful medium-range global weather forecasting.* Science, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
