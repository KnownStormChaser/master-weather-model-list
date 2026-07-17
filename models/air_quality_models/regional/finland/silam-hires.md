# SILAM Hires (FMI High-Resolution Air Quality Forecast — Nordic / Finland domain)

## What this model is
SILAM (System for Integrated modeLling of Atmospheric coMposition) is FMI's global-to-mesoscale chemistry-transport and dispersion model. This entry covers **FMI's operational high-resolution ("hires") SILAM air-quality forecast** over a Finland-centred Nordic/Baltic domain — a full-chemistry, sub-kilometre-scale configuration distributed publicly through FMI's THREDDS Data Server.

It is the finest of FMI's publicly distributed SILAM configurations. Where the [global](../../global/finland/silam-global.md) run is 0.2° and the [European](./silam-europe.md) run is 0.1°, this one resolves the Finnish/Baltic region at roughly **1 km**, carrying the full ~470-field model state on 10 vertical layers. Primary intended use is high-resolution regional air-quality forecasting.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI) — Atmospheric Composition Research / Air Quality modelling group
- **Country / region:** Finland (forecast domain: Finland, the Baltic, and adjacent Scandinavia / NW Russia)

---

## What area it covers
- **Coverage:** Finland-centred Nordic / Baltic domain — real-world bounds approximately **17.5°E–38.2°E, 58.3°N–70.8°N**
- **Domain details:** **Rotated** latitude–longitude grid, **1560 × 900** points; rotated-grid spacing ~0.0074°–0.0075° (≈ 0.8 km, i.e. roughly 1 km-scale). (Bounds and grid confirmed from live THREDDS metadata, July 2026.)

---

## Basic details
- **Model type:** Regional (high-resolution) air quality / atmospheric composition (offline chemistry-transport)
- **Model system / core:** SILAM **v6.1** (multi-scale Eulerian advection with semi-Lagrangian / Eulerian dispersion)
- **Horizontal resolution:** ~0.0075° on a rotated-pole grid (≈ 0.8 km; sub-kilometre / "hires")
- **Vertical levels:** 10 staggered "thick" height layers (mass-conserving), layer mid-heights 12.5–7725 m; model top near ~7.7 km
- **Model top:** ~7.7 km
- **Forecast length:** **48 h (2 days)**
- **Update frequency / cycles:** 1× daily (00 UTC run)
- **Temporal output resolution:** Hourly

---

## Meteorological driver
- **Driving NWP model:** A high-resolution NWP driver — **not confirmed from the distributed files.** A ~1 km chemistry run requires km-scale meteorology, so this is most likely a high-resolution FMI NWP (HARMONIE-AROME / MEPS) rather than the coarser IFS used for the global and European runs. ***Flag: verify the hires meteorological driver against FMI/SILAM documentation.***
- **Coupling:** Offline (one-way), read through SILAM's meteorological pre-processor
- **Update source frequency:** *Verify*

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** SILAM gas-phase chemistry (carbon-bond lineage, CB4/CB05) — *verify exact mechanism and version*
- **Number of chemical species:** >100 carried internally; ~475 output fields in this distribution (slightly more than the European set)
- **Aerosol treatment:** Sectional (size-resolved) — *verify bin count*
- **Aerosol components represented:** Sulfate, nitrate, ammonium, organic and black/elemental carbon, sea salt, mineral dust, primary PM; fire-emitted PM tracers (FRP-/GFAS-tagged) present
- **Heterogeneous / aqueous chemistry:** Yes — *verify specifics*

---

## Emissions
- **Anthropogenic inventory:** CAMS-REG European regional inventory (typical for SILAM's European-scale runs); a finer local inventory may be used over Finland — *verify*
- **Biogenic emissions:** Online biogenic VOC (isoprene present as `cnc_C5H8_gas`) — *verify scheme*
- **Wildfire emissions:** Fire-radiative-power based — FMI **IS4FIRES** and/or **GFAS** (output carries `cnc_PM_FRP` and `cnc_PM_GFAS_*` tracers)
- **Dust scheme:** Online — *confirm activity over this domain*
- **Sea salt scheme:** Online (relevant given the Baltic / marine coverage)
- **Other sources:** As configured — *verify*

---

## What it provides
Full-chemistry 3D concentration fields (`cnc_*`), hourly, on 10 height layers, including:
- Ozone (O3), nitric oxide (NO), nitrogen dioxide (NO2), sulfur dioxide (SO2), carbon monoxide (CO)
- PM2.5 and PM10 (including sea-salt and dust components)
- Speciated inorganic aerosol (sulfate, nitrate, ammonium) and organic / carbonaceous aerosol
- Mineral dust and sea salt
- Fire-emitted PM tracers
- Supporting fields (air density, etc.)

Airborne pollen and aeroallergens are treated here as an air-quality-relevant exposure and are documented in the section below.

---

## Pollen and aeroallergens (companion dataset)
FMI runs a parallel SILAM pollen / aeroallergen forecast on the **same rotated Finland/Baltic grid** as the chemistry dataset, distributed as `silam_hires_pollen_v6_1`.
- **Coverage / grid:** Identical to the chemistry dataset — rotated latitude–longitude, 1560 × 900, ~0.0075° (~0.8 km), 17.5°E–38.2°E / 58.3°N–70.8°N
- **Taxa / allergens:** Birch, alder, grass, hazel, mugwort (with sub-source variants), olive, ragweed; plus an aphids tracer
- **Products per taxon:** Airborne concentration (`cnc_POLLEN_*`), ready-to-fly amount (`Poll_Rdy2fly_*`), remaining / total seasonal pollen (`poll_left_*`, `poll_tot_m2_*`), phenology heat-sums (`heatsum_*`), empirical bias correction (`pollen_corr_*`), plus driving meteorology
- **Cadence / output:** Daily run, hourly output
- **Access:** THREDDS — https://thredds.silam.fmi.fi/thredds/catalog/silam_hires_pollen_v6_1/catalog.xml — via OPeNDAP, NetCDF Subset Service, and HTTPServer

---

## Data availability
- **Is the data free?** Yes
- **License:** Presumed CC BY 4.0 (FMI open data), consistent with the AWS distribution — **but the THREDDS server's own licence statement is not confirmed here; verify before relying on it** (open access ≠ open licence)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (`.nc4`)
- **Official download location:**
  - THREDDS catalog: https://thredds.silam.fmi.fi/thredds/catalog/silam_hires_v6_1/catalog.xml
  - Access services (verified live July 2026, no login): **OPeNDAP** (`/thredds/dodsC/`), **NetCDF Subset Service** (`/thredds/ncss/grid/`), **HTTPServer** whole-file download (`/thredds/fileServer/`)
  - Aggregated "best" time series: `silam_hires_v6_1_best.ncd` (via OPeNDAP/NCSS)
  - Native per-run files: `SILAM-AQ-hires_v6_1_<YYYYMMDDHH>_<NNN>.nc4` — one file per forecast hour (`001`–`048`), ~1.43 GB each

---

## Notes
- **Distributed via FMI's THREDDS server, not AWS.** See [SILAM Global](../../global/finland/silam-global.md) (AWS surface subset) and [SILAM Europe](./silam-europe.md) (0.1° full chemistry) for the companion configurations.
- **Rotated-pole grid.** Coordinates in the files are rotated latitude/longitude; reproject (or use the file's grid-mapping metadata) before combining with regular lat/lon products. The real-world bounding box above is approximate.
- **Large files.** At ~1.43 GB per hourly file (~1.4 M grid points × ~475 fields × 10 layers), full-run downloads are heavy; prefer OPeNDAP or NCSS subsetting for targeted extractions.
- **Pollen is included here** (see *Pollen and aeroallergens*) as an air-quality-relevant exposure. The companion `silam_hires_pollen_v6_1` shares this entry's grid and is documented within this entry rather than separately.
- **Archive depth:** Short rolling window — only ~4 daily runs present at time of check (2026-07-13 → 2026-07-16), versus ~63 for the European domain. Confirm current retention.

---

## Official documentation
- SILAM model site: http://silam.fmi.fi
- FMI SILAM factsheet (model core / vertical structure / IFS forcing for the European-scale runs): https://atmosphere.copernicus.eu/sites/default/files/2020-09/SILAM%20factsheet_Feb2020.pdf
- THREDDS catalog (this dataset): https://thredds.silam.fmi.fi/thredds/catalog/silam_hires_v6_1/catalog.xml
- FMI open data: http://en.ilmatieteenlaitos.fi/open-data
