# HARMONIE-AROME North Sea Forcing / DINI native grid (KNMI – Cy43 L20)

## What this model is
This is a **deterministic** distribution of the UWC-West HARMONIE-AROME Cy43 DINI run, produced specifically to **force storm-surge and wave models** over the North Sea. Unlike every other KNMI HARMONIE product — which are bi-linearly regridded to lat-lon — this dataset (`harmonie_arome_cy43_l20`) is delivered on the **native Lambert conformal model grid**, carrying the air–sea exchange fields (surface pressure, wind, fluxes, precipitation, radiation) that ocean and coastal models need as atmospheric boundary conditions.

It is the only KNMI HARMONIE product on the raw model grid, and — while it is an atmospheric NWP dataset, not a wave or surge model itself — it is the atmospheric *forcing* input those Dutch marine models are driven by.

---

## Who runs it
- **Operating partnership:** United Weather Centres-West (UWC-West) — KNMI, Icelandic Met Office, DMI (Denmark), Met Éireann (Ireland)
- **Public distributor (this dataset):** KNMI
- **Country / region:** Netherlands (distribution); multi-national (operations)

---

## What area it covers
- **Coverage:** The full DINI (Denmark–Iceland–Netherlands–Ireland) domain; intended for use over the North Sea and the large Dutch lakes (IJsselmeer/Markermeer)
- **Geographic bounding box (KNMI metadata):** 62.6°N / 38.75°S-bound / 25.0°W / 16.0°E
- **Grid (verified from GRIB):** **native Lambert conformal**, **1909 × 1609** points, first grid point at (−25.447°, 39.639°). This is the raw ~2 km model grid — *not* the rotated/regular lat-lon grids used by the P-series.

---

## Basic details
- **Model type:** Regional deterministic NWP (surface/forcing distribution)
- **Model system / core:** HARMONIE-AROME (Cycle 43), UWC-West configuration
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (native ~2 km)
- **Horizontal resolution / grid:** ~2 km on the **native Lambert conformal** grid (1909 × 1609) — no interpolation applied (contrast the P-series, which are bi-linearly regridded)
- **Vertical structure:** Surface and near-surface forcing fields (see *What it provides*); not a full-column product
- **Forecast length:** 0–60 h (verified: 61 hourly control steps per run)
- **Ensemble:** No — **deterministic control only** (verified: every member tagged `CONTROL`)
- **Update frequency / cycles:** Hourly
- **Temporal output resolution:** 1 hour

---

## What it provides
Surface and air–sea **forcing fields** for driving storm-surge and wave models (per the dataset abstract):
- Mean sea-level pressure
- 2 m temperature and dew point
- 10 m wind
- Total precipitation
- Total cloud cover
- Land/sea mask
- Long-wave and short-wave radiation
- Momentum flux from sea and lakes

These are exactly the atmospheric boundary conditions a coastal hydrodynamic or spectral wave model requires. (Note: the GRIB messages use a KNMI-local parameter table that standard eccodes does not fully resolve to human-readable names; the field list above is from KNMI's dataset description.)

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 1, delivered as one `.tar` archive per run (~6 GB), containing 61 hourly control GRIB files named `fc{YYYYMMDDHH}+{HHH}CONTROL_GB_UWCW01_L20`.
- **Data retention:** Standard KNMI rolling window (most recent runs; not precisely verified here). For historical archives, contact `licentiebureau@knmi.nl`.
- **Official download location:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-l20-1-0
  - API dataset `harmonie_arome_cy43_l20`, version `1.0`, via the KNMI Open Data API (key in `Authorization` header; access identical to the other KNMI HARMONIE datasets).

---

## Notes
- **Native grid is the distinguishing feature.** Every other KNMI HARMONIE product (P1, P3/P5, BES, P2/P4 EPS) is bi-linearly regridded to a lat-lon grid; L20 alone ships the raw Lambert conformal model grid, making it the choice for users who want native-resolution fields or who are coupling to ocean/coastal models expecting the model's own grid.
- **Forcing dataset, not a marine model.** KNMI does not distribute an open operational wave or storm-surge *model* (Dutch operational surge/wave modelling is run by Rijkswaterstaat/Deltares). L20 is the atmospheric forcing those models consume — worth cross-referencing from any wave/surge context in the catalog.
- **Same run as the P-series.** L20 is the deterministic DINI run also distributed (regridded, with different variable/level sets) as the [P3/P5 European datasets](./harmonie-knmi.md); it differs in grid (native Lambert) and variable focus (air–sea forcing).
- **Product code.** "L20"/"UWCW01" are KNMI's internal product identifiers; the metadata does not define what "L20" denotes.

---

## Official documentation
- KNMI Data Platform — dataset page: https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-l20-1-0
- KNMI Open Data API documentation: https://developer.dataplatform.knmi.nl/open-data-api
- KNMI HARMONIE overview and parameter tables: https://english.knmidata.nl/open-data/harmonie
