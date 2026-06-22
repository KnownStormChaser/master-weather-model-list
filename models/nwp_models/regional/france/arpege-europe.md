# ARPEGE Europe

## What this model is
ARPEGE Europe is the **regional 0.1° public distribution** of Météo-France's operational global ARPEGE model, covering Europe and North Africa.

It is not a separate model — the underlying forecasts come from the same global ARPEGE run on its stretched-grid spectral core, where native resolution is finest over France (~5 km). The Europe distribution simply interpolates that output onto a regional 0.1° regular latitude–longitude grid, providing a finer-resolution view of the same forecast over the European domain than the 0.25° or 0.5° global packages can offer.

For the parent system's full technical description, see [ARPEGE](../../global/france//arpege-global.md).

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Europe and North Africa
- **Domain details:** Regional 0.1° regular latitude–longitude grid (approximate bounds: **72°N – 20°N**, **32°W – 42°E**)

---

## Basic details
- **Model type:** Global deterministic NWP, regional distribution (stretched-grid spectral parent)
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF)
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized)
- **Native horizontal resolution:** Variable — approximately 5 km over France, ~24 km over the antipodes (TL1798 stretched spectral truncation)
- **Public distribution grid:** **0.1° (~10 km)** regular latitude–longitude over Europe and North Africa
- **Vertical levels:** 105
- **Model top:** ~0.1 hPa (~65 km); lowest model level ~10 m above ground
- **Forecast length:**
  - 102 hours (4 days 6 hours) for 00, 06, and 18 UTC cycles
  - 114 hours (4 days 18 hours) for 12 UTC cycle
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly through ~48 h, then 3-hourly thereafter (varies by package)
- **Time step:** 240 s

---

## What it provides
Deterministic forecasts of:
- Temperature, wind, humidity, pressure
- Total precipitation and precipitation rate
- Cloud cover (total, low, mid, high)
- Surface and upper-air fields on standard pressure levels
- Wind gusts, downward shortwave radiation, mean sea level pressure, and additional surface diagnostics
- A wide set of upper-air pressure levels suitable for synoptic and aviation use

The variable set matches the ARPEGE Global packages; only the geographic domain and grid resolution differ.

---

## Data availability

ARPEGE Europe data is openly licensed under the Etalab Open Licence and is available through the same three parallel channels as ARPEGE Global.

### 1. Météo-France public API (`public-api.meteofrance.fr`)
- **Bulk packages:**
  `https://public-api.meteofrance.fr/previnum/DPPaquetARPEGE/v1/models/ARPEGE/grids/0.1/packages/{SP1|SP2|HP1|IP1}/productARP?...`
- **WCS single-variable:**
  `https://public-api.meteofrance.fr/public/arpege/1.0/wcs/MF-NWP-GLOBAL-ARPEGE-01-EUROPE-WCS/GetCoverage?...`
- **Authentication:** Free API key required. Register and manage keys at https://portail-api.meteofrance.fr
- **Documentation:** https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/Mod+les+et+donn+es+de+pr+vision

### 2. data.gouv.fr open-data portal (`object.data.gouv.fr`)
- **URL pattern:**
  `https://object.data.gouv.fr/meteofrance-pnt/pnt/{run}/arpege/01/{package}/arpege__01__{package}__{packageTime}__{run}.grib2`
  where `{run}` is in ISO-8601 form (e.g. `2026-05-01T00:00:00Z`).
- **Dataset landing page:** https://www.data.gouv.fr/datasets/donnees-du-modele-atmospherique-global-arpege
- **Authentication:** None
- **Use case:** Best for users who want unauthenticated bulk access without registering for an API key.

### 3. Community AWS mirror (`mf-models-on-aws.s3.amazonaws.com`)
A community-run mirror, not affiliated with Météo-France, that redistributes the same data on AWS. Publicly listed in the AWS Open Data Registry.

- **URL pattern:**
  `https://mf-models-on-aws.s3.amazonaws.com/arpege-europe/v1/{YYYY-MM-DD}/{HH}/{PACKAGE}/{TIMEPACK}.grib2`
- **Inventory files:** Companion `.inv` files (replace `.grib2` with `.inv` in the URL) support partial downloads via HTTP Range requests.
- **AWS Open Data Registry listing:** https://registry.opendata.aws/meteo-france-models/
- **Documentation:** https://mf-models-on-aws.org/en/doc/datasets/v1/
- **Authentication:** None
- **Caveat:** Operated independently and not subject to Météo-France's operational change-management process.

### Summary
- **Is the data free?** Yes (Etalab Open Licence)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2

---

## Notes
- ARPEGE Europe is **not a separate model run** — it is a regional output grid from the same global ARPEGE forecast as the 0.25° and 0.5° global packages. The 0.1° resolution is meaningful because ARPEGE's stretched grid runs at native resolutions finer than 0.1° over Europe, so the regional distribution preserves more of that detail than the 0.25° global packages.
- For users whose area of interest is entirely within Europe and North Africa, the 0.1° Europe distribution is generally preferred over the 0.25° or 0.5° global packages. Users needing global coverage should use ARPEGE Global.
- The companion ARPEGE Ensemble Prediction System (PEARP) provides probabilistic guidance from 35 members and includes a matching 0.1° Europe distribution; PEARP is documented separately under ensemble models.
- Within the AROME family, the higher-resolution AROME France (1.3 km native, 0.025° public) is nested inside ARPEGE for its lateral boundary conditions; ARPEGE Europe is positioned between ARPEGE Global and AROME in resolution and domain extent.

---

## Official documentation
- Météo-France public API documentation: https://portail-api.meteofrance.fr
- API Paquets Modèles overview (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853639487/
- ARPEGE technical description (PDF, Météo-France): linked from the data.gouv.fr datasets above
- Météo-France NWP documentation: https://donneespubliques.meteofrance.fr/?fond=produit&id_produit=130&id_rubrique=51
- AWS Open Data Registry — Atmospheric Models from Météo-France: https://registry.opendata.aws/meteo-france-models/
- Community mirror documentation: https://mf-models-on-aws.org/en/doc
