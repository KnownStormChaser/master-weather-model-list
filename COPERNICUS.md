# Copernicus Products

This page indexes all models in this repository that are distributed through the **Copernicus programme**, across four of its services: the Copernicus Marine Service (marine products), the Copernicus Atmosphere Monitoring Service (atmospheric composition), the Copernicus Climate Change Service (long-range prediction), and the Copernicus Emergency Management Service (flood and fire danger forecasting).

Copernicus is the European Union's Earth observation programme, coordinated by the European Commission with implementation by Mercator Ocean International (marine) and ECMWF (atmosphere, climate, and the computational side of emergency management). Products are distributed under standard Copernicus licences with free access after self-registration.

### Four data stores, not one

The single most practical thing to know about the Copernicus portfolio is that it is not served from one place. Each service has its own store, its own account, and its own per-dataset licence acceptance:

| Service | Store | Products in this repository |
|---|---|---|
| Marine (CMEMS) | Marine Data Store (MDS) | 7 wave systems, 1 ocean physics system |
| Atmosphere (CAMS) | Atmosphere Data Store (ADS) | 2 global, 1 regional ensemble |
| Climate Change (C3S) | Climate Data Store (CDS) | 3 seasonal systems |
| Emergency Management (CEMS) | Early Warning Data Store (EWDS) | 3 flood systems, 1 fire danger system |

The licences differ too — CMEMS and Copernicus general licences for marine and atmosphere, the **CEMS-FLOODS datasets licence** for the flood products, and plain **CC-BY-4.0** for the fire danger product. Only the last carries an SPDX identifier. See each product's entry for the exact terms.

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

## Copernicus Marine ocean physics products

The Copernicus Marine ocean physics products provide global and regional 3D ocean state forecasts — temperature, salinity, currents, sea level, mixed layer depth, and sea ice fields. These are distinct from the wave products (which forecast surface wave fields) and from the biogeochemistry products (which forecast nutrients, carbon, plankton, and oxygen).

### Global

#### [GLO12 (Mercator Global Ocean Physical Analysis and Forecast)](./models/ocean_models/global/france/glo12.md)
- **Production Unit:** Mercator Ocean International
- **Country:** France
- **Model core:** NEMO v3.6
- **Sea ice model:** LIM3 (11-category, EVP rheology)
- **Resolution:** 1/12° (~8 km), 50 vertical z-levels
- **Forecast length:** 10 days, daily updates
- **Data assimilation:** SAM2 (SEEK Kernel) 4D + IAU + 3D-Var bias correction; assimilates SST, SLA, sea ice concentration, and in-situ T/S profiles
- **Coupling:** Online to LIM3 sea ice; one-way wave forcing from MFWAM (added in v2.3, March 2026); one-way ECMWF IFS atmospheric forcing
- **Distinctive feature:** Two-tier analysis (NRT-analysis upgraded to best-analysis after ~2-3 weeks); SMOC derived product combining circulation, tides, and Stokes drift
- **Product ID:** `GLOBAL_ANALYSISFORECAST_PHY_001_024`

### Observations at a glance

GLO12 is currently the only ocean physics product in this index. Worth noting:

- **Peer system outside Copernicus:** GLO12's direct operational peer is NOAA's [Global RTOFS](./models/ocean_models/global/us/rtofs-global.md), which uses HYCOM and CICE v4 instead of NEMO and LIM3. Together GLO12 and RTOFS form the two dominant operational global ocean physics forecasts worldwide. Both run at 1/12° resolution.
- **Regional ocean physics products exist** within Copernicus Marine — companion products to the wave systems already documented (Mediterranean, Black Sea, Baltic, Arctic, IBI, NWS), each operated by the same Production Unit as the corresponding wave product. These are not yet in this repository but would be natural additions if ocean physics coverage is expanded beyond global.
- **Reanalysis counterpart:** GLORYS12 is GLO12's matching reanalysis covering 1993–present. Not in this repository (operational forecasts only) but worth knowing about for users needing homogeneous long-term datasets.
- **AI counterpart:** GLONET is Mercator's neural ocean forecast trained on GLORYS12 data; currently a research/development product, not yet a fully operational system.

---

## Copernicus Atmosphere Monitoring Service (CAMS) products

CAMS is the atmospheric composition branch of Copernicus, coordinated by ECMWF. Products are distributed through the Atmosphere Data Store (ADS) rather than the Marine Data Store.

CAMS runs global atmospheric composition production in **two separate operational modes**: one for reactive gases and aerosols (CAMS Global), and one for long-lived greenhouse gases (CAMS Global GHG Forecasts). Both share the IFS model base but are distinct production systems with different data assimilation configurations, resolutions, and delivery schedules. A third regional production (CAMS Regional) covers European air quality via an 11-model ensemble.

### Global

#### [CAMS Global](./models/air_quality_models/global/eu/cams-global.md)
- **Production Unit:** ECMWF (on behalf of the EU)
- **Model core:** IFS (Integrated Forecasting System) with atmospheric composition modules
- **Resolution:** ~40 km (TL511), distributed at 0.4°
- **Forecast length:** 5 days, twice daily
- **Data assimilation:** 4D-Var with satellite composition retrievals (AOD, O3, CO, NO2, SO2)
- **Coupling:** Chemistry online-coupled within IFS
- **Product note:** Not a product identifier in the CMEMS sense — distributed through the ADS

#### [CAMS Global Greenhouse Gas Forecasts](./models/air_quality_models/global/eu/cams-ghg-global.md)
- **Production Unit:** ECMWF (on behalf of the EU)
- **Model core:** IFS (Integrated Forecasting System) with greenhouse gas modules
- **Resolution:** ~9 km forecast (~0.10°) / ~25 km analysis (~0.25°)
- **Forecast length:** 5 days, once daily
- **Data assimilation:** 4D-Var with GOSAT/TANSO and METOP-C/IASI satellite column retrievals (CO₂ and CH₄)
- **Coupling:** GHG tracers online-coupled within IFS; biogenic CO₂ fluxes coupled via ECLand/CHTESSEL
- **Tracers:** CO₂ and CH₄ (long-lived greenhouse gases)
- **Distribution:** Atmosphere Data Store (ADS), dataset `cams-global-greenhouse-gas-forecasts`
- **Note:** Separate operational production from CAMS Global. The two systems share the IFS base but assimilate different observations and are distributed as distinct products. The GHG forecast is initialized from a 4-day forecast of the analysis experiment rather than the analysis directly, due to the 2–4 day latency of satellite column retrievals.

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

### Observations at a glance

The CAMS product portfolio in this index surfaces a few patterns worth knowing:

- **Shared IFS base, separate production systems.** CAMS Global and CAMS Global GHG Forecasts both run on ECMWF's IFS but are operationally separate — different observations assimilated, different grids, different delivery cadences. They are not different variables of a single forecast.
- **Regional is structurally different.** CAMS Regional is a median ensemble of 11 independent national models rather than a single forecast system. Its architecture doesn't parallel the global products.
- **Reanalysis and inversion products are separate families.** The operational forecasts documented here have reanalysis and flux inversion counterparts (CAMS EGG4, CAMS inversion-optimised fluxes) that are outside this repository's operational-forecast scope but are part of the same broader CAMS product family.
- **N₂O is not in the operational forecast.** The CAMS Global GHG Forecast covers only CO₂ and CH₄. Nitrous oxide (N₂O) is produced as part of the CAMS global inversion product instead.

---

## Copernicus Climate Change Service (C3S) products

C3S is the climate branch of Copernicus, coordinated by ECMWF, with products distributed through the Climate Data Store (CDS). Despite the "Climate Change" branding, the products indexed here are **operational seasonal prediction systems**, not climate projections — forward-looking forecasts on monthly initialisation cycles, which is what places them in scope.

### Global

#### [C3S Seasonal Multi-System](./models/climate_models/global/multi-national/c3s-seasonal.md)
- **Coordinating Unit:** ECMWF on behalf of the EU
- **Production:** 9 independently operated fully coupled seasonal systems from 8 centres
- **Contributing centres:** ECMWF, UK Met Office, Météo-France, DWD, CMCC, NCEP, JMA, ECCC (two systems), BoM
- **Common delivered grid:** 1° × 1°
- **Forecast length:** ~6–7 months, varying by system (184 to 217 days)
- **Ensemble design:** Multi-system; no single fixed total, per-system real-time sizes from 11 to 55
- **Release schedule:** ECMWF on the 6th of each month at 12 UTC; all other systems on the 10th
- **Per-contributor terms:** contributions from NCEP, JMA, ECCC and BoM are in-kind from non-European centres and carry an additional licence condition
- **Note:** succeeded EUROSIP, retired October 2019. The `system` keyword is load-bearing — a real-time forecast must be matched to the correct hindcast `system` value for anomalies to be computed correctly.

#### [SEAS5](./models/climate_models/global/eu/seas5.md)
- **Production Unit:** ECMWF
- **Resolution:** TCO319/L91 (~36 km) atmosphere; 0.25° ORCA ocean, 75 levels
- **Forecast length:** 7 months, extended to 13 months once a season
- **Ensemble size:** 51 members
- **Note:** ECMWF's own contribution to the C3S multi-system (`system=51`), and separately the driving system for three impact models indexed below — GloFAS Seasonal, EFAS Seasonal, and CEMS Fire Seasonal. SEAS6 is expected; see [`STATUS.md`](./STATUS.md).

### Regional

#### [ItaliaMeteo Seasonal Downscaling](./models/climate_models/regional/italy/italiameteo-seas5-downscaling.md)
- **Production Unit:** Agenzia ItaliaMeteo
- **Country:** Italy
- **Resolution:** 0.1° (~10 km), 131 × 131 over 35–48°N, 6–19°E
- **Forecast length:** 4 calendar months
- **Note:** downstream of C3S rather than a C3S contribution. It is a quantile-mapped statistical downscaling of SEAS5 against ERA5-Land, obtained through the CDS but distributed by ItaliaMeteo under CC BY 4.0 via MeteoHub — so it appears in this index by provenance, not by distribution channel. Italy's actual contribution to the C3S multi-system is CMCC SPS4, an unrelated system.

---

## Copernicus Emergency Management Service (CEMS) products

CEMS is the disaster-response branch of Copernicus, managed by the European Commission's Joint Research Centre with ECMWF as computational centre. Products are distributed through the Early Warning Data Store (EWDS).

CEMS-Flood comprises the Global and European Flood Awareness Systems, both running the open-source **LISFLOOD** hydrological model at different resolutions. CEMS-Fire runs the **Global ECMWF Fire Forecast (GEFF)** model, which implements three national fire danger rating systems in parallel.

### Flood — global

#### [GloFAS Forecast](./models/hydrological_models/global/eu/cems-glofas-forecast.md)
- **Model core:** LISFLOOD
- **Resolution:** 0.05° (~5 km), 90°N–60°S
- **Forecast length:** 30 days, daily at 00 UTC
- **Forcing:** IFS ENS days 1–15 spliced onto one-day-old extended-range ENS for days 16–30; AIFS Single added at v4.4
- **Ensemble size:** 51
- **Version:** v4.5 (16 April 2026)
- **Note:** the EWDS dataset is the **30-day legacy configuration**. The GloFAS web portal moved to a 46-day sub-seasonal chain at v4.3; ECMWF states users are given continued access to the 30-day forecasts through EWDS.

#### [GloFAS Seasonal](./models/hydrological_models/global/eu/cems-glofas-seasonal.md)
- **Model core:** LISFLOOD
- **Resolution:** 0.05° (~5 km)
- **Forecast length:** 215 days, monthly from the 1st, published on the 10th
- **Forcing:** downscaled **runoff** from SEAS5 — not meteorology
- **Ensemble size:** 51
- **Note:** the EWDS description states 123 days; the live request constraints expose 215. The forecast has been 215 days since June 2021.

### Flood — Europe

#### [EFAS Seasonal](./models/hydrological_models/regional/eu/efas-seasonal.md)
- **Model core:** LISFLOOD
- **Resolution:** 1 arcminute (~1.4 km) — the finest hydrological forecast in this repository
- **Forecast length:** 215 days, monthly from the 1st
- **Forcing:** SEAS5 meteorology, 51 members
- **Version:** EFAS v5.6 (25 February 2026); forecasts carry their production version in file metadata
- **Note:** **the only openly available EFAS forecast product.** The EFAS medium-range forecast and historical simulation are both embargoed with real-time access restricted to EFAS partners; the seasonal product carries no such restriction.

### Fire danger — global

#### [CEMS Fire Seasonal](./models/fire_danger_models/global/eu/cems-fire-seasonal.md)
- **Model core:** GEFF (Global ECMWF Fire Forecast)
- **Rating systems:** Canadian FWI (7 indices), US NFDRS (4), Australian McArthur Mark 5 (3)
- **Resolution:** 1° × 1° per the EWDS documentation — but see the entry, the catalogue bounding box contradicts it
- **Forecast length:** 215 days, monthly from the 1st
- **Forcing:** SEAS5; 25 members to 2016, 51 from 2017
- **Licence:** CC-BY-4.0 — the only Copernicus product in this index with an SPDX-identified licence
- **Note:** explicitly not a real-time service. The reforecast (1981–2016, ERA-Interim-initialised) lives inside the same dataset as the forecast, with the ensemble size and initialisation source both changing at the 2016/2017 boundary.

### Observations at a glance

- **Two LISFLOOD systems, one model, two resolutions.** GloFAS at 0.05° globally and EFAS at 1 arcminute over Europe run the same hydrological core with independent calibrations — 1995 gauging stations for GloFAS v4.0, 1903 for EFAS v5.0.
- **The two seasonal flood chains are not the same design.** GloFAS Seasonal is forced with downscaled runoff from SEAS5; EFAS Seasonal is forced with SEAS5 meteorology. Their reference climatologies differ too — 1979–2022 against 1992–2022 — so anomalies from the two are not directly comparable.
- **Embargo is product-specific, not service-wide.** Within EFAS, the medium-range forecast runs 30 days behind the public feed and the historical simulation 6 days behind, both with real-time access limited to partners, while the seasonal product is current. The CEMS-FLOODS licence's Article 12 restricted-data clause is what makes this possible under a single licence.
- **The operational daily fire danger forecast has no in-scope channel.** ECMWF directs real-time fire danger users to EFFIS web services, which expose the GEFF fields over WMS only. A WCS request against the EFFIS endpoint returns an error stating the rasters are tile-indexed without WCS metadata, and WCS 1.0.0 exposes only two static layers. The seasonal dataset is the only openly retrievable GEFF product. Canada's [CWFIS](./models/fire_danger_models/regional/canada/cwfis.md) is the national system that does publish an operational gridded forecast, through WCS.

---

## Finding Copernicus products elsewhere in the repository

Copernicus products also appear in topic-based indexes where relevant:

- [`AI_MODELS.md`](./AI_MODELS.md) — indexes the AI-related models. No Copernicus product in this index is itself AI-based, though ECMWF's AIFS line is adjacent to CAMS Global architecturally, and GloFAS has taken AIFS Single meteorology as forcing to 15-day lead time since v4.4 (10 September 2025) — a downstream AI-influenced relationship not yet indexed there
- [`STATUS.md`](./STATUS.md) — tracks upcoming implementations and retirements

---

## What this index does not cover

- **Copernicus reanalysis products** (CAMS EAC4, ERA5, ERA5-Land, GLORYS12, CMEMS regional reanalyses, GloFAS and EFAS historical simulations, CEMS fire danger historical data) — this repository focuses on operational near-real-time forecasts, not historical reanalyses
- **Reforecast and hindcast archives** (GloFAS and EFAS medium-range and seasonal reforecasts) — historical-only, and covered within the parent forecast entries rather than given their own
- **Copernicus Land and Security services** — these remain outside the repository's scope. Note that Climate Change (C3S) and Emergency Management (CEMS) products **are** now included where they are forward-looking operational forecasts: seasonal prediction under C3S, and flood and fire danger forecasting under CEMS. Only their reanalysis and historical components are excluded.
- **Embargoed Copernicus forecasts** — the EFAS medium-range forecast (`efas-forecast`) is published with a 30-day delay and real-time access restricted to EFAS partners, which puts it outside the repository's open-access rule despite being a Copernicus forecast product
- **Copernicus products distributed only as rendered imagery** — the operational EFFIS and GWIS fire danger layers are served over WMS with no coverage retrieval available
- **Products available only through the legacy CMEMS Web API** — all products listed here are available through a current Copernicus data store
- **Copernicus Sentinel satellite data** — this is observational data, not model output

---

## Contributing

If a Copernicus product is added to the repository, please also add it to this index with a short summary matching the format above. The operator-country pattern is particularly worth noting in the entry — it's exactly the kind of non-obvious fact that makes this index useful.
