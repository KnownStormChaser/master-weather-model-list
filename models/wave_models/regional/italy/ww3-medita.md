# WW3-MEDITA (Mediterranean WAVEWATCH III)

## What this model is
WW3-MEDITA is an operational regional wave forecasting system covering the entire Mediterranean basin, operated by Agenzia ItaliaMeteo and Arpae Emilia-Romagna.

It is based on the third-generation spectral wave model **WAVEWATCH III (WW3)** and uses an **unstructured calculation grid with variable resolution** — approximately 18 km in the open sea, refining down to **150–200 m along the Italian coast**. The unstructured grid allows a gradual, continuous transition between low- and high-resolution regions without the discontinuities that nested structured grids introduce at domain boundaries.

WW3-MEDITA became operational in October 2025, replacing the previous SWAN-MEDITARE system.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo and Arpae Emilia-Romagna (operational forecasting chain), with implementation in collaboration with the Italian Department of Civil Protection
- **Country / region:** Italy

---

## What area it covers
- **Coverage:** Entire Mediterranean basin, with a focus on Italian coastal waters
- **Grid type:** Unstructured (variable-resolution triangular mesh)
- **Resolution range:** ~18 km in the open sea down to ~150–200 m along the Italian coast

---

## Basic details
- **Model type:** Regional deterministic wave model (unstructured grid)
- **Core wave model:** WAVEWATCH III (WW3)
- **Horizontal resolution:** Variable — ~18 km (open sea) to ~150–200 m (Italian coast)
- **Forecast length:** 72 hours
- **Update frequency:** 1× daily
- **Production cycle:** 00 UTC
- **Temporal output resolution:** 1-hourly

---

## Forcing
- **Atmospheric forcing (10 m wind):** Spatial blending of two atmospheric models:
  - **ICON-2I** (~2.2 km resolution) over the Italian domain
  - **ECMWF-IFS** (~9 km resolution) over the rest of the Mediterranean
  
  The blending is designed so that the highest-resolution wind forcing coincides with the area where the wave model resolution itself is highest (Italian coastal waters).

- **Lateral boundary conditions:** None required — the Mediterranean basin is essentially closed at this domain extent, with the western edge near Gibraltar treated within the model grid.

---

## What it provides
The MeteoHub distribution provides the following wave parameters:

- Significant wave height (`hs`)
- Mean wave direction (`dir`)
- Mean wave period T01 (`t01`)
- Wave peak frequency (`fp`)
- Eastward and northward Stokes drift (`uuss`, `vuss`)
- Radiation stress components (`sxx`, `sxy`, `syy`)
- RMS bottom orbital velocity, zonal and meridional (`uubr`, `vubr`)
- Wave dissipation in the bottom boundary layer (`fbb`)

Grid metadata files are also distributed:
- `latitude`, `longitude` — node coordinates of the unstructured mesh
- `tri` — triangulation connectivities (the mesh topology)
- `mapsta` — status map for the grid

The MeteoHub map viewer (separate from the raw data download) displays significant wave height and wave propagation direction, with selectable views over either the full Mediterranean or the Italian sub-domain.

Note that the WW3-MEDITA distribution does **not** include partitioned swell fields (primary/secondary swell or wind-wave partitions), peak period, or maximum crest/trough variables — a notably leaner parameter set than basin-scale Copernicus Marine wave products such as MEDWAM.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Direct download (MeteoHub, raw cycle archives):**
  https://meteohub.agenziaitaliameteo.it/nwp/ww3/
- **Parameter list:**
  https://meteohub.agenziaitaliameteo.it/nwp/ww3/WW3_shortname_parameter_index.txt
- **Map viewer:**
  https://meteohub.agenziaitaliameteo.it/app/maps/marine
- **ItaliaMeteo product page:**
  https://www.agenziaitaliameteo.it/en/sea/forecasts/ww3-medita/

Cycle directories on MeteoHub follow the format `YYYYMMDDHH/` (always `HH = 00` for WW3-MEDITA). Each cycle directory contains one subdirectory per output parameter, with a single NetCDF file inside (e.g., `ww3_2026042700_surface-0.nc` for HS, ~65 MB).

---

## Relationship to other Mediterranean wave products
The Mediterranean is covered by several operational wave models with different strengths. WW3-MEDITA is distinct from each:

- **[MEDWAM](../greece/medwam.md)** — Copernicus Marine product (HCMR, Greece; WAM Cycle 6 at 1/24° ~4.6 km, structured grid, multi-satellite altimetric data assimilation, two-way coupled to Mediterranean ocean physics, 10-day forecast, 2× daily). MEDWAM is higher-frequency, longer-range, data-assimilating, and ocean-coupled, but uses a uniform structured grid that cannot resolve sub-kilometre coastal features.
- **[WW3-MARO](../france/ww3-maro-france.md)** — Météo-France's WAVEWATCH III Mediterranean product distributed via data.gouv.fr (~0.10° structured grid, AROME-forced, ~72-hour forecast, 2× daily). Same model family as WW3-MEDITA but a structured grid at coarser resolution.
- **WW3-MEDITA (this entry)** — Italian WW3 product on an unstructured mesh designed specifically to refine down to 150–200 m along the Italian coast. No documented data assimilation, no swell partitioning, single daily cycle. Its niche is high-resolution coastal wave detail in Italian waters, not basin-wide consistency or assimilation-based accuracy.

Users needing data-assimilating, long-range, or partitioned wave products should look to MEDWAM. Users needing the highest-resolution coastal wave guidance for Italian waters specifically should look to WW3-MEDITA.

The model is also part of the same operational ecosystem as the [ICON-2I atmospheric model](../../../nwp_models/regional/italy/icon-2i.md), which provides the high-resolution component of its wind forcing.

---

## Notes
- The use of an unstructured triangular mesh is the defining technical feature of WW3-MEDITA. Most operational regional wave models in this repository use structured (regular lat-lon or rotated) grids, with multi-resolution behaviour achieved through nesting (e.g., GWAM → EWAM → CWAM in DWD's chain) or refined-cell schemes (e.g., the SMC grid used by AMM15-WW3). WW3-MEDITA's continuous-resolution mesh avoids the artefacts that can arise at nest boundaries.
- Working with the output requires handling unstructured-grid NetCDF: the `latitude`, `longitude`, and `tri` (triangulation) variables describe the mesh, and field variables are indexed by mesh node rather than by (i, j) grid indices. Standard structured-grid plotting tools will not work directly on these files without first reconstructing the mesh.
- The MeteoHub archive is rolling: the directory listing shows roughly the previous month of cycles available at any given time. Users needing longer historical access should download cycles as they are produced.
- Operational since October 2025; the previous Mediterranean wave product in the same operational chain was SWAN-MEDITARE, based on the SWAN nearshore wave model.

---

## Official documentation
- ItaliaMeteo product page: https://www.agenziaitaliameteo.it/en/sea/forecasts/ww3-medita/
- ItaliaMeteo announcement: https://www.agenziaitaliameteo.it/en/agenzia/news-e-gallery/news/ww3-medita-operativo-il-nuovo-modello-per-la-previsione-dello-stato-del-mare/
- Arpae marine forecasts: https://www.arpae.it/it/temi-ambientali/mare/previsioni-mare
- WAVEWATCH III: https://polar.ncep.noaa.gov/waves/wavewatch/
