# HARMONIE-AROME Ireland (Met Éireann – DINI)

## What this model is
HARMONIE-AROME Ireland is the **convection-permitting regional numerical weather prediction (NWP) model** distributed by Met Éireann for short-range forecasting over Ireland and surrounding waters.

Since 2024, Met Éireann no longer runs a separate Irish-domain HARMONIE production. Instead, Ireland's operational HARMONIE forecasts are produced as part of the **United Weather Centres-West (UWC-West)** partnership of DMI (Denmark), the Icelandic Met Office, KNMI (Netherlands), and Met Éireann, on the shared **DINI** (Denmark–Iceland–Netherlands–Ireland) domain. Met Éireann distributes its slice of the same DINI run via the Met Éireann Open Data Portal at [opendata.met.ie](https://opendata.met.ie/).

This entry covers the **deterministic** DINI configuration as distributed by Met Éireann. The companion ensemble system (DINI-EPS, formerly IREPS) is documented separately under regional ensemble models.

---

## Who runs it
- **Operating partnership:** United Weather Centres-West (UWC-West)
  - Met Éireann (Ireland)
  - DMI (Danish Meteorological Institute)
  - Icelandic Met Office (Veðurstofa Íslands)
  - KNMI (Royal Netherlands Meteorological Institute)
- **Public distributor (this dataset):** Met Éireann
- **Country / region:** Ireland (distribution); multi-national (operations)
- **Computing infrastructure:** UWC-West "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík

---

## What area it covers
- **Coverage:** Ireland and surrounding waters, on the larger DINI domain that extends from East Greenland to southern Italy
- **Domain name:** DINI (Denmark–Iceland–Netherlands–Ireland)
- **Domain details:** Met Éireann distributes its products on the shared DINI integration — the same model state DMI distributes as DINI and KNMI distributes as `harmonie_arome_cy43_p1` (Dutch cutout) and `harmonie_arome_cy43_p3` (European cutout)

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core, AROME physics, SURFEX surface scheme); cycle **43h**
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2 km; shallow convection parameterized)
- **Horizontal resolution:** ~2 km on the native Lambert conformal grid
- **Vertical levels:** 90 hybrid levels
- **Model top:** 10 hPa (TBD — standard HARMONIE-AROME configuration)
- **Forecast length:** Up to 60 hours
- **Update frequency / cycles:** Hourly on the supercomputer; Met Éireann publishes deterministic output to the Open Data Portal (TBD — confirm exact public publication cadence)
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes — the UWC-West DINI production runs hourly cycling assimilation using ECMWF's SAPP (Scalable Acquisition and Pre-Processing) for observation processing across the four partner services
- **Method / cadence:** Hourly DA cycle (TBD — confirm exact 3D-Var/4D-Var configuration and observation set)

---

## Initial and boundary conditions
- **Initial conditions:** UWC-West HARMONIE-AROME analysis on the DINI domain
- **Boundary conditions:** ECMWF IFS (HRES global model)

---

## What it provides
Deterministic forecasts of:
- temperature (2 m, model levels, pressure levels)
- wind (10 m, model levels, pressure levels)
- precipitation
- mean sea level and surface pressure
- humidity
- cloud fields (low/medium/high/total cloud cover)
- surface fluxes and radiation

Output is available at surface, near-surface, model, and pressure levels.

---

## Data availability
- **Is the data free?** Yes — a free user account is required (registration at https://opendata.met.ie/register)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0); attribution to Met Éireann required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://opendata.met.ie/

### How to access — Met Éireann Open Data Portal

The Met Éireann Open Data Portal provides two access tiers for HARMONIE NWP data:

- **Near real time:** the most recent three hours of NWP data with limited functionality
- **NWP Data Store:** the rolling NWP data archive dating back to October 2023

Account registration is free at https://opendata.met.ie/register.

### Alternative access via UWC-West partners

Because the DINI domain is a single shared UWC-West production, the same underlying model run is also available through:
- **DMI** (Danish Meteorological Institute) — DINI distribution at https://opendatadocs.dmi.govcloud.dk/ (see [HARMONIE (DMI)](../denmark/harmonie-dmi.md))
- **KNMI** — European cutout (P3) and Dutch cutout (P1) on the KNMI Data Platform (see [HARMONIE-AROME Europe / DINI (KNMI)](../netherlands/harmonie-knmi.md) and [HARMONIE-AROME Netherlands (KNMI)](../netherlands/harmonie-arome-netherlands.md))
- **Icelandic Met Office** — via its own services

These are not duplicate forecasts; they are repackagings of the same UWC-West model run, differing in distribution grid, retained variable set, GRIB encoding, and access mechanism.

---

## Notes
- **Met Éireann no longer runs a separate Irish-only HARMONIE domain.** Prior to UWC-West, Met Éireann operated its own HARMONIE-AROME configuration and the **IREPS** ensemble (1000 × 900 grid, cy40h1.1, 65 vertical levels, 2.5 km, 4× daily). Both have been superseded by the shared UWC-West DINI production. Documentation that still references "IREPS" or the 1000 × 900 / 65-level configuration refers to the legacy system.
- **DINI is the same model run distributed by other UWC-West partners.** The Met Éireann distribution, DMI's [DINI distribution](../denmark/harmonie-dmi.md), KNMI's [P3 European dataset](../netherlands/harmonie-knmi.md), and KNMI's [P1 Dutch dataset](../netherlands/harmonie-arome-netherlands.md) are all packaged independently from the same UWC-West cy43 model run on the Aurora supercomputer in Iceland. Underlying model state is identical.
- **DINI vs MEPS:** UWC-West (Ireland, Denmark, Iceland, Netherlands) and MetCoOp ([MEPS](../norway/meps.md): MET Norway, SMHI, FMI, ESTEA, LEGMC) are the two regional UWC clusters running parallel HARMONIE-AROME productions. The two clusters target a common UWC NWP production by ~2027.
- **Companion ensemble — DINI-EPS:** The UWC-West partnership operates a 1+30 member regional ensemble (DINI-EPS) on the same DINI domain with hourly updates and 60-hour forecasts. This replaces Met Éireann's earlier IREPS ensemble. If documented separately, DINI-EPS belongs under `ensemble_models/regional/` as a UWC-West joint production.
- **Relationship to siblings in the HARMONIE-AROME family:** The UWC-West HARMONIE production shares the same dynamical core and physics as other ACCORD-consortium HARMONIE-AROME and AROME deployments. See [MEPS](../norway/meps.md), [AROME-Arctic](../norway/arome-arctic.md), [AROME France](../france/arome-france.md), [AROME Hungary](../hungary/arome-hungary.md), and other family members.
- **GRIB edition:** All Met Éireann HARMONIE-AROME output is GRIB2.

---

## Recent version history

### September 2024 — UWC-West Aurora supercomputer becomes operational
The UWC-West collaboration entered its operational phase with the cy43 HARMONIE-AROME production running on the new HPE Cray "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík. Common UWC-West NWP models had been running operationally since March 2024. This consolidated Met Éireann's HARMONIE production with DMI, IMO, and KNMI onto the same hardware and the same model run, replacing Met Éireann's legacy Irish-only HARMONIE/IREPS chain.

### Pre-2024 — IREPS (legacy)
The Irish Regional Ensemble Prediction System (IREPS) became operational on 15 October 2018 as Met Éireann's first ensemble system, using HARMONIE-AROME cy40h1.1 with 1 control + 10 perturbed members on a 1000 × 900 / 2.5 km / 65-level grid. It was upgraded on 15 April 2020 to a 54-hour, 11-member ensemble running 4× daily (00/06/12/18 UTC) using combined ECMWF and KNMI HPC resources. IREPS was retired with the transition to the UWC-West DINI production in 2024.

---

## Official documentation
- Met Éireann Open Data Portal: https://opendata.met.ie/
- Met Éireann Open Data documentation: https://opendata.met.ie/documentation
- Met Éireann Open Data about: https://opendata.met.ie/about
- Met Éireann Open Data registration: https://opendata.met.ie/register
- Met Éireann operational NWP overview: https://www.met.ie/science/numerical-weather-prediction/operational-nwp-in-met-eireann
- UWC-West operational announcement (Met Éireann): https://www.met.ie/new-met-eireann-weather-and-climate-supercomputer-becomes-operational-in-unique-collaboration-with-three-other-national-meteorological-services
- Bengtsson et al. (2017), *The HARMONIE–AROME Model Configuration in the ALADIN–HIRLAM NWP System*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
