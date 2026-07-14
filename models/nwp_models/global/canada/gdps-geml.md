# GEML (Global Environmental eMuLator)

## What this model is
GEML (Global Environmental eMuLator) is Environment and Climate Change Canada's experimental AI-based global deterministic weather forecast model.

It is a graph neural network derived from Google DeepMind's GraphCast architecture (Lam et al., 2023), with weights retrained and refined by ECCC using ERA5 reanalysis (1979–2016) and ECMWF operational analyses (2016–2021). Like other standalone AI weather models, GEML does not solve the equations of fluid dynamics explicitly; instead, it predicts atmospheric evolution directly from learned patterns in historical weather data, producing forecasts orders of magnitude faster than conventional physics-based NWP.

GEML serves two distinct roles operationally. First, it is distributed as its own forecast product through the MSC datamart — a complete 10-day twice-daily global forecast in its own right. Second, it provides the reference large-scale temperature and wind fields that the operational [GDPS](./gem-global.md) (v10.0.0, operational since May 26, 2026) is spectrally nudged toward, making it the AI component of ECCC's hybrid physics-AI forecasting effort.

GEML is the AI component of the operational GDPS v10.0.0 (operational since May 26, 2026), and is also distributed as a standalone forecast product on the MSC datamart.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada
- **Architecture origin:** Derived from Google DeepMind's GraphCast (Lam et al., 2023)
- **Training and weights:** Retrained and refined by ECCC's ASTD-MRD division

---

## What area it covers
- **Coverage:** Global
- **Grid:** Uniform latitude-longitude grid with poles, 1440 × 721 grid points (global; 90°N–90°S latitude, 0°–359.75° longitude)
- **Horizontal resolution:** 0.25° (~28 km)

---

## Basic details
- **Model type:** Global deterministic (AI-based)
- **Architecture:** Graph neural network (GNN), derived from GraphCast
- **Training data:**
  - Pre-trained on ERA5 reanalysis (1979–2015), following the training configuration in Lam et al. (2023)
  - Fine-tuned on ECMWF HRES initial conditions dataset for calendar years 2016–2021 (Rasp et al., 2024)
- **Input data:** 3D analyses of atmospheric variables on a global 0.25° × 0.25°, 13-level grid; initialized from GDPS analysis
- **Forecast timestep:** 6 hours (autoregressive)
- **Forecast length:** 10 days (240 h)
- **Update frequency:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** 6-hourly (lead times 000–240 h; 41 steps per run)
- **Initialization:** GDPS analysis

---

## Vertical structure and variables

### Pressure levels (13)
50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa

### Prognostic variables
**At pressure levels:**
- Temperature
- Geopotential
- U (zonal) component of wind
- V (meridional) component of wind
- Vertical velocity
- Specific humidity

**At surface:**
- 2 m temperature
- 10 m U component of wind
- 10 m V component of wind
- Mean sea level pressure

---

## What it provides
GEML produces global deterministic forecasts of:
- Temperature (surface and upper-air pressure levels)
- Wind components (10 m and upper-air)
- Geopotential
- Specific humidity
- Vertical velocity
- Mean sea level pressure

Forecasts are output every 6 hours out to 10 days ahead.

The 13-level pressure grid is identical to the one used by NOAA's GraphCastGFS and AIGFS systems, reflecting the shared GraphCast lineage. This makes GEML, GraphCastGFS, and AIGFS broadly comparable in vertical resolution, though they differ in training data sources, fine-tuning procedures, and operational implementation.

---

## Relationship to other models

### GEML's two operational roles within ECCC
GEML is unusual in serving as both a standalone forecast product and an internal component of another operational system:

1. **Standalone forecast product:** Distributed at `/today/model_gdps-geml/25km/` on the MSC datamart as a complete 10-day forecast for direct user consumption.
2. **AI component of the operational hybrid GDPS:** GEML's large-scale temperature and wind fields are used as the nudging target for the operational [GDPS](./gem-global.md) (since v10.0.0, May 26, 2026), which spectrally nudges its physics-based GEM forecast toward GEML at large scales (>2750 km) in the mid-troposphere (250–850 hPa).

This dual role distinguishes GEML from other operational AI models like AIGFS or AIFS Single, which serve only as standalone products without being directly used as components of physics-based hybrid systems.

### Architectural lineage
GEML is part of a broader family of operational productionizations of the GraphCast architecture:
- **GraphCast** (Google DeepMind, 2023) — the original research architecture, pre-trained on ERA5
- **GraphCastGFS** (NOAA, experimental) — NCEP's productionization, fine-tuned on GDAS+ERA5
- **[AIGFS](./aigfs.md)** (NOAA, operational) — operational descendant of GraphCastGFS, replaced EAGLE SOLO in December 2025
- **GEML** (ECCC, this entry) — Canadian productionization, fine-tuned on ERA5 + ECMWF HRES analyses

The four systems share the same underlying GNN architecture and 13-pressure-level vertical structure but differ in training data, fine-tuning procedures, and operational status.

### Architectural peers (different lineages)
- **[AIFS Single](../eu/aifs-single.md)** (ECMWF) — operational AI deterministic, encoder-processor-decoder architecture with attention-based GNN encoder/decoder and sliding-window transformer processor
- **FourCastNetGFS** (NOAA, experimental) — NCEP's productionization of NVIDIA's FourCastNet using Spherical Fourier Neural Operators

### Companion ECCC systems
- **[Operational GDPS](./gem-global.md):** ECCC's stable physics-based global model. Provides the analysis used to initialize GEML.

---

## Data availability
- **Is the data free?** Yes
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (simple packing; ~1.98 MB per field, ~162 MB per step, ~6.5 GB per run)
- **Official download location:**  
  https://dd.weather.gc.ca/today/model_gdps-geml/25km/
  - **Path template:** `https://dd.weather.gc.ca/today/model_gdps-geml/25km/{HH}/{hhh}/` — `{HH}` = run (`00`/`12`), `{hhh}` = 3-digit lead time (`000`–`240`, every 6 h)
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_GDPS-GEML_{Var}_{LevelType}-{Level}_LatLon0.25_PT{hhh}H.grib2` (e.g. `20260714T00Z_MSC_GDPS-GEML_AirTemp_IsbL-0500_LatLon0.25_PT024H.grib2`)
- **Documentation:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-geml-datamart_en/
- **HuggingFace repository (model weights and code):**  
  https://huggingface.co/ECCC-ASTD-MRD/geml

The HuggingFace distribution makes GEML one of the few operational AI weather models whose trained weights are publicly available — a deliberate ECCC choice that contrasts with the more closed weight-distribution practices of some other operational AI systems.

---

## Status
- GEML's large-scale fields became an operational input on May 26, 2026, when GDPS v10.0.0 — which spectrally nudges toward GEML — replaced v9.1.0 as ECCC's operational global deterministic system.
- GEML is also distributed as a standalone forecast product through the MSC datamart.
- ECCC's April 2026 supercomputer upgrade to H100 GPU infrastructure is the enabling hardware for routine operational AI inference; GEML inference is GPU-accelerated and was not feasible within ECCC's pre-2026 operational compute envelope.

---

## Notes
- GEML's 0.25° resolution and 13-level pressure grid match NOAA's GraphCastGFS and AIGFS, reflecting the shared GraphCast architecture. Users comparing the three should be aware that despite this structural similarity, the systems differ in training data (ERA5+HRES for GEML, ERA5+GDAS for the NOAA systems), fine-tuning procedures, and operational status.
- The `/25km/` directory naming on the MSC datamart refers to the approximate metric equivalent of 0.25° resolution at the equator. The actual grid spacing varies with latitude on a regular lat-lon grid, narrowing significantly toward the poles.
- Like other GraphCast-derived models, GEML uses two model states (current time and 6 hours prior) as input to step the forecast forward in 6-hour increments. This means GEML cannot be initialized from a single analysis time — it requires the GDPS analysis at both T and T-6h.
- The 4 surface variables (2 m temperature, 10 m U/V wind, MSLP) and 6 upper-air variables (temperature, geopotential, U/V wind, vertical velocity, specific humidity) constitute the total GEML output set. Variables not in this list — including precipitation, cloud, radiation, and humidity diagnostics commonly available from physics-based NWP — are not produced by GEML directly. File-verified against the 00Z run: 6 upper-air fields × 13 levels + 4 surface fields = 82 GRIB2 files per lead-time step.
- **Field conventions (file-verified):** The upper-air geopotential field is distributed as true geopotential Φ (GRIB shortName `z`, units m² s⁻²), *not* geopotential height — divide by g ≈ 9.80665 m s⁻² for height in metres. Vertical velocity is pressure velocity omega (`w`, Pa s⁻¹). Files use GRIB2 simple packing (unlike the JPEG2000 packing in the operational GDPS GRIB2) and carry originating centre `cwao`.
- ECCC's choice to publicly release GEML's weights via HuggingFace (`ECCC-ASTD-MRD/geml`) is operationally meaningful: it allows external researchers and forecast services to reproduce GEML inference, fine-tune the model further, or build downstream products. This contrasts with operational AI weather systems where weights remain proprietary.
- The dual role (standalone product + nudging input for the experimental GDPS) is methodologically distinctive. Most operational AI weather systems are run either as full replacements for physics-based forecasts (AIFS Single alongside IFS, AIGFS as operational complement to GFS) or as research/development products without operational integration. GEML occupies a more closely-coupled position within ECCC's broader hybrid forecasting architecture.

---

## Official documentation
- GEML datamart README:  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-geml-datamart_en/
- Experimental GDPS (v9.0.9) technical specifications (Environment and Climate Change Canada, June 2025) — describes GEML's role as the spectral nudging target
- Experimental GDPS (v9.0.9) fact sheet (Environment and Climate Change Canada, June 2025)
- HuggingFace model repository: https://huggingface.co/ECCC-ASTD-MRD/geml

### Key references
- Lam, R., et al. (2023). Learning skillful medium-range global weather forecasting. *Science*, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
- Rasp, S., et al. (2024). WeatherBench 2: A benchmark for the next generation of data-driven global weather models. *J. Adv. Model. Earth Syst.*, 16(6), e2023MS004019. https://doi.org/10.1029/2023MS004019
- Husain, S.Z., et al. (2024). Leveraging data-driven weather models for improving numerical weather prediction skill through large-scale spectral nudging. *arXiv*, arXiv:2407.06100. https://doi.org/10.48550/arXiv.2407.06100
