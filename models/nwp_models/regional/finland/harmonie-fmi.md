# HARMONIE (MEPS) — FMI distribution

## What this model is
This entry documents the **Finnish Meteorological Institute (FMI) distribution of HARMONIE (MEPS)** — the MetCoOp Ensemble Prediction System, a high-resolution, convection-permitting regional NWP system based on HARMONIE-AROME.

The underlying model is the **same MetCoOp production** documented in the [MEPS](../norway/meps.md) entry: FMI is a co-producing member of MetCoOp (one of the three HPC platforms that runs the MEPS ensemble is FMI's "Vinha") and redistributes the MEPS output through its own Open Data service. What differs is the **access mechanism** (an FMI WFS stored query plus a binary download service) and the **distributed subset** (FMI currently serves surface-level data only). This is not a separate forecast — it is the MetCoOp MEPS run repackaged and delivered through FMI's infrastructure.

---

## Who runs it
- **Organization:** MetCoOp (Meteorological Cooperation on Operational Numerical Weather Prediction). Distributed by the **Finnish Meteorological Institute (FMI)** as one of the MetCoOp member institutes; FMI also hosts one of the three HPC platforms (Vinha) on which the MEPS ensemble is produced.
  - MetCoOp member institutes: MET Norway, SMHI (Sweden), FMI (Finland, since 2017), Estonian Environment Agency (ESTEA, since 2020), and LEGMC (Latvia, since 2024)
- **Country / region:** Finland (distributor); multi-national (Nordic and Baltic) production

---

## What area it covers
- **Coverage:** Scandinavia and Finland (Nordic region, including surrounding marine areas) — the MetCoOp MEPS domain
- **Domain details:** Live-verified from the GRIB2 grid definition (2026-07-29): Lambert conformal conic, **949 × 1069** grid points, Dx 2499.96 m / Dy 2499.889 m, LaD = Latin1 = Latin2 = 63.3°N, LoV = 15.0°E, spherical earth R = 6371220 m, first (SW) grid point 50.32°N 0.279°E. The domain spans approximately 18.1°W–54.2°E and 49.8°N–75.2°N. This is the same distribution grid MET Norway uses for [MEPS](../norway/meps.md); the "960 × 1080" figure quoted in some MetCoOp literature refers to the computational grid including the extension zone, not the distributed one. FMI's WFS `gml:GridEnvelope` reports `high = 949 1069`, which implies 950 × 1070 — an off-by-one against the actual GRIB `Ni`/`Nj`.
- **Model-level grid:** The hybrid-level producer is served on a 3×-thinned version of the same projection: **316 × 356**, Dx ≈ 7523.7 m (~7.5 km), same origin and LCC parameters.
- **Subsetting:** Users are expected to subset to their area of interest with query parameters.

---

## Basic details
- **Model type:** Regional NWP (convection-permitting) — FMI distributes a **deterministic** subset of the MetCoOp MEPS production, with no ensemble information encoded (see Notes)
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core), currently cycle 43h2.2
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2.5 km; shallow convection parameterized)
- **Ensemble size (production):** 30 perturbed members + 1 control (see [MEPS](../norway/meps.md) for the ensemble/time-lagging design). **FMI distributes no ensemble members** — every GRIB message carries `typeOfProcessedData = af` with no `perturbationNumber` or `numberOfForecastsInEnsemble` (live-verified 2026-07-29). The distributed fields are consistent with the control member, but FMI does not label them as such (**TBD**).
- **Horizontal resolution:** ~2.5 km
- **Vertical levels:** 65 (production). FMI distributes three vertical subsets via separate producers: **surface** (`levels=0`), **9 isobaric levels** (1000, 925, 850, 700, 600, 500, 400, 300, 250 hPa), and **model/hybrid levels 12–65** on a thinned grid. See "What it provides".
- **Model top:** 10 hPa (~30 km) in production
- **Forecast length:** Up to 66 hours (control member); 61 hours for the perturbed ensemble members. See [MEPS](../norway/meps.md) for details.
- **Update frequency / cycles:** MetCoOp production cycles continuously (hourly); **FMI republishes every third hour** — origin times 00/03/06/09/12/15/18/21 UTC. Live-verified 2026-07-29: 12Z and 15Z returned data; 13Z and 14Z returned empty responses.
- **Data retention:** Only the **two most recent cycles** are held. At 19:00 UTC on 2026-07-29 the 09Z run and everything older returned zero bytes, and the 18Z run had not yet appeared (latency > 1 h 07 m; exact publication latency **TBD**). There is no archive — for historical MEPS data use the MET Norway [`meps25epsarchive`](../norway/meps.md) THREDDS catalogue.
- **Temporal output resolution:** 1 hour (`timestep=60`). Requesting a sub-hourly `timestep` (e.g. 15) **fails silently**: the service returns messages stamped `15m`/`30m`/`45m` filled entirely with the missing value rather than an error.

---

## Data assimilation
- **Data assimilation:** Yes (MetCoOp production)
- **Method / cadence:** Upper-air 3D-Var with large-scale mixing, and surface analysis via CANARI optimal interpolation. Assimilated observations include conventional in-situ (SYNOP, SHIP, buoy, radiosonde), aircraft (AMDAR and Mode-S EHS), GNSS ZTD, weather radar reflectivity and Doppler radial wind, scatterometer, and microwave/IR satellite radiances. See the [MEPS](../norway/meps.md) entry for the full assimilation description; data assimilation is a property of the MetCoOp production, not the FMI distribution layer.

---

## Initial and boundary conditions
- **Initial conditions:** HARMONIE-AROME analyses with ensemble perturbations
- **Boundary conditions:** ECMWF IFS (lateral boundary conditions; Météo-France ARPEGE as automatic backup)

(These are production properties shared with [MEPS](../norway/meps.md).)

---

## What it provides
FMI distributes three gridded products through three producers. **Only the surface producer is documented by FMI**; the pressure- and model-level producers are undocumented but fully operational (live-verified 2026-07-29).

### Surface — `harmonie_scandinavia_surface`
- temperature (2 m), dew point, relative humidity
- wind (10 m): U/V components, wind speed, wind direction, wind gust
- precipitation amount
- mean sea level / surface pressure, geopotential height
- total, low, medium, and high cloud cover
- CAPE
- visibility
- global / net surface shortwave and longwave radiation (instantaneous and accumulated)

The WFS advertises exactly 22 parameters. The download service additionally serves at least `Precipitation1h`, `SnowDepth`, `FogIntensity`, `WeatherSymbol3`, and `LandSeaMask`, which appear in neither FMI's documentation nor the WFS `observedProperties`. `Cloudiness`, `PrecipitationType`, and `ProbabilityThunderstorm` return empty responses. **`RadiationNetSurfaceLWAccumulation` is advertised but returns the missing value at every step tested, including mid-forecast** — treat it as unavailable.

### Pressure levels — `harmonie_scandinavia_pressure`
Nine isobaric levels (1000, 925, 850, 700, 600, 500, 400, 300, 250 hPa) on the full 949 × 1069 / 2.5 km grid. Parameters: temperature (`t`), dew point (`dpt`), relative humidity (`r`), geopotential height (`gh`), wind U/V (`u`, `v`), wind speed (`ws`), wind direction (`mdwi`). The `fmi::forecast::harmonie::pressure::grid` stored query defaults to only six levels (1000, 925, 850, 700, 500, 300); the additional 600, 400, and 250 hPa levels are reachable by querying the producer directly with `levels=`.

### Model (hybrid) levels — `harmonie_scandinavia_hybrid`
Model levels **12–65** (54 of the 65 production levels) on a 3×-thinned 316 × 356 / ~7.5 km grid. Parameters: `Pressure`, `GeomHeight`, `Temperature`, `Humidity`, `WindDirection`, `WindSpeedMS`, `WindUMS`, `WindVMS`, `VerticalVelocityMMS` (`wz`, m s⁻¹).

For ensemble members, cross-sections, and the full untinned model-level structure, use the MET Norway THREDDS distribution documented in [MEPS](../norway/meps.md).

### Duplicate stored-query namespace
`fmi::forecast::meps::surface::grid`, `::pressure::grid`, and `::hybrid::grid` also exist and resolve to the **same three `harmonie_scandinavia_*` producers**. They are aliases, not separate products.

---

## Data availability
- **Is the data free?** Yes — no registration or account required (the user must accept the Creative Commons licence before using the open data interfaces)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution to the Finnish Meteorological Institute required. The CC BY 4.0 licence applies to FMI's open data sets (as well as Finnish Transport Agency and STUK data sets distributed through the same service).
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, GRIB1, and NetCDF (selectable via the `format` parameter; all three live-verified). GRIB2 decodes cleanly with ECMWF's ecCodes. NetCDF output is classic `NETCDF3_64BIT_OFFSET`, CF-1.6.
- **File sizes:** ~3.17 MB per field per timestep on the native 2.5 km grid. A full 22-parameter, 67-timestep surface request is therefore on the order of 4.7 GB — FMI's warning about file size is not an exaggeration.
- **Official download location:**
  - WFS stored query (lists available runs): `https://opendata.fmi.fi/wfs?service=WFS&version=2.0.0&request=getFeature&storedquery_id=fmi::forecast::harmonie::surface::grid&`
  - Binary download service (direct file download): `https://opendata.fmi.fi/download` (and `data.fmi.fi`) with the `harmonie_scandinavia_surface`, `harmonie_scandinavia_pressure`, or `harmonie_scandinavia_hybrid` producer
  - Undocumented stored queries: `fmi::forecast::harmonie::pressure::grid`, `fmi::forecast::harmonie::hybrid::grid` (plus the `fmi::forecast::meps::*` aliases)

### How to access — FMI Open Data

FMI delivers gridded forecast model data in two steps, or via direct download:

1. **WFS stored query** — `fmi::forecast::harmonie::surface::grid` returns a collection of `GridSeriesObservation` features, one per available model run, including forecast period (`phenomenonTime`), available properties (`observedProperties`), forecast area geometry (`featureOfInterest`), and nominal run time (`analysisTime`). The `om:result` element does **not** embed the grid; instead it contains a `gml:fileReference` URL pointing to the binary download service.
2. **Binary Download Service** — a separate HTTP request to the referenced URL (under `opendata.fmi.fi/download` / `data.fmi.fi`) returns the actual GRIB2/GRIB1/NetCDF file.

The binary files can also be downloaded **directly** without making the WFS request first, by constructing a download URL against the `harmonie_scandinavia_surface` producer.

**Key query parameters for the download service:** `starttime`, `endtime`, `origintime` (nominal model run time), `timestep` (minutes) or `timesteps` (count), `param` (comma-separated parameters), `format` (`grib1` | `grib2` | `netcdf`), `projection` (e.g. `EPSG:4326`), and spatial subsetting via `bbox`, `gridcenter`, `gridsize`, `gridstep`, or `gridresolution`.

**Supported projections** include `EPSG:4326`, `EPSG:3995`, and polar stereographic (latitude of origin 60, central meridian 0). If no projection is specified, the data's native projection is used.

**Example (GRIB2, vicinity of Tampere, temperature and wind):**
`https://opendata.fmi.fi/download?producer=harmonie_scandinavia_surface&param=temperature,windums,windvms&format=grib2&gridcenter=23.7,61.5,100,100&projection=EPSG:4326&`

**Important:** forecast model files can be very large. FMI strongly recommends constraining each query with spatial and parameter filters to the exact area and variables of interest.

### Request limits
- Download Service: 20,000 requests per day
- View Service: 10,000 requests per day
- Combined Download + View: 600 requests per 5 minutes

### Client libraries
FMI and the community provide helper libraries for the open data interfaces:
- Python: https://github.com/pnuu/fmiopendata
- R (rOpenGov): https://github.com/rOpenGov/fmi2
- JavaScript (metolib): https://github.com/fmidev/metolib

---

## Notes
- **Same model as [MEPS](../norway/meps.md), different distribution.** FMI co-produces MetCoOp MEPS (its Vinha HPC runs members 9, 10, 11 of the ensemble, alongside SMHI/MET Norway's Cirrus and Stratus systems) and redistributes the output. The forecast state is identical to the MET Norway MEPS distribution; the differences are the access mechanism (FMI WFS + binary download service vs MET Norway THREDDS/OPeNDAP), the data format (FMI offers GRIB2/GRIB1/NetCDF; MET Norway offers NetCDF/NCML), and the distributed subset (FMI: deterministic fields only, at surface, 9 pressure levels, and thinned model levels; MET Norway: full vertical structure, all ensemble members, cross-sections, and a multi-year archive). This is analogous to how the UWC-West DINI run is repackaged independently by DMI, KNMI, Met Éireann, and the Icelandic Met Office (see [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md) and [HARMONIE (DMI)](../denmark/harmonie-dmi.md)).
- **FMI's documentation understates what is served.** The Open Data manual states that "currently only the data near the ground or the sea surface (surface level) is provided" in gridded form. This is **not correct as of 2026-07-29**: pressure-level and model-level producers are live and return valid data. The documentation gap is the single largest discrepancy in this entry's sources.
- **GRIB encoding quirks (live-verified 2026-07-29).** FMI's SmartMet-based encoder produces several fields that decode misleadingly:
  - `centre = ecmf` — files claim **ECMWF** as originating centre, not FMI. GRIB1 output uses ECMWF local table 128.
  - `Pressure` encodes as `msl` (mean sea level pressure). **Surface pressure is not served** despite the phrasing in FMI's parameter list.
  - `GeopHeight` in the surface producer encodes as `orog` (discipline 0 / category 3 / number 5) — model orography, not a geopotential height field. In the pressure producer it is a true `gh`.
  - `PrecipitationAmount` uses local parameter 192/201/113, which ecCodes renders as `rain_con` ("convective rain"). The field is not convective-only; do not trust the resolved shortName.
  - `WindDirection` uses local parameter 192/140/242 (`mdwi`), a wave-table mean wind direction code.
  - **Cloud cover units are inconsistent within one file.** `TotalCloudCover` uses the standard 0/6/1 code in **percent**; low, medium, and high cloud cover use local codes 192/128/186–188 in **fraction (0–1)**. Rescale before combining.
  - **Radiation accumulations are cumulative from forecast start, but `stepRange` encodes a 1-hour window.** Centre-point `ssrd` for the 15Z 2026-07-29 run read 4.35 → 5.61 → 7.61 → 9.86 → 12.10 → 13.80 MJ m⁻² across messages labelled `14-15`, `15-16`, … `19-20`. Take differences between consecutive steps to recover hourly totals. Raising `timestep` does not widen the accumulation window — `timestep=180` still returns 1-hour-labelled messages, just sampled 3-hourly.
  - Missing-value sentinels differ by format: **9999** in GRIB, **32700** (`_FillValue`) in NetCDF.
- **NetCDF metadata is incomplete.** Global attributes carry unfilled template placeholders (`title = <title>`, `source = <producer>`), and variables receive FMI parameter-ID suffixes (`air_temperature_4`, `eastward_wind_23`, `northward_wind_24`). CF standard names and units are correct; provenance metadata is not.
- **Point time series.** In addition to the gridded product, FMI also serves HARMONIE (MEPS) as point time series via separate stored queries (see FMI's time series data documentation).
- **No FMI HARMONIE on AWS S3.** FMI mirrors only its SILAM atmospheric composition model and radar data to AWS Open Data; HARMONIE (MEPS) is **not** on S3 and must be accessed via the WFS / binary download routes above.
- **No account required.** Unlike the Met Éireann distribution of the sibling UWC-West HARMONIE production (which requires free registration), FMI's open data needs no account — only acceptance of the CC BY 4.0 licence and adherence to the request limits.
- **Companion suites.** MetCoOp also operates two related products built on the MEPS modelling grid: **MNWC** (a deterministic 12-hour nowcasting suite refreshed hourly, partly produced on FMI's Vinha HPC) and **MECaS** (a calibrated ensemble forecast of near-surface temperature, wind, and gusts). These are documented in the [MEPS](../norway/meps.md) entry's notes. FMI does not currently appear to redistribute MNWC or MECaS through this Open Data service.
- **Relationship to siblings:**
  - [MEPS](../norway/meps.md) — the MET Norway distribution of the same MetCoOp production (full distribution; primary reference for model internals).
  - [AROME-Arctic](../norway/arome-arctic.md) — MET Norway's deterministic Arctic-domain HARMONIE-AROME model.
  - [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), [HARMONIE (DMI)](../denmark/harmonie-dmi.md), and other ACCORD-consortium HARMONIE-AROME / AROME deployments share the same core model.
- **Repository location:** Like the [MEPS](../norway/meps.md) entry, this documents an ensemble system but is filed under `nwp_models/regional/` for consistency with the existing MEPS entry. The same relocation consideration noted there applies here.

---

## Official documentation
- FMI Open Data — Forecast models manual: https://en.ilmatieteenlaitos.fi/open-data-manual-forecast-models
- FMI Open Data — overview: https://en.ilmatieteenlaitos.fi/open-data
- FMI Open Data — licence (CC BY 4.0): https://en.ilmatieteenlaitos.fi/open-data-licence
- FMI Open Data — accessing data: https://en.ilmatieteenlaitos.fi/open-data-manual-accessing-data
- FMI Open Data — data models: https://en.ilmatieteenlaitos.fi/open-data-manual-data-models
- FMI WFS GetCapabilities: https://opendata.fmi.fi/wfs?request=GetCapabilities
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/
- Eresmaa et al. (2026), *The Operational Forecast Process at MetCoOp*, Bull. Amer. Meteor. Soc., 107, E946–E963. https://doi.org/10.1175/BAMS-D-25-0225.1
- See the [MEPS](../norway/meps.md) entry for further HARMONIE-AROME and MetCoOp references.
