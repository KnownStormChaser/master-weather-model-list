# Baltic Sea hydrodynamic model (NEMO) — FMI open data distribution

## What this model is
FMI distributes surface fields from its operational NEMO-based Baltic Sea hydrodynamic forecast as raw gridded data through the FMI Open Data download service. The feed provides sea surface temperature, sea surface salinity, surface currents, and sea surface height across the Baltic basin on an hourly, five-day forecast.

This entry documents the **open-data distribution only**, which is a thin surface slice of a full 3D ocean model. FMI's own open-data pages label the product simply "Sea models: waves, currents (NEMO)"; the download service exposes it under `producer=nemo` and the WFS under the name `hydrodyn`. FMI does not publish a configuration or version identifier for this feed.

The underlying production is understood to belong to the **Nemo-Nordic** lineage — the NEMO-based Baltic and North Sea configuration developed jointly by FMI and SMHI and used operationally by the Copernicus Marine Baltic Monitoring and Forecasting Centre. **This linkage is inferred from the producer name and parameter set, not stated by FMI (TBD).**

---

## Who runs it
- **Production Unit:** Finnish Meteorological Institute (FMI)
- **Country:** Finland; domain covers multi-national Baltic waters
- **Programme or coordinating body:** Not stated for this feed. The Nemo-Nordic configuration is developed and operated jointly by FMI and SMHI within the Copernicus Marine BAL MFC (**TBD** whether this specific feed is the BAL MFC production or an FMI-internal run).
- **Role in any larger system:** Likely supplies surface current, sea level and ice fields to FMI's Baltic wave chain — see [WAM Baltic Sea (FMI)](../../../wave_models/regional/finland/wam-baltic-fmi.md) (**TBD**).

---

## What area it covers
- **Coverage:** Baltic Sea only — Gulf of Bothnia, Bothnian Sea, Åland and Archipelago Seas, Gulf of Finland, Gulf of Riga, Baltic Proper, and the southwestern transition waters
- **Domain bounds (live-verified 2026-07-29):** 9.041°E to 30.291°E, 53.025°N to 65.875°N
- **Grid dimensions:** 512 × 516 regular latitude–longitude, di = 0.041585°, dj = 0.024951° (≈2.3 km × 2.8 km at 60°N); spherical earth R = 6371220 m
- **Special masked or excluded regions:** A sea-point bitmap retains **64,540 of 264,192** grid points (~24%); land is encoded as missing. **The North Sea portion of the Nemo-Nordic domain is absent** — this is a Baltic-only cutout.

---

## Basic details
- **Model type:** Regional ocean physics, deterministic. **Only surface fields are distributed.**
- **Core ocean model:** NEMO (producer name and FMI's own "currents (NEMO)" labelling). Version and configuration not stated for this feed — the Nemo-Nordic 2.0 configuration described by Kärnä et al. (2021) uses NEMO 4.0 (**TBD**).
- **Sea ice model:** An `IceConcentration` parameter is served (see below), implying an ice component; the model is **TBD**. Nemo-Nordic 2.0 uses the NEMO-LIM3/SI3 lineage.
- **System name:** None published. FMI uses `nemo` (download service), `hydrodyn` (WFS), and "Hydrodynamic sea current model" (documentation).
- **Horizontal resolution:** Delivered at ≈2.3 × 2.8 km. Nemo-Nordic 2.0 runs at approximately 1 nmi (1.85 km) natively, so **the delivered grid is a regridded/coarsened product, not the model's native curvilinear grid** (the native NEMO grid is not regular lat-lon).
- **Vertical levels:** **Surface only in this distribution.** The `levels=` query parameter is accepted but **silently ignored** — live-verified with `levels=` 0, 1, 2, 5, 10, 20, 50, 100 and 200, all returning byte-identical surface fields at `level=0`. The underlying model is fully 3D (Nemo-Nordic 2.0 uses 56 z* levels with 1 m surface layer resolution), but none of it is exposed here.
- **Vertical coordinate:** z* in the underlying configuration (**TBD** for this feed; irrelevant to the surface-only product)
- **Forecast length:** 120 h (5 days) from T+0
- **Update frequency:** 2× daily
- **Production cycles:** 00 and 12 UTC
- **Target delivery time:** Not published. Live-verified 2026-07-29: the 12Z cycle had not appeared at 19:28 UTC, giving a latency of **more than 7 h 28 m** for that cycle. A single observation cannot pin this down (**TBD**).
- **Temporal output resolution:** 1 hour
- **Archive availability:** **Three cycles only** (~36 h). Live-verified: 2026-07-28 00Z, 2026-07-28 12Z and 2026-07-29 00Z returned data; 2026-07-27 12Z and earlier returned nothing. No archive.
- **Bathymetry source:** TBD

---

## Forcing
- **Atmospheric forcing:** TBD. FMI's operational Baltic chain is driven by HARMONIE-AROME — see [HARMONIE (MEPS) — FMI distribution](../../../nwp_models/regional/finland/harmonie-fmi.md) — but this is not documented for the open-data feed.
- **River runoff:** TBD
- **Lateral boundary conditions:** TBD. Nemo-Nordic includes the North Sea and takes its open boundary in the northern North Sea; since this feed is a Baltic cutout of a larger domain, the boundary is internal to the parent run rather than at the delivered domain edge.
- **Tidal forcing:** TBD. Baltic tides are small; whether explicit tides are included is not stated.
- **Ice forcing or coupling:** Online sea ice component implied by the `IceConcentration` output (**TBD**).
- **Initial conditions:** TBD

---

## Coupling
Not documented for this feed (**TBD**). Nemo-Nordic operates as ocean-plus-sea-ice with one-way atmospheric forcing; whether the FMI operational chain couples to the [WAM wave model](../../../wave_models/regional/finland/wam-baltic-fmi.md) in either direction is unverified.

---

## Data assimilation
- **DA scheme:** TBD — not documented for this feed.
- **Update cycle:** TBD
- **Increment application:** TBD

### Assimilated observations
Not documented (**TBD**). Recorded here as unknown rather than absent; the Nemo-Nordic operational chain has used SST and in-situ assimilation in some configurations.

---

## What it provides

### 3D ocean fields
**None.** The distribution is surface-only; see *Basic details*.

### Surface fields (live-verified 2026-07-29)

| `param` | eccodes | GRIB2 d/c/n | GRIB units | Delivered precision |
|---|---|---|---|---|
| `TemperatureSea` | `ocpt` | 192/150/129 | **°C** | 0.1 °C |
| `Salinity` | `ocs` | 192/150/130 | psu | 0.1 psu |
| `SeaWaterVelocityU` | *(unresolved)* | 10/1/2 | m s⁻¹ | ~1×10⁻⁴ m s⁻¹ |
| `SeaWaterVelocityV` | *(unresolved)* | 10/1/3 | m s⁻¹ | ~1×10⁻⁴ m s⁻¹ |
| `SeaLevel` | `sl` | 192/150/152 | m | **0.1 m** |

`SeaLevel` is **not advertised** in the WFS stored query but is served by the producer.

- **Sea surface height reference frame:** Not stated (**TBD**). Combined with the 0.1 m quantization, `SeaLevel` should not be treated as a usable sea level or surge product.

### Sea ice fields
- `IceConcentration` (`ci`, GRIB2 10/2/0, fraction 0–1) is accepted by the producer and returns a well-formed but **entirely missing-valued** field in July. Whether it is populated during the ice season is **TBD** and should be re-verified in winter. `IceThickness` is not served.

### Special derived products
None.

### Static fields
None. Bathymetry, land-sea mask and grid cell dimensions are not distributed; the land mask can be inferred from the GRIB bitmap.

---

## Data availability
- **Is the data free?** Yes — no registration or account required; the user must accept the Creative Commons licence before using the open data interfaces
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution to the Finnish Meteorological Institute required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, GRIB1, and NetCDF via the `format` parameter — but **not all parameters export to all formats**. `SeaLevel` returns zero bytes when `format=netcdf`; it is GRIB-only. NetCDF output is classic `NETCDF3_64BIT_OFFSET`, CF-1.6.
- **Product identifier:** None published
- **Dataset identifiers:** `producer=nemo` (download service); `fmi::forecast::hydrodyn::grid` (WFS). **`producer=hbm` returns byte-identical data** — a legacy alias, not a separate model (see Notes).
- **File naming:** Not applicable — files are generated per request, not published as named objects
- **File size:** ~912 kB for four fields at one timestep on the full delivered grid; ~227 kB per single field per timestep
- **Official access:**
  - WFS stored query: `https://opendata.fmi.fi/wfs?service=WFS&version=2.0.0&request=getFeature&storedquery_id=fmi::forecast::hydrodyn::grid&`
  - Binary download service: `https://opendata.fmi.fi/download` with `producer=nemo`
  - Point time-series stored queries: `fmi::forecast::hydrodyn::point::{simple,timevaluepair,multipointcoverage}`
- **DOI:** None for this feed
- **Delivery mechanism:** HTTP GET against the SmartMet-based binary download service; the WFS returns a `gml:fileReference` URL rather than embedding the grid. Request limits: 20,000 download requests/day, 10,000 view requests/day, 600 combined per 5 minutes.

---

## Version history

No version history is published for this feed. FMI does not expose a configuration or version identifier, and the open-data documentation has not been updated to name the model beyond "(NEMO)". Reconstructing a history would require correlating against Nemo-Nordic releases, which is not currently justified (**TBD**).

---

## Notes
- **Surface-only, and `levels=` lies.** The single most important constraint. The query parameter is accepted without error and then ignored; every depth request returns the surface field. A user could easily believe they had retrieved a 50 m temperature field. If 3D Baltic ocean physics is needed, this feed cannot supply it — use the Copernicus Marine Baltic analysis-forecast physics product instead.
- **Temperature, salinity and sea level are quantized to 0.1.** Live-verified across the full domain: SST resolves in 0.1 °C steps, salinity in 0.1 psu steps, and `SeaLevel` takes **exactly 11 distinct values across the entire Baltic Sea** (−0.4 to 0.6 m). Currents are not affected (~1×10⁻⁴ m s⁻¹). The GRIB messages use 24-bit packing, so the precision loss happens upstream of encoding, presumably in an intermediate storage step. **The sea level field is too coarse for surge, flooding or navigation use**; FMI's separate OAAS sea level forecast for predefined points is the appropriate product for that purpose and is distributed as point time series only.
- **`producer=hbm` is an alias, not a second model.** It returns byte-identical grids, values and bitmaps to `producer=nemo`. HBM (the HIROMB-BOOS Model) was FMI's earlier operational Baltic hydrodynamic model; the producer name survives as a compatibility alias pointing at the NEMO output. Do not catalog it as a distinct system.
- **GRIB encoding quirks (live-verified):**
  - `centre = ecmf` — files declare ECMWF as originating centre, not FMI.
  - **`typeOfLevel = entireAtmosphere`** on salinity, both current components, and sea level. Only temperature is correctly encoded as `surface`. These are ocean fields; the level type is meaningless.
  - **Current components do not resolve in ecCodes 2.48** — `shortName = unknown`, `units = unknown`. Fall back to the WMO GRIB2 Code Table 4.2: discipline 10 (oceanographic), category 1 (currents), number **2 = U-component of current**, number **3 = V-component of current**, both m s⁻¹. NetCDF output resolves them correctly as `eastward_sea_water_velocity` / `northward_sea_water_velocity`.
  - Missing-value sentinel is **9999** in GRIB but **32700** (`_FillValue`) in NetCDF.
- **SST units differ by format.** GRIB2 delivers **degrees Celsius**; NetCDF delivers **Kelvin** for the same request (verified: GRIB 12.40–20.90, NetCDF 285.55–294.05 K over the same box and step). Salinity and currents are consistent across formats. Check units per format rather than assuming.
- **NetCDF carries better metadata than GRIB here.** Variables receive correct CF standard names and FMI parameter-ID suffixes (`sea_surface_temperature_61`, `eastward_sea_water_velocity_1146`). Global attributes, however, retain unfilled template placeholders (`title = <title>`, `source = <producer>`) — the same defect present across all FMI SmartMet NetCDF output.
- **Retention is three cycles.** No archive of any kind. Continuous local capture is required for any time-series use.
- **Companion FMI marine feeds on the same service:** [WAM Baltic Sea](../../../wave_models/regional/finland/wam-baltic-fmi.md) (`producer=wam`) and the OAAS sea level forecast (point time series only).

---

## Official documentation
- Product page: none — FMI documents this only as a line item on https://en.ilmatieteenlaitos.fi/open-data-sets-available ("Sea models: waves, currents (NEMO)")
- FMI Open Data — Forecast models manual: https://en.ilmatieteenlaitos.fi/open-data-manual-forecast-models
- FMI Open Data — licence (CC BY 4.0): https://en.ilmatieteenlaitos.fi/open-data-licence
- FMI WFS GetCapabilities: https://opendata.fmi.fi/wfs?request=GetCapabilities
- Operating institute: https://en.ilmatieteenlaitos.fi/marine-dynamics-group

### Key references
- Kärnä, T. et al. (2021). *Nemo-Nordic 2.0: operational marine forecast model for the Baltic Sea*. Geoscientific Model Development, 14, 5731–5749. https://doi.org/10.5194/gmd-14-5731-2021
- Hordoir, R. et al. (2019). *Nemo-Nordic 1.0: a NEMO-based ocean model for the Baltic and North seas — research and operational applications*. Geoscientific Model Development, 12, 363–386. https://doi.org/10.5194/gmd-12-363-2019
