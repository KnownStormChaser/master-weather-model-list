# SILAM Europe (FMI Regional Air Quality Forecast — European domain)

## What this model is
SILAM (System for Integrated modeLling of Atmospheric coMposition) is FMI's global-to-mesoscale chemistry-transport and dispersion model. This entry covers **FMI's operational European-domain SILAM air-quality forecast** — a full-chemistry regional configuration distributed publicly through FMI's THREDDS Data Server.

Unlike the AWS Open Data product, which carries only a seven-species **surface** subset of the *global* run (see [SILAM Global](../../global/finland/silam-global.md)), the European THREDDS output is the full model state: ~400 concentration and aerosol fields on 10 vertical layers, at 0.1° (~10 km) over Europe. Primary intended uses are regional air-quality forecasting and research.

Note: FMI also contributes a European SILAM configuration to the CAMS Regional ensemble (see [CAMS Regional](../eu/cams-regional.md)); that is a separate, CAMS-harmonised setup. This entry documents FMI's own independently distributed European forecast.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI) — Atmospheric Composition Research / Air Quality modelling group
- **Country / region:** Finland (forecast domain: Europe)

---

## What area it covers
- **Coverage:** Europe (plus a North-African fringe and the NE Atlantic)
- **Domain details:** Regular latitude–longitude grid, **700 × 420**, **25.0°W–45.0°E, 30.0°N–72.0°N**, at **0.1° × 0.1°**. (Grid confirmed against both the FMI SILAM factsheet and live THREDDS metadata, July 2026.)

---

## Basic details
- **Model type:** Regional air quality / atmospheric composition (offline chemistry-transport)
- **Model system / core:** SILAM **v6.1** (multi-scale Eulerian advection with semi-Lagrangian / Eulerian dispersion)
- **Horizontal resolution:** 0.1° × 0.1° (~10 km)
- **Vertical levels:** 10 staggered "thick" height layers (mass-conserving), from a ~25 m-thick lowest layer up to a model top near ~7.7 km; layer mid-heights 12.5–7725 m
- **Model top:** ~7.7 km
- **Forecast length:** **120 h (5 days)**
- **Update frequency / cycles:** 1× daily (00 UTC run)
- **Temporal output resolution:** Hourly

---

## Meteorological driver
- **Driving NWP model:** ECMWF IFS — SILAM is directly forced by IFS meteorological fields (per the FMI SILAM factsheet)
- **Coupling:** Offline (one-way); IFS meteorology is read through SILAM's meteorological pre-processor
- **Update source frequency:** IFS operational dissemination

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** SILAM gas-phase chemistry (carbon-bond lineage, CB4/CB05) — *verify exact mechanism and version against current SILAM documentation*
- **Number of chemical species:** >100 species carried internally; ~409 output fields in this distribution
- **Aerosol treatment:** Sectional (size-resolved) — *verify bin count*
- **Aerosol components represented:** Sulfate, nitrate, ammonium (e.g. ammonium nitrate), organic and black/elemental carbon, sea salt, mineral dust, primary PM; fire-emitted PM tracers (FRP-/GFAS-tagged) also present
- **Heterogeneous / aqueous chemistry:** Yes (SILAM includes aqueous-phase and heterogeneous processes) — *verify specifics*

---

## Emissions
- **Anthropogenic inventory:** CAMS-REG European regional inventory (typical for SILAM's European domain) — *verify current version*
- **Biogenic emissions:** Online biogenic VOC (isoprene present in output as `cnc_C5H8_gas`) — *verify scheme*
- **Wildfire emissions:** Fire-radiative-power based — FMI **IS4FIRES** and/or **GFAS** (output carries `cnc_PM_FRP` and `cnc_PM_GFAS_*` tracers; IS4FIRES is served on the same THREDDS server)
- **Dust scheme:** Online (`cnc_dust` present)
- **Sea salt scheme:** Online (sea-salt PM present, e.g. `cnc_PM10SS`)
- **Other sources:** As configured (marine, soil, volcanic) — *verify*

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
FMI runs a parallel SILAM pollen / aeroallergen forecast for this region, distributed as `silam_europe_pollen_v6_1`.
- **Coverage / grid:** Rotated latitude–longitude, 549 × 459, ~0.1° (~10 km), over a broad European domain (~47.6°W–78.1°E, 19.0°N–76.0°N — wider than the chemistry domain)
- **Taxa / allergens:** Birch, alder, grass, hazel, mugwort (with sub-source variants), olive, ragweed; plus an aphids tracer
- **Products per taxon:** Airborne concentration (`cnc_POLLEN_*`), ready-to-fly amount (`Poll_Rdy2fly_*`), remaining / total seasonal pollen (`poll_left_*`, `poll_tot_m2_*`), phenology heat-sums (`heatsum_*`), empirical bias correction (`pollen_corr_*`), plus driving meteorology (2 m temperature, humidity, 10 m wind, precipitation)
- **Cadence / output:** Daily run, hourly output (multi-day forecast; exact per-run length not separately verified)
- **Access:** THREDDS — https://thredds.silam.fmi.fi/thredds/catalog/silam_europe_pollen_v6_1/catalog.xml — via OPeNDAP, NetCDF Subset Service, and HTTPServer (same service set as the chemistry dataset)

---

## Data availability
- **Is the data free?** Yes
- **License:** Presumed CC BY 4.0 (FMI open data), consistent with the AWS distribution — **but the THREDDS server's own licence statement is not confirmed here; verify before relying on it** (open access ≠ open licence)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (`.nc4`)
- **Official download location:**
  - THREDDS catalog: https://thredds.silam.fmi.fi/thredds/catalog/silam_europe_v6_1/catalog.xml
  - Access services (verified live July 2026, no login): **OPeNDAP** (`/thredds/dodsC/`), **NetCDF Subset Service** (`/thredds/ncss/grid/`), **HTTPServer** whole-file download (`/thredds/fileServer/`)
  - Aggregated "best" time series: `silam_europe_v6_1_best.ncd` (via OPeNDAP/NCSS)
  - Native per-run files: `SILAM-AQ-europe_v6_1_<YYYYMMDDHH>_<NNN>.nc4` — one file per forecast hour (`001`–`120`), ~420 MB each

---

## Notes
- **Distributed via FMI's THREDDS server, not AWS.** The AWS Open Data bucket carries only the global surface subset; the full regional chemistry lives here. See [SILAM Global](../../global/finland/silam-global.md) for the AWS product.
- **Distinct from FMI's CAMS Regional contribution**, which is a separate, CAMS-harmonised European SILAM configuration delivered through the Copernicus ADS — see [CAMS Regional](../eu/cams-regional.md).
- **Pollen is included here** (see *Pollen and aeroallergens*) as an air-quality-relevant exposure. It is a distinct THREDDS dataset (`silam_europe_pollen_v6_1`) on its own broader grid, but documented within this entry rather than separately.
- **Archive depth:** ~63 daily runs retained at time of check (2026-05-15 → 2026-07-16); confirm current retention before documenting as a fixed window.
- Grid, 10-layer vertical structure, and IFS forcing are corroborated by the FMI SILAM factsheet (Feb 2020) and by live THREDDS metadata (July 2026).

---

## Official documentation
- SILAM model site: http://silam.fmi.fi
- FMI SILAM factsheet (grid / vertical / meteorological driver): https://atmosphere.copernicus.eu/sites/default/files/2020-09/SILAM%20factsheet_Feb2020.pdf
- THREDDS catalog (this dataset): https://thredds.silam.fmi.fi/thredds/catalog/silam_europe_v6_1/catalog.xml
- FMI open data: http://en.ilmatieteenlaitos.fi/open-data
