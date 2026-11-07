# Pirate NAM (Pirate Weather continuation of the NAM 12 km)

## What this model is
Pirate NAM is an experimental, community-run continuation of NOAA's retired North American Mesoscale (NAM) 12 km forecast, run by Pirate Weather. It uses **WOOF-NAM**, a GPU port of the NAM's NMMB model and physics, with NOAA's unmodified GSI analysis program in the NAM's configuration. Its stated goal is to **reproduce the forecast the NAM would have produced**. It measures itself by how close it gets to the NAM, not by accuracy against observations.

> **Experimental.** Pirate NAM is not an official forecast and not a NOAA/NCEP product. Runs may stop or change without notice, there is no regular schedule yet, and the operator says not to use it for safety-related decisions.

---

## Who runs it
- **Organization:** Pirate Weather (Alexander Rey). Pirate NAM is a fork of WOOF-NAM by Recast Systems and is not affiliated with or endorsed by NOAA, NCEP or Recast Systems.
- **Country / region:** Canada (independent / non-governmental). Runs on OVHcloud GPU machines; data is hosted in OVHcloud's Toronto (`ca-east-tor`) object storage.

---

## What area it covers
- **Coverage:** The NAM 12 km parent domain: North America and the surrounding Pacific, Atlantic and Arctic Oceans. The domain includes the North Pole.
- **Domain details:** A rotated latitude-longitude grid of **954 × 835** points, centred at **54°N, 106°W** (southern pole of rotation at 36°S, 254°E). Rotated extents are ±60.039° in longitude and ±45.036° in latitude. Geographic corners, decoded from the GRIB2:
  - SW: 7.49°S, 144.13°W
  - SE: 7.49°S, 67.87°W
  - NW: 44.54°N, 133.20°E
  - NE: 44.54°N, 14.80°E

  This is the NAM's native parent grid, **not** the NCEP grid 218 (614 × 428 CONUS Lambert conformal) used by the familiar `awphys` files. Point-by-point comparisons with archived NAM `awphys` output need regridding.

---

## Basic details
- **Model type:** Deterministic NWP (limited-area)
- **Model system / core:** NMMB (NEMS Non-hydrostatic Multiscale Model on the B grid), run through the WOOF-NAM GPU port with the NAM's physics
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** No. Runs at 12 km with parameterized convection. The NAM's 3 km nests are not run.
- **Horizontal resolution:** ~12 km (0.126° × 0.108° on the rotated grid). GRIB2 and Zarr are on the native grid. WMS is regridded to 0.1° lat-lon.
- **Grid dimensions:** 954 × 835 (see "What area it covers")
- **Vertical levels:** 60 (the NAM's levels, per WOOF-NAM documentation)
- **Model top:** TBD (presumably the NAM's, but not stated by Pirate NAM or WOOF-NAM)
- **Forecast length:** 84 h. The first published cycle, 00 UTC 5 November 2026, was a 48 h test run started from GFS without analyses.
- **Update frequency / cycles:** No fixed schedule. Available cycles are listed in `runs.json`; on 2026-11-07 it listed 00 UTC 5 Nov, 18 UTC 5 Nov and 00 UTC 6 Nov 2026. The newest 8 cycles are kept.
- **Temporal output resolution:** Hourly (f000–f084)

---

## Data assimilation
- **Data assimilation:** Yes (since the 18 UTC 5 November 2026 cycle)
- **Method / cadence:** NOAA GSI (hybrid ensemble–variational, using the newest available GEFS as the ensemble) in a 6 h catch-up. Each cycle starts from GFS 6 h before the forecast time, with **3 analyses** 2 hours apart (for a 00 UTC forecast: 20, 22 and 00 UTC). The operational NAM ran 7 hourly analyses.
  - **Observations:** RAP's hourly observation files and MRMS radar, since the NAM's own observation dumps ended with the NAM. RAP's files have no GPS radio occultation.
  - **Not used:** Satellite radiances and lightning were tested and left out because they made little difference to closeness to the NAM.

---

## Initial and boundary conditions
- **Initial conditions:** GFS (atmosphere, soil, snow, SST and sea ice), 6 h before the forecast time, then the GSI catch-up. Soil and snow are not carried from cycle to cycle as in the NAM.
- **Boundary conditions:** The GFS cycle 6 h older than the forecast time, as in the NAM.

---

## What it provides
Deterministic forecasts of:

**GRIB2 (43 records at f000, 45 at later hours):**
- Isobaric fields at 1000, 925, 850, 700, 500, 300 and 250 hPa: geopotential height, temperature, relative humidity, u/v wind
- 2 m temperature and dew point; 10 m u/v wind
- Surface pressure; mean sea level pressure
- Precipitable water; composite reflectivity
- Total and hourly precipitation (from f001)

**Zarr v3 and WMS (10 fields):** `t2m`, `d2m` (°C), `mslp` (mb), `wind10`, `wind250` (m/s), `apcp` (mm since start), `refc` (dBZ), `pwat` (mm), `z500` (dam), `t850` (°C, missing below ground)

---

## Data availability
- **Is the data free?** Yes
- **License:** TBD. No data licence is stated. The Apache 2.0 licence in the repository covers the WOOF-NAM code, not the forecast output. The inputs (GFS, GEFS, RAP, MRMS) are U.S. government works in the public domain.
- **Is the data downloadable?** Yes. Public HTTPS (S3-compatible object storage), no account required.
- **Data formats:** GRIB2 (CCSDS-compressed, template 5.42; needs a decoder built with libaec, such as ecCodes or a recent wgrib2), Zarr v3, and WMS 1.3.0 (PNG map tiles)
- **Official download location:**
  - Data access page: https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/data.html
  - Run list: https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/runs.json
  - Map viewer: https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/index.html
  - WMS: https://nam-wms.pirateweather.net/datasets/pirate-nam/wms?service=WMS&version=1.3.0&request=GetCapabilities

**Path structure** (verified live, under `https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/`):

    # GRIB2: one file per forecast hour (~40–43 MB each)
    <YYYYMMDDHH>/woof/grib/woofnam.t<HH>z.f<FFF>.grib2     # FFF = 000..084

    # Zarr v3: 10 fields, all hours (~680 MB)
    <YYYYMMDDHH>/woof/zarr

```python
import xarray as xr
ds = xr.open_zarr("https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/2026110600/woof/zarr")
ds.t2m.sel(fhr=24)
```

Reading the Zarr store over HTTP needs `fsspec` and `aiohttp`.

---

## Notes
- **This is the NAM's only continuation.** NOAA retired the NAM (12 km parent and all nests) on 3 November 2026; see the retirements section of [STATUS.md](../../../STATUS.md). NOAA's operational successor is [RRFS](../regional/usa/rrfs.md). Pirate NAM is an independent attempt to keep the NAM's 12 km forecast going, not a NOAA replacement.
- **Its output is not identified as NCEP's.** The GRIB2 centre and generating-process codes are 255 (missing), not NCEP's centre 7, so tools that identify the NAM by centre or process ID won't recognize these files.
- **The GRIB2 field set is much smaller than the NAM's.** About 43–45 records per hour, against roughly 450 in the NAM's `awphys` files. Some WOOF-NAM products, such as `awphys` GRIB2 cut to grid 218, are not published.
- **The WMS is regridded and serves only the newest run.** Layers come from a 0.1° lat-lon copy using the nearest model point, because the WMS software draws the pole-containing rotated grid wrongly. A new run appears within ~20 minutes of publication and older times drop out. In EPSG:4326, give bounding boxes as `lon,lat` (the order WMS 1.3.0 specifies returns an empty response); EPSG:3857 is recommended. Limits: 2048 px per side, 40 requests per second per address.
- **There is no long-term archive.** Only the newest 8 cycles are kept, and withdrawn runs are deleted.
- **How close it gets to the NAM** (operator's benchmarks, one test case, 00 UTC 3 Nov 2026, to 12 h): median RMSE against the archived NAM was 0.84× a plain GFS start and 1.19× a Fortran NAM rerun. Live inputs cost about 2.7% in median error compared with the NAM's own observation files. A live run is still somewhat colder at 2 m than the archived NAM, especially over water. See [BENCHMARKS.md](https://github.com/Pirate-Weather/pirate-nam/blob/main/BENCHMARKS.md).
- **Main differences from the NAM:** 12 km only, with no nests; starts from GFS including land; 3 analyses instead of 7; RAP observation files and MRMS instead of the NAM's dedicated feed, radiances and radar; runs on two NVIDIA V100S GPUs instead of a supercomputer.
- **Related entries:** inputs come from [GFS](../global/usa/gfs.md), [GEFS](../../ensemble_models/global/usa/gefs.md), [RAP](../regional/usa/rap.md) (observation files) and [MRMS](../../../observations/radar/usa/mrms.md).
- **Supporting the project:** the operator funds the runs and asks for support through [GitHub Sponsors](https://github.com/sponsors/alexander0042) or a [Pirate Weather](https://pirate-weather.apiable.io/) subscription.

---

## Recent version history
- **18 UTC 5 November 2026:** First published cycle with the GSI catch-up, run on WOOF-NAM 0.1.3 with the sun-angle fix.
- **WOOF-NAM 0.1.3 (5 November 2026):** Composite and simulated reflectivity now include the convection scheme's rain, as the NAM post did; before, they ran 3–4 dB colder than the NAM's own values.
- **Sun-angle fix (5 November 2026):** The first live GSI cycle (12 UTC 5 November) kept a cached start hour, so shortwave heating ran 6 h behind and 2 m temperatures drifted to about −3.25 K against GFS by 84 h. That run was withdrawn and deleted.
- **00 UTC 5 November 2026:** First published cycle, a 48 h test run from GFS with no data assimilation.

---

## Official documentation
- Pirate NAM repository and README: https://github.com/Pirate-Weather/pirate-nam
- Benchmarks: https://github.com/Pirate-Weather/pirate-nam/blob/main/BENCHMARKS.md
- Data access page: https://pirate-nam-tor.s3.ca-east-tor.io.cloud.ovh.net/data.html
- WOOF-NAM (upstream model, Recast Systems): https://github.com/recastsystems/woof-nam
