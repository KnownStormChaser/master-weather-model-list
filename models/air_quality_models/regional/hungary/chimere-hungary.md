# CHIMERE Hungary

## What this model is
CHIMERE Hungary is the operational **air quality / atmospheric composition forecast** run by HungaroMet (Hungarian Meteorological Service) using the CHIMERE Eulerian offline chemistry-transport model (CTM). It produces gridded hourly forecasts of six regulated pollutants — CO, NO₂, O₃, SO₂, PM10, and PM2.5 — over the Carpathian Basin and three high-resolution city domains (Budapest, Miskolc, Pécs).

The forecasts are driven by HungaroMet's [AROME](../../../nwp_models/regional/hungary/arome-hungary.md) meteorology and EMEP anthropogenic emissions, and are intended for public-health air quality forecasting and air quality assessment.

---

## Who runs it
- **Organization:** HungaroMet (Hungarian Meteorological Service, formerly OMSZ — Országos Meteorológiai Szolgálat)
- **Country / region:** Hungary

---

## What area it covers
- **Coverage:** Hungary and the wider Carpathian Basin, plus three nested high-resolution city windows
- **Domain details (four published domains):**

  | Domain | Code | Bounds | Resolution | Grid (nx × ny) |
  |---|---|---|---|---|
  | Carpathian Basin | HUN | 45°N–50°N, 14°E–25°E | 0.1° × 0.1° (≈ 8–11 km) | 111 × 51 |
  | Budapest | BUD | 47.30°N–47.66°N, 18.85°E–19.37°E | 0.02° × 0.015° (≈ 1.5–2 km) | 27 × 25 |
  | Miskolc | MIS | 48.02°N–48.185°N, 20.51°E–20.89°E | 0.02° × 0.015° (≈ 1.5–2 km) | 20 × 12 |
  | Pécs | PEC | 46.01°N–46.19°N, 18.11°E–18.39°E | 0.02° × 0.015° (≈ 1.5–2 km) | 15 × 13 |

  Grid dimensions read from the distributed NetCDF files (2026-07) and confirmed against each domain's bounds ÷ resolution.
- **Projection:** Regular latitude–longitude (latlon)

---

## Basic details
- **Model type:** Air quality / atmospheric composition (regional offline chemistry-transport model)
- **Model system / core:** CHIMERE (multi-scale Eulerian offline CTM, developed by LMD/IPSL and partners)
- **Model version:** CHIMERE-2017
- **Horizontal resolution:** 0.1° × 0.1° (HUN); 0.02° × 0.015° (BUD, MIS, PEC)
- **Vertical levels:** Model vertical structure not documented. The **distributed** product is a single near-surface layer only — 2-D fields with no vertical dimension; the server metadata's `height_levels` and `pressure_levels` lists are both empty, and each field carries `cell_methods = "bottom_top: mean"` (a near-surface layer mean).
- **Model top:** TBD (not documented; not applicable to the surface-only distributed product)
- **Forecast length:** 48 hours (0–48 h)
- **Update frequency / cycles:** 1× daily (00 UTC), all domains
- **Temporal output resolution:** 1 hour

---

## Meteorological driver
- **Driving NWP model:** [AROME Hungary](../../../nwp_models/regional/hungary/arome-hungary.md) (HungaroMet's 2.5 km convection-permitting AROME)
- **Coupling:** Offline (one-way) — CHIMERE ingests AROME forecast fields; the dataset description explicitly describes CHIMERE as an "off-line" CTM
- **Update source frequency (if offline):** TBD (not documented)

---

## Chemistry and aerosols
> The HungaroMet dataset description does not document the chemistry/aerosol configuration. The fields below are marked TBD for this deployment; see *Notes* for general CHIMERE-2017 model characteristics.
- **Gas-phase chemical mechanism:** TBD (CHIMERE-2017 default is the MELCHIOR2 reduced mechanism; not confirmed for this deployment)
- **Number of chemical species:** TBD
- **Aerosol treatment:** TBD (CHIMERE uses a sectional aerosol scheme)
- **Aerosol components represented:** TBD (CHIMERE typically represents sulfate, nitrate, ammonium, organic and black carbon, dust, sea salt, and primary PM)
- **Heterogeneous/aqueous chemistry:** TBD

---

## Emissions
- **Anthropogenic inventory:** EMEP gridded emissions (via the CEIP WebDab emission database)
- **Biogenic emissions:** TBD (not documented; CHIMERE commonly uses MEGAN)
- **Wildfire emissions:** TBD
- **Dust scheme:** TBD
- **Sea salt scheme:** TBD
- **Other sources:** TBD

---

## Data assimilation
- **Assimilates AQ observations:** No air quality data assimilation is described in the HungaroMet dataset description; the system runs as a forecast CTM driven by AROME meteorology and EMEP emissions.

---

## What it provides
Hourly gridded near-surface concentration forecasts, one parameter per file. **Five species are actually distributed** (verified against the live feed across all four domains, 2026-07): NO2, O3, SO2, PM10, PM2.5 — all in µg/m³ (NetCDF `units = "ug/m3"`). CO is listed in both the dataset-description PDF and the server's `chimere.json`, **but no CO files are present in any domain**, so it is treated here as documented-but-not-currently-published.

| Parameter | Description | Unit | In live feed? |
|---|---|---|---|
| NO2 | Nitrogen dioxide concentration | µg/m³ | Yes |
| O3 | Tropospheric ozone concentration | µg/m³ | Yes |
| SO2 | Sulphur dioxide concentration | µg/m³ | Yes |
| PM10 | PM10 (particulate matter) concentration | µg/m³ | Yes |
| PM25 | PM2.5 (fine particulate matter) concentration | µg/m³ | Yes |
| CO | Carbon monoxide concentration | ppb | No — documented but absent from the feed |

---

## Data availability
- **Is the data free?** Yes
- **License:** HungaroMet Open Data Portal terms (free use without modification; attribution required as *"Database: Meteorological Database, HungaroMet Nonprofit Zrt."*; modifications require written consent, with example attribution forms provided in the General Terms of Use). Warnings, alerts, and aviation forecasts may only be redistributed unmodified.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (classic), one parameter × one lead time per file, each compressed inside its own ZIP archive (`.nc.zip`). Files use HungaroMet's `HMS` convention, **not CF**: there are no `lat`/`lon`/`time` coordinate variables — the grid is defined solely by global attributes (`Lo1`, `La1`, `Dx`, `Dy`, `Nx`, `Ny`), so georeferencing must be reconstructed from those. The `version=1.0` global attribute is the converter/file-format version, not the CHIMERE model version.
- **Official download location:**
  https://odp.met.hu/weather/nwp/CHIMERE/
  - **Path structure:** `https://odp.met.hu/weather/nwp/CHIMERE/{DOMAIN}/nc/{HH}/` — `{DOMAIN}` ∈ {HUN, BUD, MIS, PEC}; only the `00` UTC run directory exists
  - **Machine-readable metadata:** `https://odp.met.hu/weather/nwp/CHIMERE/chimere.json` (domain geometries, forecast length, output frequency, variable list and units)
  - Each forecast is split into 49 files per parameter — one per hourly lead time, `+00000` … `+04800`
- **File naming convention** (confirmed against the live feed):
  - `CHIMERE_<domain>-<parameter>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - `<domain>`: HUN / BUD / MIS / PEC
  - `<parameter>`: NO2, O3, SO2, PM10, PM25 (CO documented but not currently present)
  - `<YYYYMMDD>`: forecast date · `<HHmm>`: init time UTC (`0000`) · `<TTTtt>`: lead time, hours (TTT) + minutes (tt), e.g. `+02400` = +24 h

---

## Notes
- **Offline multi-scale CTM:** CHIMERE is designed to run from the hemispheric scale down to the urban scale, with resolutions from ~1–2 km to hundreds of km. The HungaroMet deployment uses this multi-scale capability to nest three ~1.5 km city domains (Budapest, Miskolc, Pécs) inside the ~10 km Carpathian Basin domain.
- **Meteorological coupling:** Driven offline by HungaroMet's [AROME](../../../nwp_models/regional/hungary/arome-hungary.md) forecast — both systems are operated by HungaroMet and distributed through the same open data portal.
- **Emissions source:** Anthropogenic emissions come from the EMEP/CEIP gridded inventory rather than a national HungaroMet inventory.
- **Verification:** HungaroMet points to the Copernicus Atmosphere Monitoring Service (CAMS) for regularly updated CHIMERE verification results. CHIMERE is also one of the constituent models in the [CAMS Regional](../eu/cams-regional.md) European air quality ensemble; the HungaroMet deployment documented here is an independent national configuration.
- **Uncertainties:** Per the dataset description, forecast uncertainty arises from the input emission inventory, the driving meteorological forecast, and the nonlinear chemical/physical process descriptions in the CTM.
- **Chemistry configuration not documented:** The HungaroMet dataset description does not specify the gas-phase mechanism, aerosol scheme, or vertical structure. The general CHIMERE-2017 characteristics noted in the *Chemistry and aerosols* section are model-level defaults, not confirmed for this specific deployment.

---

## Official documentation
- HungaroMet ODP CHIMERE folder (root): https://odp.met.hu/weather/nwp/CHIMERE/
- CHIMERE public dataset description (PDF, English): https://odp.met.hu/weather/nwp/CHIMERE/Description_airquality_forecast-CHIMERE-en.pdf
- CHIMERE dataset description (PDF, Hungarian): https://odp.met.hu/weather/nwp/CHIMERE/Leiras_levegominoseg_elorejelzes-CHIMERE-hu.pdf
- HungaroMet open data portal (root): https://odp.met.hu/
- CHIMERE model home (LMD / IPSL): https://www.lmd.polytechnique.fr/chimere/
- Copernicus Atmosphere Monitoring Service (CAMS): https://atmosphere.copernicus.eu/
- EMEP / CEIP WebDab emission database: https://www.ceip.at/webdab-emission-database

### Contact
- Open data technical contact: odp@met.hu
