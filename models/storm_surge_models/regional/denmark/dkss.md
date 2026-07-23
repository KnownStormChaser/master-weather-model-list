# DKSS (Danish Storm Surge Model)

## What this model is
DKSS is the Danish Meteorological Institute's operational **three-dimensional baroclinic ocean circulation and storm-surge forecast system**. DMI uses it to predict and warn about coastal flooding and water levels around Denmark.

DKSS predicts **total water level, including the tide** — not a surge residual. Tidal sea level is imposed at the North Sea–Baltic open boundary from 17 harmonic constituents and propagates through the nested domains, so the distributed water-level field is the combined tide-plus-meteorological signal, encoded as *deviation of sea level from mean*. DMI does **not** distribute the tidal and surge components separately in this product.

Beyond water level, DKSS is a full ocean model: it also forecasts three-dimensional currents, water temperature and salinity on fixed depth layers, plus sea-ice thickness and concentration. It runs as a **six-domain two-way nest**, from a North Sea–Baltic regional parent down to individual Danish fjords and belts. All six share the same code, physics, cycle schedule, forcing chain and parameter set, differing in extent, resolution, and layer count; they are presented together here because DMI documents and distributes them as one model with six model areas.

---

## Who runs it
- **Organization:** Danish Meteorological Institute (DMI)
- **Country / region:** Denmark

---

## What area it covers
- **Coverage:** the north-western European shelf — North Sea and Baltic Sea — with progressively finer nests over the inner Danish waters, the Wadden Sea, the Limfjord, the Little Belt, and Roskilde Fjord / Isefjord
- **Inundation coverage:** TBD. HBM includes a documented wetting-and-drying facility capable of simulating the inter-tidal zone, but whether the operational DKSS domains extend onto land far enough for meaningful overland flooding output is not stated in the open-data documentation.

All bounds, grid dimensions and increments below are **live-verified from GRIB** (00 UTC run, 23 July 2026):

| Domain | Prefix / collection | Grid (lon × lat) | Longitude | Latitude | Δlon | Δlat | Depth layers |
|---|---|---|---|---|---|---|---|
| North Sea–Baltic Sea | `DKSS_NSBS_SF` / `dkss_nsbs` | 414 × 348 | 4.125°W – 30.292°E | 48.525 – 65.875°N | 5′ | 3′ (3 nm) | 50 |
| Inner Danish Waters | `DKSS_IDW_SF` / `dkss_idw` | 396 × 482 | 9.340 – 14.826°E | 53.587 – 57.596°N | 50″ | 30″ (0.5 nm) | 52 |
| Wadden Sea | `DKSS_WS_SF` / `dkss_ws` | 156 × 149 | 6.181 – 10.486°E | 53.225 – 55.692°N | 1′40″ | 1′ (1 nm) | 22 |
| Limfjord | `DKSS_LF_SF` / `dkss_lf` | 810 × 390 | 8.138 – 10.385°E | 56.461 – 57.109°N | 10″ | 6″ (0.1 nm) | 21 |
| Little Belt | `DKSS_LB_SF` / `dkss_lb` | 190 × 168 | 9.493 – 10.018°E | 55.246 – 55.710°N | 10″ | 10″ | 42 |
| Roskilde / Isefjord | `DKSS_IF_SF` / `dkss_if` | 185 × 117 | 11.613 – 12.124°E | 55.654 – 55.976°N | 10″ | 10″ | 10 |

Two corrections to DMI's published model-area table are noted under **Notes**.

---

## Basic details
- **Model type:** Deterministic storm surge / three-dimensional ocean model
- **Core hydrodynamic model:** **HBM** (HIROMB-BOOS Model), DMI's in-house circulation and sea-ice model — hydrostatic, free-surface, baroclinic, developed for shallow shelf seas
- **Dimensionality:** 3D baroclinic on fixed z-layers, with an explicit sea-ice module
- **Forecast length:** **120 hours (5 days)** — live-verified: 121 files per cycle, steps 0–120
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) — live-verified
- **Temporal output resolution:** 1 hour
- **Model time steps (per domain, barotropic / baroclinic / ice):** NSBS 20 s / 90 s / 900 s · IDW 10 s / 90 s / 900 s · WS 20 s / 90 s / 900 s · LF 2.5 s / 20 s / 900 s · LB 5 s / 20 s / 900 s · IF 5 s / 15 s / 900 s

---

## Grid and bathymetry
- **Grid type:** structured regular latitude–longitude, one grid per domain
- **Horizontal resolution:** see domain table above — from 3 nautical miles on the regional parent down to 0.1 nautical mile (~185 m latitude spacing) in the Limfjord
- **Bathymetry source:** TBD — not stated in the open-data documentation
- **Wetting and drying:** HBM supports it; operational configuration TBD

### Vertical layer structure
- Layers are **fixed depth (z) layers**, not terrain-following.
- The **surface layer is 8 m thick** in the North Sea–Baltic and Wadden Sea domains and **2 m thick** in the other four.
- Below an 8 m surface layer, layers are 2 m thick; below a 2 m surface layer, they are 1 m thick. Layer thickness increases toward the sea floor, and the bottom layer has no fixed thickness — it extends from the base of the second-lowest layer to the sea bed.
- Current, temperature and salinity should be read as the **vertical mean over the layer**, not a point value at the encoded depth.

---

## Vertical datum and reference level
- **Vertical datum:** model mean sea level. DMI encodes the field as *deviation of sea level from mean* (GRIB1 parameter 82, `DSLM`).
- **What the water level field is measured relative to:** total water level (tide plus meteorological contribution) relative to the model's own mean sea level — **not** a surge residual above a tidal prediction, and **not** referenced to a national datum such as DVR90 or to LAT/Chart Datum.
- **Datum conversion offsets provided?** No. No static datum-offset field or per-station conversion table is published with the gridded product.
- **Mean sea level trend / SLR handling:** TBD — not documented.

> Users comparing DKSS against Danish tide-gauge observations need to establish the offset between model mean sea level and the gauge datum themselves; DMI does not ship it with these files.

---

## Tide handling
- **Are tides included?** Yes. Tidal sea level is applied at the North Sea–Baltic open boundary and propagates into all nested domains.
- **Tidal forcing source:** 17 harmonic constituents applied at the regional open boundary. The specific constituent set is not enumerated in the open-data documentation (TBD).
- **Separation of components:** None. Only the combined total is distributed — no separate tide field, no separate surge residual.
- **Tide–surge interaction:** Modelled nonlinearly within the run, since tide and meteorological forcing are integrated together rather than superposed afterward.

---

## Forcing and coupling
- **Meteorological forcing — wind:** 10 m wind from [HARMONIE-AROME DINI](../../../nwp_models/regional/denmark/harmonie-dmi.md) for the first 60 hours, then ECMWF IFS global (GLM) for the remainder of the 120 h forecast
- **Meteorological forcing — pressure:** mean sea level atmospheric pressure from the same HARMONIE DINI / ECMWF chain
- **Additional atmospheric forcing:** 2 m temperature, 2 m specific humidity, and total cloud cover — used by the surface heat-flux and ice modules
- **Wave contribution:** None. DKSS is not coupled to [WAM](../../../wave_models/regional/denmark/wam-dmi.md) and does not include wave setup. DMI reads the two models *together* when forecasting maximum coastal water height, but they are separate one-way-forced runs on a shared meteorological driver, not a coupled system.
- **River discharge / freshwater forcing:** TBD — not documented in the open-data pages
- **Ocean forcing / open boundary conditions (North Sea–Baltic parent):**
  1. Surge contribution from **NOAMOD**, DMI's North Atlantic surge model
  2. Tidal sea level from 17 harmonic constituents
  3. Monthly climatological temperature and salinity fields applied via a sponge layer
- **Nesting:** Inner Danish Waters and Wadden Sea are nested in the North Sea–Baltic regional model; Limfjord is nested in both the regional and the Inner Danish Waters model; Little Belt and Roskilde/Isefjord are nested in the Inner Danish Waters model.

---

## Data assimilation
- **Assimilates water level observations:** Not documented for the operational open-data DKSS chain. (TBD — DMI has published research on 3D-Var and EnOI assimilation of temperature and salinity profiles into HBM, but the open-data documentation does not state whether any assimilation is active in the operational forecast suite.)

---

## What it provides
Every domain carries the **same nine parameter types**. Water level, wind and ice are surface-only; current, temperature and salinity are additionally provided on every depth layer. GRIB1 parameter numbers are given in `table2Version = 128`, centre `ekmi`, but follow **standard WMO GRIB1 Table 2 ocean codes** — see the decoding warning under Notes.

| GRIB1 param | DMI code | Description | Unit | Levels |
|---|---|---|---|---|
| 82 | `DSLM` | Deviation of sea level from mean | m | surface only |
| 33 | `UGRD` | u-component of wind (10 m, forcing field) | m s⁻¹ | surface only |
| 34 | `VGRD` | v-component of wind (10 m, forcing field) | m s⁻¹ | surface only |
| 49 | `UOGRD` | u-component of current | m s⁻¹ | surface + all depth layers |
| 50 | `VOGRD` | v-component of current | m s⁻¹ | surface + all depth layers |
| 80 | `WTMP` | Water temperature | **°C** | surface + all depth layers |
| 88 | `SALTY` | Salinity | **g/kg (PSU)** | surface + all depth layers |
| 92 | `ICETK` | Ice thickness | m | surface only |
| 91 | `ICEC` | Ice concentration (1 = ice, 0 = no ice) | fraction | surface only |

Live-verified message counts per file: North Sea–Baltic 209, Inner Danish Waters 217, Little Belt 177, Wadden Sea 97, Limfjord 93, Roskilde/Isefjord 49 — each matching DMI's published band tables exactly.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Output geometry:** **Gridded fields only.** DKSS station time series at Danish tide gauges exist as rendered plots on `ocean.dmi.dk`, but no point-geometry data files are published in the open-data feed, so the storm-surge point-output exception does not apply to this entry.
- **Data formats:** **GRIB edition 1**
- **File packaging:** one file per forecast time step, flat prefix, named `DKSS_<AREA>_SF_<modelRun>_<validTime>.grib`
- **Official download location:**
  - AWS S3 (no account, no key, no registration): `s3://dmi-opendata/forecastdata/DKSS_NSBS_SF/` and the five sibling area prefixes (region `eu-north-1`)
    `aws s3 ls --no-sign-request s3://dmi-opendata/forecastdata/DKSS_IDW_SF/`
  - DMI Forecast STAC API (free API key required): `https://dmigw.govcloud.dk/v1/forecastdata/collections/dkss_nsbs`
  - DMI Forecast EDR API (free API key required): `https://dmigw.govcloud.dk/v1/forecastedr/collections/dkss_nsbs`

---

## Notes

### Decoding warnings (live-verified)

- **eccodes resolves DKSS parameter names incorrectly — do not trust `shortName`.** The files declare `table2Version = 128` with centre `ekmi`, which causes eccodes to apply the **ECMWF local table 128** rather than the standard WMO ocean codes DMI actually uses. The resulting names are wrong and dangerously plausible:

  | GRIB1 param | eccodes reports | Actually is |
  |---|---|---|
  | 33 | `rsn` — "Snow density" | u-component of wind |
  | 34 | `sst` — "Sea surface temperature" | v-component of wind |
  | 49 | `10fg` — "Maximum 10 metre wind gust" | u-component of current |
  | 50 | `lspf` — "Large-scale precipitation fraction" | v-component of current |
  | 80, 82, 88, 91, 92 | "Experimental product" (unnamed) | water temp., sea level deviation, salinity, ice conc., ice thickness |

  Read `indicatorOfParameter` directly and map it against DMI's published parameter table (or standard WMO GRIB1 Table 2 ocean codes). Value ranges corroborate the correct mapping: on the 23 July 2026 00 UTC Inner Danish Waters run, parameter 80 spans 3.9–20.6, parameter 88 spans 0–34.5, and parameter 82 spans −0.06 to +0.62.

- **Units differ from the nominal Table 2 definitions.** Water temperature (80) is distributed in **°C**, not kelvin. Salinity (88) is distributed in **g/kg / PSU**, not kg/kg. Both confirmed live and consistent with DMI's parameter table. A pipeline that applies a naive Table 2 unit conversion will produce nonsense.

- **The surface layer is encoded twice.** Every vertically-varying parameter appears once at `level = 0` and again at `level =` the mid-depth of the top layer, giving *n + 1* records for *n* layers. DMI does this because the top layer thickness differs between domains. Deduplicate, or you will double-count the surface.

- **Depth levels are layer mid-depths in integer metres, and the sequence skips values.** `typeOfLevel = depthBelowSea`; the encoded level is the layer midpoint rounded to a whole metre. In the 2 m-surface-layer domains, mid-depths of 1.0, 2.5, 3.5, 4.5 … round to 1, 3, 4, 5 …, so **level 2 does not exist**. Live-verified level sets: NSBS 50 levels spanning 4–525 m · IDW 52 levels spanning 1–75 m · LB 42 levels spanning 1–55 m · WS 22 levels `[4, 9, 11, 13 … 49]` · LF 21 levels spanning 1–22 m · IF 10 levels `[1, 3, 4 … 11]`.

- **Land is bitmap-masked, so `numberOfValues` is far below `Ni × Nj`** — heavily so in the fjord domains. Live-verified at the 23 July 2026 00 UTC run: Limfjord carries 49,591 values of 315,900 grid points (16%), North Sea–Baltic 34,037 of 144,072 (24%), Roskilde/Isefjord 7,824 of 21,645 (36%), Inner Danish Waters 80,984 of 190,872 (42%), Wadden Sea 11,512 of 23,244 (50%). Honour the GRIB bitmap.

- **DKSS grids scan south-to-north**, the opposite of the sibling [WAM](../../../wave_models/regional/denmark/wam-dmi.md) grids. Do not share scanning assumptions between the two.

### Documentation-vs-reality discrepancies (live-verified)

- **Wadden Sea layer count.** DMI's model-area table states 24 vertical layers for `dkss_ws`. The live GRIB carries **22**, and DMI's own parameter table on the same page also implies 22 (bands 10–31). The "24" appears to be an error in the summary table.
- **Wadden Sea longitude increment.** DMI's model-area table gives 1′24″ (0.02333°) for `dkss_ws`. The live GRIB gives **1′40″ (0.027774°)**, derived from 156 points spanning 6.181–10.486°E. The live value is also the one consistent with DMI's own "1 nautical mile" characterisation of the domain; 1′24″ would give roughly 0.8 nm. Treat 1′40″ as correct.

### Relationships and scope

- **Meteorological driver:** [HARMONIE-AROME DMI (DINI/IG)](../../../nwp_models/regional/denmark/harmonie-dmi.md).
- **Sibling marine model:** [WAM (DMI)](../../../wave_models/regional/denmark/wam-dmi.md) shares the same forcing chain and cycle schedule but is not coupled to DKSS.
- **DKSS-EPS is not in the catalog.** DMI operates a 19-member storm-surge ensemble (18 members forced by the DMI-COMEPS atmospheric ensemble plus one deterministic member, with perturbation coming exclusively from the weather forcing). It is published only as rendered ensemble plots for Danish coastal stations on `ocean.dmi.dk` — no raw gridded or point data files — so it falls outside scope.
- **Not the Copernicus Marine Baltic physics product.** DMI also contributes HBM-based products to Copernicus Marine (`BALTICSEA_ANALYSISFORECAST_PHY_003_006`), distributed as NetCDF on a 1 nm grid with data assimilation, under the Copernicus Marine licence. That is a separate system.
- **S3 retention is longer than the documented API window.** DMI documents 48 hours of runs in the STAC API; the S3 bucket was live-measured on 23 July 2026 holding cycles back to 20 July 00 UTC (~3¼ days).
- **Publication latency.** DMI documents all DKSS areas complete at approximately +3h20m after cycle time; observed S3 timestamps for the 00 UTC 23 July 2026 run match (03:21–03:36 UTC).

---

## Official documentation
- DMI: *Forecast Data Storm Surge Model (DKSS)* — includes the full per-domain parameter and band tables
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-storm-surge-model-dkss
- DMI: *Storm Surge Model (DKSS) EDR API parameter list*
  https://www.dmi.dk/friedata/dokumentation/data/storm-surge-model-dkss-edr-api-parameter-list
- DMI ocean models: *HBM*
  https://ocean.dmi.dk/models/hbm.php
- DMI: *Forecast Data Availability*
  https://opendatadocs.dmi.govcloud.dk/Data/Forecast_Data_Availability
- AWS Open Data Registry: *Danish Meteorological Institute (DMI) Open Data Forecasts*
  https://registry.opendata.aws/dmi-opendata/
- Murawski et al. (2021) and Frishfelds et al. (2022) on HBM nested configurations; *Operational ocean modelling at DMI*
  https://www.unoceanprediction.org/sites/default/files/halls/files/2023-12/Operational-ocean-modelling-at-DMI_0.pdf
