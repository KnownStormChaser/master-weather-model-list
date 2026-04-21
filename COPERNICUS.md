# Copernicus Marine and Copernicus Atmosphere Products

This page indexes all models in this repository that are distributed through the **Copernicus programme** — either the Copernicus Marine Service (marine products) or the Copernicus Atmosphere Monitoring Service (atmospheric composition).

Copernicus is the European Union's Earth observation programme, coordinated by the European Commission with implementation by Mercator Ocean International (marine) and ECMWF (atmosphere). Products are distributed under standard Copernicus licences with free access after registration.

This index exists because Copernicus products are operated by many different national institutes across Europe — the country-and-type directory structure of this repository reflects each product's **operator**, which is often not the country most users would intuitively associate with the product's **coverage area**. For example, the Black Sea wave product is operated by a German institute; the Arctic wave product is operated by Norway; the Iberian-Biscay-Irish wave product is operated by Spain but covers UK and Irish waters. This index makes the full Copernicus portfolio discoverable as a coherent whole.

---

## How Copernicus operational products work

Copernicus marine and atmospheric products are operated through a distributed network of **Production Units** (PUs) — national research institutes and meteorological services across Europe that each operate one or more operational systems on behalf of the programme. Products are delivered through centralized data portals (the Marine Data Store for marine products, the Atmosphere Data Store for atmospheric products), but the actual forecast production happens at the Production Unit.

Each product has:
- A **product identifier** (e.g., `BLKSEA_ANALYSISFORECAST_WAV_007_003`) that remains stable across version upgrades
- A **Production Unit** that runs the operational system
- A **Monitoring and Forecasting Centre (MFC)** or **Thematic Assembly Centre (TAC)** that coordinates the product family
- A **DOI** for citation

Products may change operator across their lifetime (the NWS wave product is a notable example — see that entry). The product identifier persists even when the underlying system is replaced.

---

## Copernicus Marine wave products

All six regional wave systems and the global wave system below are distributed through the Copernicus Marine Service (CMEMS). Together they provide near-complete coverage of European regional seas and adjacent waters.

### Global

#### [MFWAM Global (Copernicus)](./models/wave_models/global/france/mfwam-copernicus.md)
- **Production Unit:** Météo-France
- **Model core:** MFWAM (third-generation WAM derivative)
- **Resolution:** 1/12° (~8 km)
- **Forecast length:** 10 days, twice daily
- **Data assimilation:** Multi-satellite altimeter SWH + Sentinel-1 SAR wave spectra
- **Coupling:** None (ECMWF wind forcing only)
- **Product ID:** `GLOBAL_ANALYSISFORECAST_WAV_001_027`

### Regional

#### [BALWAM — Baltic Sea](./models/wave_models/regional/finland/balwam.md)
- **Production Unit:** Finnish Meteorological Institute (FMI)
- **Country:** Finland
- **Model core:** WAM Cycle 4.7
- **Resolution:** 1 nautical mile (~1.85 km)
- **Forecast length:** 9 days, twice daily
- **Data assimilation:** None
- **Coupling:** One-way from Baltic physics (hourly currents, sea level, ice)
- **Product ID:** `BALTICSEA_ANALYSISFORECAST_WAV_003_010`

#### [MEDWAM — Mediterranean Sea](./models/wave_models/regional/greece/medwam.md)
- **Production Unit:** Hellenic Centre for Marine Research (HCMR)
- **Country:** Greece
- **Model core:** WAM Cycle 6
- **Resolution:** 1/24° (~4.6 km)
- **Forecast length:** 10 days, twice daily
- **Data assimilation:** Multi-satellite altimeter SWH + U10 wind (OI)
- **Coupling:** One-way from Mediterranean physics (hourly currents, sea level)
- **Product ID:** `MEDSEA_ANALYSISFORECAST_WAV_006_017`

#### [IBIWAM — Iberian-Biscay-Irish waters](./models/wave_models/regional/spain/ibiwam.md)
- **Production Unit:** Nologin / CESGA
- **Country:** Spain
- **Model core:** MFWAM (ECWAM-IFS-47R1)
- **Resolution:** 1/36° (~2.7 km)
- **Forecast length:** 5–10 days, twice daily
- **Data assimilation:** Multi-satellite altimeter SWH + CFOSAT/Sentinel-1 spectra (OI)
- **Coupling:** Two-way with IBI ocean physics
- **Product ID:** `IBI_ANALYSISFORECAST_WAV_005_005`

#### [AMM15-WW3 — North-West European Shelf](./models/wave_models/regional/uk/amm15-ww3-uk.md)
- **Production Unit:** UK Met Office (since November 2025)
- **Country:** United Kingdom
- **Model core:** WAVEWATCH III v7.1 coupled to NEMO v3.6
- **Resolution:** 3–1.5 km SMC grid (delivered on 1/33° × 1/74°)
- **Forecast length:** 7 days, once daily
- **Data assimilation:** None (in current system)
- **Coupling:** Two-way online with NEMO AMM15 via OASIS3-MCT
- **Product ID:** `NWSHELF_ANALYSISFORECAST_WAV_004_014`
- **Note:** Between September 2023 and November 2025, this product was operationally an extraction of IBIWAM. See the entry for the full lineage.

#### [ARCWAM — Arctic Ocean](./models/wave_models/regional/norway/arcwam.md)
- **Production Unit:** MET Norway (ARC MFC)
- **Country:** Norway
- **Model core:** WAM Cycle 4.7 (with wave-under-ice modifications)
- **Resolution:** 3 km polar stereographic
- **Forecast length:** Alternating 5/10 days, twice daily
- **Data assimilation:** Multi-satellite altimeter SWH + U10 wind (OI) — added Nov 2024
- **Coupling:** One-way from ARC physics (sea ice, tidal currents)
- **Product ID:** `ARCTIC_ANALYSIS_FORECAST_WAV_002_014`

#### [BSeas7 — Black Sea and Sea of Azov](./models/wave_models/regional/germany/bseas7-blk.md)
- **Production Unit:** Helmholtz-Zentrum Hereon
- **Country:** Germany
- **Model core:** WAM Cycle 6
- **Resolution:** 1/40° (~2.5 km)
- **Forecast length:** 10 days, twice daily
- **Data assimilation:** Altimeter SWH + wind speed (OI)
- **Coupling:** One-way from Black Sea physics (SSH, surface currents)
- **Product ID:** `BLKSEA_ANALYSISFORECAST_WAV_007_003`

### Observations at a glance

Looking across this portfolio surfaces a few patterns worth knowing:

- **Model core diversity:** Five of the seven products use the WAM family (WAM Cycle 4.7, WAM Cycle 6, or MFWAM); one uses WAVEWATCH III (AMM15-WW3); the IBI and global products use MFWAM specifically. The Copernicus wave portfolio is mostly but not exclusively WAM-based.
- **Data assimilation coverage:** Five of the seven regional products have some form of altimetric DA. The exceptions are BALWAM (closed basin, limited altimeter tracks) and AMM15-WW3 (current Met Office system doesn't run DA).
- **Coupling depth:** Only IBIWAM and AMM15-WW3 have genuine two-way ocean-wave coupling. The others use one-way forcing from companion physics products.
- **Operator ≠ coverage:** BSeas7 (Black Sea) is German-operated; ARCWAM (Arctic) is Norwegian; MEDWAM (Mediterranean) is Greek; AMM15-WW3 (NW European Shelf) is British. The pattern is "whichever national institute invested in the relevant expertise," not geographical proximity.

---

## Copernicus Atmosphere Monitoring Service (CAMS) products

CAMS is the atmospheric composition branch of Copernicus, coordinated by ECMWF. Products are distributed through the Atmosphere Data Store (ADS) rather than the Marine Data Store.

### Global

#### [CAMS Global](./models/air_quality_models/global/eu/cams-global.md)
- **Production Unit:** ECMWF (on behalf of the EU)
- **Model core:** IFS (Integrated Forecasting System) with atmospheric composition modules
- **Resolution:** ~40 km (TL511), distributed at 0.4°
- **Forecast length:** 5 days, twice daily
- **Data assimilation:** 4D-Var with satellite composition retrievals (AOD, O3, CO, NO2, SO2)
- **Coupling:** Chemistry online-coupled within IFS
- **Product note:** Not a product identifier in the CMEMS sense — distributed through the ADS

### Regional

#### [CAMS Regional — European air quality ensemble](./models/air_quality_models/regional/eu/cams-regional.md)
- **Coordinating Unit:** ECMWF
- **Production:** 11 independent European models, each run by its own national institute
- **Model cores:** CHIMERE (INERIS), DEHM (Aarhus), EMEP (MET Norway), EURAD-IM (Jülich), GEM-AQ (IEP-NRI), LOTOS-EUROS (KNMI/TNO), MATCH (SMHI), MINNI (ENEA), MOCAGE (Météo-France), MONARCH (BSC), SILAM (FMI)
- **Resolution:** ~10 km ENSEMBLE grid (0.1°)
- **Forecast length:** 4 days (96 h), once daily
- **Data assimilation:** Each model performs independent surface-observation DA
- **Ensemble design:** Multi-model median, not perturbation-based
- **Countries:** France, Denmark, Norway, Germany, Poland, Netherlands, Sweden, Italy, Spain, Finland

Note that CAMS Regional is structurally very different from the other Copernicus products in this index: it's an ensemble of eleven independent models rather than a single system, with each contributing model being an operational product in its own right at its home institute.

---

## Finding Copernicus products elsewhere in the repository

Copernicus products also appear in topic-based indexes where relevant:

- [`AI_MODELS.md`](./AI_MODELS.md) — indexes the AI-related models (note: none of the Copernicus products in this index are currently AI-based, though ECMWF's AIFS line is adjacent to CAMS Global architecturally)
- [`STATUS.md`](./STATUS.md) — tracks upcoming implementations and retirements

---

## What this index does not cover

- **Copernicus reanalysis products** (CAMS EAC4, ERA5, etc.) — this repository focuses on operational near-real-time forecasts, not historical reanalyses
- **Copernicus Land, Climate, Emergency, or Security services** — these are outside the repository's NWP/atmospheric/wave scope
- **Products available only through the legacy CMEMS Web API** — all products listed here are available through the current Marine Data Store or Atmosphere Data Store
- **Copernicus Sentinel satellite data** — this is observational data, not model output

---

## Contributing

If a Copernicus product is added to the repository, please also add it to this index with a short summary matching the format above. The operator-country pattern is particularly worth noting in the entry — it's exactly the kind of non-obvious fact that makes this index useful.
