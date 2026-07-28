# C3S Seasonal Multi-System

## What this model is
The C3S seasonal forecast service is a multi-system seasonal prediction service operated by the Copernicus Climate Change Service, implemented by ECMWF on behalf of the EU. It collects real-time forecasts and hindcasts from nine independently operated, fully coupled seasonal prediction systems, regrids them to a common 1° grid, and publishes them through the Copernicus Climate Data Store (CDS) as raw ensemble fields, monthly statistics, and bias-adjusted (anomaly) products. Forecasts extend roughly 6–7 months ahead. It succeeded EUROSIP, which was retired in October 2019.

Note that this is an *operational seasonal prediction* service, not a climate-projection product, despite the "Climate Change Service" branding — see Notes on scope.

---

## Who runs it
- **Organization:** Copernicus Climate Change Service (C3S), implemented by ECMWF on behalf of the European Union. Data archived in ECMWF's MARS and served via the CDS.
- **Country / region:** European Union (multi-national, with non-European in-kind contributors)
- **Coordinating body / programme:** C3S / Copernicus. Contributing centres: ECMWF, UK Met Office, Météo-France, DWD, CMCC, NCEP, JMA, ECCC, and BoM.

---

## What area it covers
- **Coverage:** Global
- **Domain details:** All systems delivered on a common 1° × 1° regular lat-lon grid, with grid points centred at half-degree latitude/longitude (180 latitude values, −89.5 to 89.5). Native model resolutions are considerably finer and vary by system (see Coupled configuration).

---

## Basic details
- **Model type:** Multi-system ensemble (umbrella) of fully coupled seasonal prediction systems — raw ensemble fields, monthly statistics, and bias-adjusted probabilistic products
- **Model system / core:** Varies by member (see Multi-model composition)
- **Range class:** Seasonal
- **Forecast length:** ~6–7 months, varying by system (CMCC 184 days; ECCC 214; ECMWF, UKMO, NCEP and JMA 215; BoM 217; DWD 6 calendar months; Météo-France 7 calendar months)
- **Initialization cadence:** Monthly nominal start dates. Several systems initialize in lagged mode (daily or across the preceding month) but are encoded to a common nominal start date — see Notes.
- **Ensemble generation:** Mixed — burst ensembles (ECMWF, DWD, CMCC), lagged/daily-initialized ensembles (UKMO, NCEP, JMA, BoM), and dual-component lagged systems (ECCC, Météo-France)
- **Ensemble size:** No single fixed total; per-system real-time sizes are ECMWF 51; UKMO 2 members/day (most recent 50 used for C3S products); Météo-France 25 members each from two lagged starts plus 1; DWD 50; CMCC 50; NCEP 4 members/day (52 used); JMA 5 members/day (55 used); ECCC 10 members each from two starts, per component system; BoM 11 members each. The CDS serves the complete member set even where C3S products use a subset.
- **Temporal output resolution:** Sub-daily (6-hourly) and daily raw fields; monthly aggregates
- **Output aggregation levels:** Original time resolution; monthly statistics; bias-adjusted/post-processed products (anomalies)

---

## Coupled configuration
Each member is an independently developed, fully coupled system. There is no common formulation — only the common 1° delivered grid. Native configurations (status April 2026):

| Centre | System | Atmosphere | Ocean |
|---|---|---|---|
| ECMWF | SEAS5 | TCO319/L91 (~36 km), 91 levels to 0.01 hPa | 0.25° ORCA, 75 levels |
| UK Met Office | GloSea6-GC5.1 | N216/L85 (~60 km mid-lat), 85 levels to 85 km | 0.25° ORCA, 75 levels |
| Météo-France | System 9 | TL359/L137 (0.5°), 137 levels to 0.01 hPa | 0.25° ORCA, 75 levels |
| DWD | GCFS2.2 | T127 (~100 km), 95 levels to 0.01 hPa | 0.4° TP04, 40 levels |
| CMCC | SPS4 (CMCC-CM3) | ~0.5° lat-lon, 83 levels to 0.01 hPa | 0.25° ORCA, 75 levels |
| NCEP | CFSv2 | T126/L64 (~1°), 64 levels to 0.02 hPa | 0.25°–0.5°, 40 levels |
| JMA | CPS4 | TL319 (~55 km), 128 levels to 0.01 hPa | 0.25° tripolar, 60 levels |
| ECCC | CanESM5.1p1bc | T63 (~2.8°), 49 levels to 1 hPa | 1° ORCA, 45 levels |
| ECCC | GEM5.2-NEMO | ~1.1° (~110 km), 85 levels to 0.1 hPa | 1° ORCA, 50 levels |
| BoM | ACCESS-S2 | N216 (~60 km mid-lat), 85 levels to 85 km | 0.25° ORCA, 75 levels |

---

## Initialization
Initialization varies by member: each centre initializes its own system from its own operational analyses and/or reanalyses. There is no shared C3S initialization. Per-system detail is documented in the individual C3S contribution pages linked from the Knowledge Base.

---

## Hindcasts (reforecasts)
Hindcasts are essential to this dataset — the bias-adjusted products depend on them, and the CDS `system` keyword must match between a real-time forecast and its hindcast.

- **Hindcast periods (vary by system):** ECMWF 1981–2016; UK Met Office 1993–2016; Météo-France 1993–2018; DWD 1993–2023; CMCC 1993–2022; NCEP 1993–2016; JMA 1993–2020; ECCC 1980–2023 (no hindcast data available for start dates before 1993); BoM 1993–2018.
- **Hindcast ensemble sizes:** ECMWF 25; UKMO 7 members per start (1st, 9th, 17th, 25th); Météo-France 15 each plus 1; DWD 30; CMCC 30; NCEP 4 per start date (every 5 days); JMA 5 per start (2 lagged start dates); ECCC 10 each per component; BoM 3 each.
- **Production schedule:** Most systems produce hindcasts as a fixed dataset. The UK Met Office produces them **on-the-fly**, generated close to the matching real-time forecast — which is why its `system` values increment annually (600–605, then 610 for GloSea6-GC5.1 from April 2026).
- **Distributed alongside forecasts?** Yes — via the same CDS datasets, selected by product type.

---

## Sources of predictability
ENSO is the dominant seasonal driver, with coupled ocean heat content, sea ice, land-surface memory, and stratospheric state contributing. The multi-system design is intended to sample model uncertainty better than any single system.

---

## Multi-model composition
- **Contributing centres and current systems (from April 2026), with the CDS `system` keyword:** ECMWF SEAS5 (`51`); Météo-France System 9 (`9`); UK Met Office GloSea6-GC5.1 (`610`); DWD GCFS2.2 (`22`); CMCC SPS4 (`4`); NCEP CFSv2 (`2`); JMA CPS4 (`4`); ECCC CanESM5.1p1bc (`4`) and GEM5.2-NEMO (`5`); BoM ACCESS-S2 (`2`).
- **Combination method:** Multi-system combination products are published alongside the individual system forecasts; all members are regridded to the common 1° grid.
- **Common delivered grid:** 1° × 1°.
- **ECCC structure:** ECCC's contribution is CanSIPSv3.0, supplied as **two independent forecasting systems** (CanESM5.1p1bc and GEM5.2-NEMO) rather than as a single combined system — the same split seen in NMME.
- **Per-contributor terms:** Contributions from NCEP, JMA, ECCC, and BoM are in-kind from non-European centres and are covered by an additional licence condition (see Data availability).
- **Historical / retired systems:** ECMWF System 4; Météo-France Systems 5–8; Met Office GloSea5-GC2 and GC2-LI and earlier GloSea6 issues; DWD GCFS2.0/2.1; CMCC SPS3/SPS3.5; JMA CPS2/CPS3; ECCC CanCM4i, GEM-NEMO, GEM5-NEMO.

---

## What it provides
CDS catalogue entries are organized by variable type (single-level vs. pressure-level) and by level of post-processing (original time resolution, monthly aggregation, bias-adjusted), plus a separate ocean dataset. Confirmed entries include:
- `seasonal-original-single-levels` — daily and sub-daily (6-hourly) single-level fields
- `seasonal-monthly-single-levels` — monthly statistics on single levels
- `seasonal-monthly-ocean` — monthly ocean variables (available from March 2023; archived in netCDF)
- Corresponding pressure-level and post-processed (anomaly) entries

*(Flag: the full CDS dataset list could not be enumerated directly — the catalogue requires authentication — so treat the live CDS catalogue as authoritative for exact dataset identifiers.)*

Viewer-only siblings (out of scope): the graphical seasonal forecast products on the C3S website.

---

## Data availability
- **Is the data free?** Yes — free of charge, but access requires a free ECMWF/CDS account and per-dataset licence acceptance.
- **License:** Copernicus licence, plus an *additional licence to use non-European contributions* covering the in-kind contributions from NCEP, JMA, ECCC, and BoM. C3S states that all data published in the CDS as part of the seasonal forecast multi-system may be used and redistributed without restrictions; citation and acknowledgement requirements are given on the CDS alongside the licence.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB (native; all data held in ECMWF's MARS archive in GRIB). NetCDF is offered as a conversion but is documented as experimental and not recommended, except for the ocean variables in `seasonal-monthly-ocean`, which are archived in netCDF.
- **Official download location:**  
  - CDS catalogue: https://cds.climate.copernicus.eu/datasets/seasonal-original-single-levels
  - CDS API endpoint: `https://cds.climate.copernicus.eu/api` (via the `cdsapi` Python client, with a personal access token in `~/.cdsapirc`)
- **Access route notes:** No anonymous bulk directory or FTP mirror exists — retrieval is exclusively via the CDS web forms or the CDS API. Terms and conditions must be accepted per dataset before download. Retrievals require the `system` keyword; matching a real-time forecast to the correct hindcast `system` value is essential for computing anomalies correctly.

---

## Release schedule
- **ECMWF:** 6th of each month at 12 UTC
- **All other forecast systems:** 10th of each month at 12 UTC

---

## Notes
- **Scope note — registration gate.** Access requires a free account and per-dataset licence acceptance. This is self-service registration, not an approval gate, and the licence permits unrestricted redistribution. **This requires confirming that self-registration is treated as in-scope, and revisiting the `COPERNICUS.md` exclusion wording**, which currently excludes "Copernicus Climate… services" — language aimed at reanalysis/CAMS but which as written would collide with this forecast product.
- **Nominal start dates vs. real initialization.** Several systems (Météo-France, ECCC) initialize members in lagged mode but encode them in the CDS as if all were initialized on the 1st of the month. UKMO, NCEP, JMA, and BoM build a nominal month from members initialized across the preceding month. The real initialization times are not always exposed.
- **`system` keyword is load-bearing.** It identifies the forecasting system version and differs in meaning between fixed-hindcast systems (mapped to the centre's version numbering) and on-the-fly-hindcast systems (UK Met Office, three-digit incrementing scheme).
- **ECMWF SEAS5 `system=5` is deprecated.** From November 2022 only `system=51` real-time data is served; `system=5` hindcasts should not be used alongside it (different 1° interpolation and grid definition).
- **Member overlap.** NCEP CFSv2 has its own entry; ECCC's contribution is CanSIPS, also with its own entry. This entry cross-references rather than re-documenting.
- **Succeeded EUROSIP**, retired October 2019. IMME/EuroSIP as displayed elsewhere is a separate, non-open activity (a *Systems Not in the Catalog* candidate).
- **Relationship to ECMWF SEAS5 standalone.** SEAS5 is openly licensed as part of ECMWF's real-time catalogue, but the practical open route for its seasonal data is this CDS channel rather than the free `data.ecmwf.int` Open Data subset.

---

## Recent version history
- **April 2026:** Met Office GloSea6-GC5.1 (`system=610`) replaces GloSea6 (`605`).
- **January/February 2026:** JMA CPS4 (`system=4`) introduced.
- **August 2025:** CMCC SPS4 (`system=4`) replaces SPS3.5.
- **May 2025:** BoM joins with ACCESS-S2; Météo-France System 9 replaces System 8.
- **April 2025:** DWD GCFS2.2 (`system=22`) replaces GCFS2.1.
- **August 2024:** ECCC upgrades to CanSIPSv3.0 components (`system=4` and `5`).
- **March 2023:** Ocean variables dataset added.
- **November 2022:** ECMWF SEAS5 re-versioned as `system=51`.
- **October 2019:** EUROSIP retired; C3S multi-system takes over.
- **September 2017:** First real-time forecasts (ECMWF, Météo-France, UK Met Office).

---

## Official documentation
- C3S seasonal forecasts overview: https://climate.copernicus.eu/seasonal-forecasts
- ECMWF dataset page: https://www.ecmwf.int/en/forecasts/dataset/c3s-seasonal-forecasts
- Knowledge Base (dataset documentation root): https://confluence.ecmwf.int/display/CKB/C3S+Seasonal+Forecasts
- Description of the C3S seasonal multi-system (per-system specs, `system` keyword): https://confluence.ecmwf.int/display/CKB/Description+of+the+C3S+seasonal+multi-system
- Summary of available data (release dates, start dates, roster history): https://confluence.ecmwf.int/display/CKB/Summary+of+available+data
- Detailed list of parameters: https://confluence.ecmwf.int/display/CKB/Detailed+list+of+parameters
- Recommendations and efficiency tips: https://confluence.ecmwf.int/display/CKB/Recommendations+and+efficiency+tips+for+C3S+seasonal+forecast+datasets
- CDS API client: https://github.com/ecmwf/cdsapi
