# RDWPS (Regional Deterministic Wave Prediction System)

## What this model is
The Regional Deterministic Wave Prediction System (RDWPS) is Canada's operational regional wave forecasting system, producing high-resolution deterministic wave forecasts out to 48 hours for the Great Lakes and Canada's Atlantic and Pacific coastal and offshore waters.

RDWPS is built on the WAVEWATCH III® spectral wave model, forced by 10 m winds from the [High Resolution Deterministic Prediction System (HRDPS)](../../../nwp_models/regional/canada/hrdps.md) and nested inside the global [GDWPS](../../global/canada/gdwps-canada.md), which supplies wave boundary conditions to the ocean domains. It complements GDWPS by resolving nearshore and inland-sea wave processes important for marine operations, coastal forecasting, and navigation.

The current operational version is **RDWPS 4.3.0** (an HPC-infrastructure port of the scientific configuration 4.2.0), implemented 14 April 2026.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Canadian Centre for Meteorological and Environmental Prediction (CCMEP), Environment and Climate Change Canada (ECCC)
- **Country / region:** Canada

---

## What area it covers
RDWPS runs **six computational domains** but distributes them as **five GRIB2 products**: the four Great Lakes as individual ~1 km lat-lon grids, plus a single **national 2.5 km rotated-pole composite** that mosaics *all* domains (the two ocean domains are distributed **only** through this composite — there are no standalone ocean-grid GRIB files).

**Computational domains (model grids, from the tech spec):**
- **Lake Superior:** 46.26°–49.11°N, 267.69°–275.84°E — structured lat-lon, ~1 km
- **Lake Huron-Michigan:** 41.43°–46.57°N, 271.85°–280.50°E — structured lat-lon, ~1 km
- **Lake Erie (incl. Lake St. Clair):** 41.22°–43.10°N, 276.39°–281.32°E — structured lat-lon, ~1 km
- **Lake Ontario:** 43.06°–44.48°N, 280.03°–284.33°E — structured lat-lon, ~1 km
- **Northeast Pacific:** 39.6°–60.5°N, 214.7°–237.8°E — **unstructured mesh, 5 km offshore to 1 km nearshore**
- **Northwest Atlantic:** 27.3°–69.3°N, 255.7°–319.2°E — **rotated structured grid, 5 km**

**Distributed products (live-verified on the 2026-07-24 00 UTC run):**

| Product | Grid type | ni × nj | Resolution | First grid point |
| --- | --- | --- | --- | --- |
| Lake Superior | regular lat-lon | 658 × 318 | 0.0124° lon × 0.0090° lat | 46.2590°N 92.3116°W |
| Lake Huron-Michigan | regular lat-lon | 698 × 573 | 0.0124° lon × 0.0090° lat | 41.4260°N 88.1452°W |
| Lake Erie | regular lat-lon | 398 × 210 | 0.0124° lon × 0.0090° lat | 41.2190°N 83.6068°W |
| Lake Ontario | regular lat-lon | 348 × 158 | 0.0124° lon × 0.0090° lat | 43.0640°N 79.9736°W |
| National (composite) | **rotated lat-lon** | 2536 × 1286 | 0.0225° | 39.681°N 226.410°W |

The national grid's rotation places the southern pole at 36.089°S, 245.305°E; unrotated it spans ~27.3°–70.6°N and ~207°–319°E, covering both ocean domains and the lakes.

---

## Basic details
- **Model type:** Deterministic regional wave model
- **Grid system:** Six WW3 computational grids (structured lat-lon for the lakes, unstructured mesh for NE Pacific, rotated structured for NW Atlantic); distributed as four ~1 km lake grids plus one 2.5 km rotated national composite
- **Core wave model:** WAVEWATCH III® (WW3) version 7.0
- **Spectral resolution:**
  - Great Lakes: 36 directional bins (10°) × 40 frequency bins (0.050–1.0058 Hz, ×1.08)
  - NE Pacific & NW Atlantic: 36 directional bins (10°) × 36 frequency bins (0.035–0.984 Hz, ×1.10)
- **Horizontal resolution:** ~1 km (Great Lakes); 5 km→1 km unstructured (NE Pacific); 5 km rotated (NW Atlantic); 2.5 km national composite on distribution
- **Forecast length:** 48 hours
- **Update frequency / cycles:** Forecast 4× daily (00, 06, 12, 18 UTC); pseudo-analysis 4× daily (6-hour windows centred at 00, 06, 12, 18 UTC), used only for initialization
- **Temporal output resolution:** Hourly throughout (000–048 h; 49 time steps, live-verified)

---

## Forcing and nesting
- **Wind forcing:** HRDPS 10 m winds (`UU`, `VV`), version 7.0.0
  - Pseudo-analysis: HRDPS **N2** (delayed-cutoff) run, hourly
  - Forecast: HRDPS **N1** run, every 30 minutes for the first 24 h, then hourly
- **Ice forcing:** ice concentration (`GL`), hourly
  - Great Lakes: WCPS (Water Cycle Prediction System) version 3.3.0 — attenuates wave growth where ice is 25–75%, suppresses above 75%
  - NE Pacific & NW Atlantic: [RIOPS](../../../ocean_models/regional/canada/riops.md) version 2.4.0 — NW Atlantic uses the same 25–75%/>75% logic as the lakes; **NE Pacific lets waves propagate freely below 50% ice and blocks propagation at or above 50%**
- **Current forcing:** None
- **Nested inside:** [GDWPS](../../global/canada/gdwps-canada.md) supplies lateral wave boundary conditions to the NE Pacific and NW Atlantic domains — pseudo-analysis uses same-hour GDWPS pseudo-analysis BCs; the 00/12 UTC forecasts use 12-h-old GDWPS forecast BCs, the 06/18 UTC forecasts use 6-h-old BCs. The Great Lakes domains are closed basins and take no wave BCs.

---

## Data assimilation
- **Assimilates wave observations:** No. RDWPS has no wave data assimilation system.
- **Initialization:** A continuously cycled pseudo-analysis run with delayed-cutoff HRDPS N2 winds and ice in 6-hour windows centred on 00, 06, 12, 18 UTC, each restarting from the previous window's restart file. Each forecast starts from the pseudo-analysis three hours before its run time (e.g. the 00 UTC forecast from the previous day's 18 UTC pseudo-analysis), with HRDPS N1 IAU data bridging the gap.

---

## What it provides
Deterministic regional wave forecasts (surface, GRIB2, one file per variable per time step). Every distributed product — the four lakes and the national composite — carries the same 19-field set (live-verified):

- **Combined sea state:** significant height of combined wind waves and swell (`HTSGW`)
- **Wind-sea partition:** significant height (`WVHGT`), direction (`WVDIR`), peak period (`PPERWW`)
- **Swell partitions (first and second):** significant height (`SWHFSWEL`, `SWHSSWEL`), mean direction (`MWDFSWEL`, `MWDSSWEL`), peak period (`PWPFSWEL`, `PWPSSWEL`)
- **Total-spectrum period / direction:** peak wave period (`PWPER`), mean zero-crossing period (`MZWPER`), mean wave direction (`WWSDIR`), peak wave direction (`PWAVEDIR`)
- **Surface Stokes drift:** u- and v-components (`USSD`, `VSSD`)
- **10 m wind (the driving HRDPS wind, at AGL-10m):** u- and v-components (`UGRD`, `VGRD`)
- **Sea ice:** ice concentration (`ICEC`)

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTP)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, **commercial use permitted**, attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  - Lake Superior: https://dd.weather.gc.ca/today/model_rdwps/superior/1km/{HH}/
  - Lake Huron-Michigan: https://dd.weather.gc.ca/today/model_rdwps/huron-michigan/1km/{HH}/
  - Lake Erie: https://dd.weather.gc.ca/today/model_rdwps/erie/1km/{HH}/
  - Lake Ontario: https://dd.weather.gc.ca/today/model_rdwps/ontario/1km/{HH}/
  - National (rotated composite): https://dd.weather.gc.ca/today/model_rdwps/national/2.5km/{HH}/
  - **Filename convention (lakes):** `{YYYYMMDD}T{HH}Z_MSC_RDWPS-Lake-{Name}_{VAR}_{LVL}_LatLon0.009x0.012_PT{hhh}H.grib2` (e.g. `..._MSC_RDWPS-Lake-Erie_HTSGW_Sfc_LatLon0.009x0.012_PT003H.grib2`)
  - **Filename convention (national):** `{YYYYMMDD}T{HH}Z_MSC_RDWPS_{VAR}_{LVL}_RLatLon0.0225_PT{hhh}H.grib2` (no domain token for the national grid)
  - Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps-datamart_en/

---

## Notes
- **Distribution grid ≠ model grid.** The NE Pacific (unstructured) and NW Atlantic (rotated 5 km) domains are never distributed in native form — they are only accessible folded into the interpolated 2.5 km national composite. The Great Lakes are distributed both as native ~1 km grids and within the composite.
- **The national domain is explicitly a post-processed composite** of all RDWPS domains, per ECCC's datamart documentation.
- **Doc-vs-reality:** the datamart filename-nomenclature page lists runs as `[00, 12]` and forecast hours ending at 048; the tech spec and data-location page both give four daily runs (00/06/12/18). Live verification on 2026-07-24 showed 00/06/12 present with 18 UTC pending at check time, and hourly output 000–048.
- **Model physics:** input/dissipation source terms ST4 (Ardhuin et al. 2010); DIA nonlinear interactions; JONSWAP bottom friction; Battjes–Janssen depth-induced breaking; third-order QUICKEST propagation with ULTIMATE TVD limiter. NE Pacific additionally uses Lumped Triad Interaction (LTA); the other domains use no triad interactions. Bathymetry: NGDC Great Lakes grids for the lakes; ETOPO1 plus CHS NONNA for the ocean domains. Coastlines from GSHHS.
- **Relationship to other ECCC systems:**
  - Parent global system: [GDWPS](../../global/canada/gdwps-canada.md) (wave boundary conditions for the ocean domains).
  - Meteorological driver: [HRDPS](../../../nwp_models/regional/canada/hrdps.md) 7.0.0 (10 m winds).
  - Ice drivers: WCPS 3.3.0 (lakes) *(entry pending)*, [RIOPS](../../../ocean_models/regional/canada/riops.md) 2.4.0 (oceans).
  - Ensemble counterpart: [REWPS](./rewps-canada.md) (Regional Ensemble Wave Prediction System) — the ensemble sibling, covering the Great Lakes only (no ocean domains).
  - RDWPS replaced WAM Regional (Gulf of St. Lawrence) when the ocean domains were added in v4.0.0.
- **U.S. counterpart (Great Lakes):** NOAA/NCEP's [GLWU](../usa/glwu.md) is the equivalent deterministic Great Lakes wave system on the U.S. side. It covers the same lakes (plus Lake Champlain) but on an unstructured mesh (~2.5 km → 250 m) rather than RDWPS's structured ~1 km per-lake grids, cycles hourly (24× daily vs 4×), and extends to 149 h at four daily long-range cycles (01/07/13/19 UTC). The two are independent operational systems, not a shared model.

---

## Recent version history

Versions are implemented at the stated date's 12 UTC run unless noted.

### RDWPS 4.3.0 — operational 14 April 2026 (current)
Adaptation to ECCC's new High Performance Computing infrastructure. Infrastructure-only; no scientific or configuration changes to the model, forcing, or outputs. The v4.2.0 technical specification still describes the current scientific configuration.

### RDWPS 4.2.0 — operational 11 June 2024
Wind forcing moved to HRDPS 7.0.0; ice forcing to WCPS 3.3.0 and RIOPS 2.4.0; corrected wind-to-grid interpolation on the ocean domains; activated a Miche-style shallow-water limiter on the Great Lakes.

### RDWPS 4.1.0 — operational 28 June 2022
HPC-infrastructure adaptation.

### RDWPS 4.0.0 — operational 1 December 2021
Major upgrade: HRDPS 6.0.0 forcing; **added the pseudo-analysis cycle**; **added the Northeast Pacific and Northwest Atlantic ocean domains**; retired WAM Regional for the Gulf of St. Lawrence.

### RDWPS 3.4.0 — operational 21 January 2020
HPC-infrastructure adaptation.

### RDWPS 3.2.0 — operational 4 March 2019
Ice input switched from an analysis to a WCPS (v2.0.0) ice forecast.

### RDWPS 3.0.0 — declared operational 4 April 2018
New WW3-based RDWPS declared operational.

### RDWPS 2.0 — 7 May 2012
Earlier-generation regional wave system.

---

## Official documentation
- RDWPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps_en/
- RDWPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps-datamart_en/
- Technical specifications (current, describes v4.2.0 config): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RDWPS_e.pdf
- Technical specifications (v4.2.0, version-pinned): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RDWPS_4.2.0_e.pdf
- Technical note (v4.2.0): https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_rdwps-420_e.pdf
- Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/fact_sheets/factsheet_rdwps_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_RDWPS_en.svg
- RDWPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/changelog_rdwps_en/
- Open Government Portal metadata (National): https://open.canada.ca/data/en/dataset/9a6594f9-ad0e-4421-ba9d-16338e5a9cbe
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
