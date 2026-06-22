# AROME Outre-Mer

## What this model is
AROME Outre-Mer (AROME-OM) is the operational convection-permitting regional deterministic numerical weather prediction system run by Météo-France over France's overseas territories. It is **a single model configuration run on five separate regional domains** — the Antilles, French Guiana, New Caledonia, French Polynesia, and Réunion–Mayotte — rather than five distinct models. Météo-France documents it as one system ("LE MODELE AROME OUTRE-MER"), and this entry follows that structure: shared characteristics are described once, with per-domain specifics collected in the summary table below.

Internally designated **PAROTRO** (AROME-Tropical), AROME-OM is designed for **short-range forecasting and nowcasting** of high-impact tropical and equatorial weather: deep convection, intense rainfall, thunderstorms, and local wind effects (sea breezes, terrain channelling). It shares the same dynamical core, physics packages, and vertical grid as [AROME France](./arome-france.md), but differs in two important respects described below: it has no data assimilation of its own, and its forecast range and output level set are smaller.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (overseas territories — Antilles, French Guiana, New Caledonia, French Polynesia, Réunion & Mayotte)

---

## How it is initialized
Unlike AROME France, **AROME-OM has no data assimilation of its own.** It is coupled for its initial conditions from two larger-scale models:

- **Upper-air initial conditions:** provided by **ECMWF's IFS** (~16 km), via dynamical downscaling with a prior "warmup" spin-up integration
- **Surface initial conditions:** provided by Météo-France's **[ARPEGE](../../global/france/arpege-global.md)**

This is a meaningful difference from AROME France, which runs its own 3DEnVar assimilation cycle. AROME-OM is effectively a high-resolution dynamical downscaling driven by IFS and ARPEGE, without an independent analysis.

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core); internal designation PAROTRO
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km (see note 4)
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude, per domain
- **Vertical levels:** 90
- **Isobaric output levels (19):** 100, 150, 175, 200, 225, 250, 275, 300, 350, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa
- **Height-above-ground output levels (14):** 2, 10, 20, 50, 100, 250, 500, 750, 1000, 1250, 1500, 2000, 2500, 3000 m
- **Forecast length:** Up to 42 hours, hourly (see note 2)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC) for all domains **except French Polynesia, which runs 2× daily (00, 12 UTC)**
- **Temporal output resolution:** Hourly
- **Operational availability (UTC run → delivery):** 00→07:30, 06→13:30, 12→19:30, 18→01:30 (next day)
- **Numerical precision:** Mixed precision
- **Ocean coupling:** 1D ocean model
- **Archive start:** 8 December 2015

Note that the output level set is coarser than metropolitan AROME France (which distributes 24 isobaric and 25 height levels); AROME-OM provides 19 isobaric and 14 height levels.

---

## What it provides
Deterministic forecasts distributed in **parameter group packages** following WMO GFNC naming conventions. The package structure is the same across all five domains:

**Surface fields**
- **SP1** — Mean sea-level pressure, 10 m wind (U, V, direction, speed, gusts), 2 m temperature and humidity, total cloud cover, precipitation, snow, downward shortwave flux, graupel
- **SP2** — Surface pressure and temperature, low/mid/high cloud cover, CAPE, boundary-layer height, total column water, 2 m min/max temperature, dewpoint, specific humidity
- **SP3** — Column water vapour, evaporation flux, latent and sensible heat fluxes, longwave/shortwave radiation fluxes (including clear-sky), surface stress (U, V), satellite brightness temperatures (channels vary by domain — see note 5)

**Isobaric fields** (19 levels, 100–1000 hPa unless noted)
- **IP1** — Temperature, humidity, wind (U, V), geopotential height
- **IP2** — Cloud water, cloud rain, cloud snow, cloud ice water content, cloud fraction
- **IP3** — Dewpoint, specific humidity, wind direction and speed, vertical velocity, vertical velocity squared, total precipitation
- **IP4** — Turbulent kinetic energy (10 levels), radar reflectivity (5 levels, 200–925 hPa)
- **IP5** — Tropopause temperature/pressure, equivalent potential temperature (16 levels), wind and geopotential at the 1500 m and 2000 m isentropic levels

**Height-above-ground fields** (14 levels, 20–3000 m unless noted)
- **HP1** — Temperature, humidity, wind (U, V, direction, speed), pressure, geopotential
- **HP2** — TKE, cloud water/rain/snow/fraction, cloud ice water content, dewpoint, specific humidity
- **HP3** — Radar reflectivity (7 levels: 500, 750, 1000, 1500, 2000, 2500, 3000 m)

---

## Domains

| Domain | Grid ID | Region | Approx. bounds | Cycles (UTC) |
|---|---|---|---|---|
| Antilles | ANTIL0025 | French Antilles, Caribbean | 22.45°N–10.4°N, 67.8°W–52.2°W | 00, 06, 12, 18 |
| French Guiana | GUYANE0025 | French Guiana, NE South America | 8.95°N–1.05°N, 56.75°W–46.3°W | 00, 06, 12, 18 |
| New Caledonia | NCALED0025 | SW Pacific | 13.75°S–26°S, 158.5°E–171.5°E | 00, 06, 12, 18 |
| French Polynesia | POLYN0025 | South Pacific | 12.6°S–25.25°S, 157.5°W–144.5°W | **00, 12 only** |
| Réunion–Mayotte | INDIEN0025 | SW Indian Ocean | 7.25°S–25.9°S, 32.75°E–67.6°E | 00, 06, 12, 18 |

Per-cycle data volumes differ substantially by domain: the Indian Ocean (Réunion–Mayotte) domain is by far the largest (its IP3 package is ~88 MB per timestep), while French Guiana is the smallest (~15 MB). The Antilles, New Caledonia, and Polynesia domains fall in between.

The native AROME grid is trapezoidal, not rectangular; GRIB files on the regular latitude–longitude grid contain **missing values** outside the effective domain, and these can differ between parameters. Météo-France recommends separating multi-parameter packages into single-parameter GRIB files before masking (some tools, e.g. CDO, cannot handle multi-parameter GRIB cleanly).

---

## Data availability

AROME-OM data is openly licensed under the Etalab Open Licence (version 2.0) and is available through the same distribution channels as the other Météo-France public NWP products. All channels carry GRIB2 files with `grid_ccsds` compression.

### 1. Météo-France public API (`public-api.meteofrance.fr`)
Bulk GRIB packages plus an OGC Web Coverage Service (WCS) for single-variable subsetting.

- **Bulk packages (pattern):**
  `https://public-api.meteofrance.fr/previnum/DPPaquetAROMOM/v1/models/AROMOM/grids/0.025/zones/{ZONE}/packages/{PACKAGE}/productAROOM?...`
  where `{ZONE}` is the domain token and `{PACKAGE}` is one of SP1–SP3, IP1–IP5, HP1–HP3 (see note 3 on the exact zone tokens)
- **Authentication:** Free API key required. Register at https://portail-api.meteofrance.fr
- **Documentation:** https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/

### 2. data.gouv.fr open-data portal (`object.data.gouv.fr`)
The same GRIB packages with no authentication required. Per-domain dataset landing pages:

- **Antilles:** https://meteo.data.gouv.fr/datasets/65bd162b9dc0d31edfabc2b9
- **French Guiana:** https://meteo.data.gouv.fr/datasets/65e0bd4b88e4fd88b989ba46
- **New Caledonia:** https://meteo.data.gouv.fr/datasets/65bd14cca6919e97e9699b09
- **French Polynesia:** https://meteo.data.gouv.fr/datasets/65bd1509cc112e6a1458ab95
- **Réunion–Mayotte:** https://meteo.data.gouv.fr/datasets/65bd1560c73941a5e0ec1891

### 3. Community AWS mirror (`mf-models-on-aws.s3.amazonaws.com`)
A community-run mirror, not affiliated with Météo-France, listed in the AWS Open Data Registry.

- **AWS Open Data Registry listing:** https://registry.opendata.aws/meteo-france-models/
- **Documentation:** https://mf-models-on-aws.org/en/doc/datasets/v1/
- **Authentication:** None
- **Caveat:** Operated independently and not subject to Météo-France's operational change-management process. Coverage of AROME-OM domains on the mirror should be verified against the mirror's own documentation, as not all Météo-France products are mirrored.

### Summary
- **Is the data free?** Yes (Etalab Open Licence v2.0)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (compressed, `grid_ccsds`)

---

## Companion ensemble system

**PEAROME-OM** (Prévision d'Ensemble AROME Outre-Mer) is the ensemble counterpart, operational since 21 February 2023. It comprises 16 members (1 control + 15 perturbed), perturbing initial, boundary, surface, and model conditions. Key differences from the deterministic AROME-OM:

- **Native resolution:** 2.5 km (vs. 1.3 km deterministic)
- **Vertical levels:** 90 (same as deterministic)
- **Lateral boundary conditions:** supplied by ARPEGE's ensemble **PEARP** (15 selected members couple the perturbed members; the control couples to deterministic ARPEGE), refreshed every 3 hours
- **Forecast length:** 48 hours, extended to **78 hours when a tropical cyclone is present** in the domain
- **Cycles (differ from deterministic):** Antilles and Guiana at 00/12 UTC; Réunion at 00/18 UTC; New Caledonia and Polynesia at 06/18 UTC

PEAROME-OM covers all five domains and is documented separately under ensemble models.

---

## Related systems
- **[AROME France](./arome-france.md)** — the metropolitan configuration. Differs from AROME-OM in that it runs its own 3DEnVar assimilation, has a longer forecast range (51 h), faster cadence (8× daily), finer native resolution, more output levels, and additional diagnostic fields.
- **[ARPEGE](../../global/france/arpege-global.md)** — provides AROME-OM's surface initial conditions (and, via PEARP, the ensemble's lateral boundary conditions).

---

## Notes
1. **New Caledonia domain bounds conflict between Météo-France sources.** The model-characteristics sheet (dated 30/10/2025) gives NCALED0025 as 13.75°S–26°S, 158.5°E–171.5°E, while the data-packages descriptif (dated 02/01/2024) gives a larger box, 10°S–30°S, 156°E–174°E. The table above uses the more recent characteristics-sheet figures. The other four domains' bounds agree across both documents. Worth verifying which is the current operational grid before relying on the New Caledonia bounds.
2. **Forecast length: 42 h vs 48 h.** The most recent sources (the 30/10/2025 characteristics sheet and the AROME-OM overview brochure) state a maximum range of 42 hours. The older data-packages descriptif (02/01/2024) states 48 hours, and the data.gouv.fr dataset descriptions also reference 48 h. This entry uses **42 h** as the current figure on the strength of the most recent documentation, but the discrepancy is unresolved — flagging for your judgment.
3. **API zone tokens unverified.** The exact `{ZONE}` strings in the API path (e.g. `ANTIL` vs `ANTILLES` vs `CARAIB`) are reconstructed by analogy with the AROME France pattern; the PDFs don't specify the API URL parameters. Verify against the live Confluence documentation before relying on these in code.
4. **Native resolution ambiguity.** The PEAROME-OM technical doc implies the deterministic model is 1.3 km native (it describes the ensemble as running at 2.5 km "instead of 1.3 km"). The AROME-OM overview brochure markets the resolution as 2.5 km — but uses the same brochure phrasing that quotes 1.25 km for metropolitan AROME, so it appears to be describing the public grid rather than the native resolution. This entry uses 1.3 km native; flagged as not fully certain.
5. **Domain alias (Antilles).** The Antilles domain appears as ANTIL0025, CARAIB0025, and CARAïBES across different Météo-France channels; all refer to the same output. The data.gouv.fr Antilles page also lists a broader bounding box (9.7°N–22.9°N, 75.3°W–51.7°W) than the operational grid; the operational grid (22.45°N–10.4°N, 67.8°W–52.2°W) is authoritative.
6. **Satellite brightness-temperature channels vary by domain and conflict across sources.** The data-packages descriptif lists SP3 channels as 62/104 (Antilles, Guiana, New Caledonia), 67/107 (Polynesia), and 62/108 (Réunion–Mayotte); the newer characteristics sheet lists 67/107 as default with 64/115 for INDIEN0025. Because the two official sources disagree, specific channel numbers are omitted from the package description above. Resolve from current documentation if this level of detail is needed.

---

## Official documentation
- AROME Outre-Mer overview (PDF, Météo-France): https://donneespubliques.meteofrance.fr/client/document/docaromeom_225.pdf
- AROME Outre-Mer model characteristics (PDF, Météo-France, 30/10/2025): linked from the Météo-France public data portal
- AROME Outre-Mer data-packages technical description (PDF, Météo-France, 02/01/2024): https://donneespubliques.meteofrance.fr/client/documentation/description-paquets-modele-aromeom.pdf
- PEAROME-OM technical description (PDF, Météo-France, 30/10/2025): linked from the Météo-France public data portal
- Météo-France public API documentation: https://portail-api.meteofrance.fr
- API Paquets Modèles overview (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- Météo-France public data portal: https://donneespubliques.meteofrance.fr
- AWS Open Data Registry — Atmospheric Models from Météo-France: https://registry.opendata.aws/meteo-france-models/
