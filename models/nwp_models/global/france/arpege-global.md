# ARPEGE (Action de Recherche Petite Échelle Grande Échelle)

## What this model is
ARPEGE is the operational global deterministic numerical weather prediction system run by Météo-France.

It is the global member of Météo-France's NWP suite, used for medium-range forecasting worldwide and as the parent model providing initial and lateral boundary conditions for the regional AROME systems. ARPEGE is built on a stretched-grid spectral dynamical core, which gives it variable native resolution — finest over France and progressively coarser away from the central point. This design lets ARPEGE deliver high-resolution guidance over the French and European domains while still running globally.

ARPEGE is jointly developed with ECMWF as part of the IFS/ARPEGE shared code base, with Météo-France-specific operational configuration, physics, and data assimilation choices.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Global, with variable native resolution (highest over France)

---

## Basic details
- **Model type:** Global deterministic NWP (stretched-grid spectral)
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at the native variable resolution, ~5 km over France)
- **Native horizontal resolution:** Variable — approximately 5 km over France, ~24 km over the antipodes (TL1798 stretched spectral truncation)
- **Public distribution grids:**  
  - **0.25° global** (newer packages; 1440 × 721 regular lat–lon)  
  - **0.5° global** (legacy packages; 720 × 361 regular lat–lon)  
  - Companion ARPEGE Europe distribution at 0.1°
- **Vertical levels:** 105
- **Model top:** ~70 km
- **Forecast length:**  
  - 102 hours (4 days 6 hours) for 00, 06, and 18 UTC cycles  
  - 114 hours (4 days 18 hours) for 12 UTC cycle
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly through ~48 h, then 3-hourly thereafter (varies by package)

---

## What it provides
Deterministic forecasts of:
- Temperature, wind, humidity, pressure
- Total precipitation and precipitation rate
- Cloud cover (total, low, mid, high)
- Surface and upper-air fields on standard pressure levels
- Wind gusts, downward shortwave radiation, mean sea level pressure, and additional surface diagnostics
- A wide set of upper-air pressure levels suitable for synoptic and aviation use

ARPEGE serves as the primary lateral boundary condition source for the various AROME regional configurations (AROME France, AROME France HD, and the AROME Outre-Mer suite covering Antilles, French Guiana, New Caledonia, Polynesia, and Réunion–Mayotte).

---

## Data availability

ARPEGE data is openly licensed under the Etalab Open Licence and is available through three parallel channels, all carrying the same underlying forecasts but with different access patterns and resolution options.

### 1. Météo-France public API (`public-api.meteofrance.fr`)
The official developer portal provides ARPEGE data through both bulk GRIB packages and an OGC Web Coverage Service (WCS) for single-variable subsetting.

- **Bulk packages:**  
  `https://public-api.meteofrance.fr/previnum/DPPaquetARPEGE/v1/models/ARPEGE/grids/{0.25|0.1}/packages/{SP1|SP2|HP1|IP1}/productARP?...`
- **WCS single-variable:**  
  `https://public-api.meteofrance.fr/public/arpege/1.0/wcs/MF-NWP-GLOBAL-ARPEGE-{025-GLOBE|01-EUROPE}-WCS/GetCoverage?...`
- **Authentication:** Free API key required. Register and manage keys at https://portail-api.meteofrance.fr
- **Documentation:** https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/Mod+les+et+donn+es+de+pr+vision

### 2. data.gouv.fr open-data portal (`object.data.gouv.fr`)
The French government's open-data S3-style object storage hosts the same GRIB packages with no authentication required.

- **URL pattern:**  
  `https://object.data.gouv.fr/meteofrance-pnt/pnt/{run}/arpege/{grid}/{package}/arpege__{grid}__{package}__{packageTime}__{run}.grib2`  
  where `{grid}` is `025` or `01` (the decimal point is dropped) and `{run}` is in ISO-8601 form (e.g. `2026-05-01T00:00:00Z`).
- **Dataset landing pages:**  
  - 0.25° packages: https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-25deg/  
  - Legacy 0.5° world / 0.1° Europe packages: https://www.data.gouv.fr/datasets/donnees-du-modele-atmospherique-global-arpege
- **Authentication:** None
- **Use case:** Best for users who want unauthenticated bulk access without registering for an API key.

### 3. Community AWS mirror (`mf-models-on-aws.s3.amazonaws.com`)
A community-run mirror, not affiliated with Météo-France, that redistributes the same data on AWS. Publicly listed in the AWS Open Data Registry.

- **URL pattern:**  
  `https://mf-models-on-aws.s3.amazonaws.com/arpege-{world|europe}/v1/{YYYY-MM-DD}/{HH}/{PACKAGE}/{TIMEPACK}.grib2`
- **Inventory files:** Companion `.inv` files (replace `.grib2` with `.inv` in the URL) support partial downloads via HTTP Range requests.
- **AWS Open Data Registry listing:** https://registry.opendata.aws/meteo-france-models/
- **Documentation:** https://mf-models-on-aws.org/en/doc/datasets/v1/
- **Authentication:** None
- **Caveat:** Currently distributes the legacy 0.5° world / 0.1° Europe packages. Operated independently and not subject to Météo-France's operational change-management process.

### Summary
- **Is the data free?** Yes (Etalab Open Licence)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2

---

## Notes
- ARPEGE's stretched grid means the native resolution varies smoothly across the globe. The 0.25° (or 0.5°) public distribution interpolates the model output onto a regular latitude–longitude grid, so the published resolution does not reflect where the model is actually running at finer scales — high-resolution areas are present in the global file, just regridded.
- The 0.1° ARPEGE Europe distribution is a separate but closely related product covering Europe and North Africa. It is documented as part of the Météo-France ARPEGE family but is not strictly "global" — users specifically wanting the Europe-focused subset should request that grid explicitly.
- The two parallel public distributions (0.5° legacy, 0.25° newer) are both maintained as of early 2026. The 0.25° packages were introduced more recently and are typically preferred for new integrations; the 0.5° packages remain available primarily for backward compatibility and are still the only resolution served by the AWS community mirror.
- The companion ARPEGE Ensemble Prediction System (PEARP) provides probabilistic guidance from 35 members at 0.5° world / 0.1° Europe and is documented separately under ensemble models.

---

## Official documentation
- Météo-France public API documentation: https://portail-api.meteofrance.fr
- API Paquets Modèles overview (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853639487/
- ARPEGE technical description (PDF, Météo-France): linked from the data.gouv.fr datasets above
- Météo-France NWP documentation: https://donneespubliques.meteofrance.fr/?fond=produit&id_produit=130&id_rubrique=51
- AWS Open Data Registry — Atmospheric Models from Météo-France: https://registry.opendata.aws/meteo-france-models/
- Community mirror documentation: https://mf-models-on-aws.org/en/doc
