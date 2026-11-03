# HiresW Guam (High-Resolution Window Forecast System — Guam domain)

## What this model is
HiresW (the High-Resolution Window Forecast System, sometimes written HiResW or HRW) is NOAA/NCEP's dual-core, convection-allowing regional NWP system. It runs two dynamical cores, WRF-ARW and FV3, side by side. **Guam is the only domain still produced.** It is the only U.S. operational convection-allowing guidance for Guam and the surrounding western Pacific.

The CONUS, Alaska, Hawaii and Puerto Rico domains were retired on **November 3, 2026 at 12 UTC** under NWS SCN 26-47, when [RRFS](./rrfs.md) and [REFS](../../../ensemble_models/regional/usa/refs.md) became operational. Guam was explicitly preserved because RRFS does not extend that far west.

---

## Who runs it
- **Organization:** NOAA / National Weather Service — NCEP (the Environmental Modeling Center develops it; NCEP Central Operations runs it in the production suite)
- **Country / region:** United States (Guam and the surrounding western Pacific)

---

## What area it covers
- **Coverage:** Guam, the southern Northern Mariana Islands and surrounding western Pacific waters
- **Domain details:** The two output grids cover **different areas**: the finer grid is a smaller window inside the 5 km grid. Verified from GRIB2 headers, 2026-11-03 12 UTC:
  - **5 km grid:** regular lat-lon, 223 × 170 points at 0.045°, from 11.70°N 141.00°E to 19.305°N 150.99°E
  - **2.5 km grid:** Mercator (LaD 20°N), 193 × 193 points at 2500 m, from 12.35°N 143.687°E to 16.794°N 148.28°E

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing, dual-core)
- **Model system / core:** Two independent dynamical cores:
  - **WRF-ARW** (Advanced Research WRF; developed and maintained by NCAR)
  - **FV3** (Finite-Volume Cubed-Sphere, limited-area configuration; UFS lineage). This core replaced the earlier NMMB core in HiresW.
- **Dynamical formulation:** Non-hydrostatic (both cores)
- **Convection-allowing:** Yes. Deep convection is explicitly resolved; no cumulus parameterization.
- **Horizontal resolution:** Native computational grid ~3–4 km (TBD for Guam specifically). Output is published on two grids, ~5 km and 2.5 km (see *What area it covers*).
- **Vertical levels:** WRF-ARW ~50 levels (raised from 40 in May 2015). The FV3 native level count is not documented in public NCEP materials and cannot be recovered from the output, which is on pressure and surface levels only.
- **Forecast length:**
  - WRF-ARW: **48 hours** (f00–f48)
  - FV3: **60 hours** (f00–f60)
- **Update frequency / cycles:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** Hourly

---

## Initial and boundary conditions
- **WRF-ARW:** Initial and lateral boundary conditions from [GFS](../../global/usa/gfs.md)
- **FV3:** Initial and lateral boundary conditions interpolated from [GFS](../../global/usa/gfs.md)

There is **no second ARW member** (`mem2`) for Guam. On the retired domains that member was initialized from NAM, which never covered Guam. So Guam is two runs per cycle, one per core.

---

## What it provides
Deterministic convection-allowing forecasts. The two grids carry **different parameter sets**, not just different resolutions:

- **5 km grid: the full output.** Upper-air and surface fields:
  - Geopotential height, temperature, relative humidity, dewpoint, u/v wind, vertical velocity and absolute vorticity on **25 isobaric levels**: 1000–500 hPa every 25 hPa, then 400, 300, 250 and 200 hPa.
  - 2 m temperature, humidity and dewpoint; 10 m wind and gusts; MSLP.
  - Precipitation and categorical precipitation type.
  - Composite and simulated reflectivity; CAPE, CIN, lifted indices, helicity, updraft helicity and storm motion.
  - Precipitable water, column-integrated hydrometeors and lightning.
  - Cloud cover by layer, ceiling and visibility, and the Haines Index.
  - Soil temperature and moisture.
- **2.5 km grid: surface and near-surface fields only, with no pressure levels.** The parameter set is in the style of NDFD weather elements:
  - 2 m temperature, humidity and dewpoint, including max/min temperature and RH.
  - 10 m wind speed, direction and gusts.
  - Precipitation, probability of precipitation and wetting-rain fields.
  - Reflectivity, CAPE/CIN and updraft helicity.
  - Ceiling, visibility and total cloud cover.
  - Land–sea mask and orography.

Record counts at f24 (2026-11-03 12 UTC): 5 km — ARW 257, FV3 259; 2.5 km — ARW 97, FV3 99.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2. Each file has a `.idx` index. BUFR soundings are also produced per core and cycle (see below).
- **Official download locations:**
  - NOMADS (real-time; two days on line, observed 2026-11-03):
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/
  - NOMADS GRIB filter (5 km grid only, both cores):
    https://nomads.ncep.noaa.gov/gribfilter.php?ds=hiresguam
  - Product information page:
    https://www.nco.ncep.noaa.gov/pmb/products/hiresw/

### File naming convention
Files live under a per-day directory `hiresw.YYYYMMDD/` and follow:

```
hiresw.t{CC}z.{core}_{grid}.f{FF}.guam.grib2
```

- `{CC}`: cycle, `00` or `12`
- `{core}`: `arw` or `fv3`
- `{grid}`: `5km` or `2p5km`
- `{FF}`: two-digit forecast hour, `00`–`48` for ARW and `00`–`60` for FV3

Per cycle this gives 49 files per ARW grid and 61 per FV3 grid, **220 GRIB2 files in total**, plus their sidecars.

BUFR soundings per cycle:
- `hiresw.t{CC}z.guam{arw|fv3}.bufrsnd.tar.gz`: bundled soundings
- `hiresw.t{CC}z.guam{arw|fv3}.class1.bufr`
- `bufr_guam{arw|fv3}.t{CC}z/`: per-station files (five stations per core) plus a `bufrloc` station list

Examples:
```bash
# List one cycle's Guam files
curl -s --http1.1 https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/hiresw.YYYYMMDD/ | grep 't12z.*guam'

# Fetch a single forecast hour (FV3, 5 km, F24, 12 UTC run)
curl -O https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/hiresw.YYYYMMDD/hiresw.t12z.fv3_5km.f24.guam.grib2
```

Use `--http1.1` for directory listings. NOMADS index pages send a malformed `Content-Length` header that curl rejects over HTTP/2: the request returns 200 with an empty body. See the [RRFS entry](./rrfs.md#data-availability).

### Publication timing
On the 12 UTC cycle of 2026-11-03:
- FV3 ran from f00 at 13:25 UTC to f60 at 14:24 UTC.
- ARW ran from f00 at 13:48 UTC to f48 at 14:39 UTC.

So FV3 lands first even though it runs longer.

---

## Relationship to other models
- **[GFS](../../global/usa/gfs.md):** Supplies the initial and lateral boundary conditions for both cores.
- **[RRFS](./rrfs.md) / [REFS](../../../ensemble_models/regional/usa/refs.md):** Replaced the retired HiresW domains on November 3, 2026, but cover North America, Hawaii and Puerto Rico only. **Neither has a Guam domain**, which is why this one survives.
- **[NBM](./nbm.md):** SCN 26-89 replaced the HiresW inputs to the NBM with RRFS and REFS for the CONUS, Alaska, Hawaii and Puerto Rico domains only. It does not say whether HiresW Guam feeds any NBM Guam guidance (TBD).

---

## Status
- **Operational.** The Guam domain is the only HiresW domain produced since **November 3, 2026, 12 UTC**. The 12 UTC cycle that day ran normally on both cores, with the full file set.
- **Scope of the surviving tree.** SCN 26-47 (AAB update) scopes the withdrawal of the NOMADS tree as everything under `/pub/data/nccf/com/hiresw/prod/` **`EXCEPT *guam*`**. The directory therefore survives with only Guam files in it. The AAC update of October 2, 2026 prints the exception as "EXCEPT \*guam\*jake". That is stray text, not a change of scope: every other Guam statement in the notice is unchanged.
- **Future.** No formal notification about the Guam domain's future exists. NCO's HiresW code page describes the code as deprecated, to be removed "when the application retires", and that page still covers the running Guam configuration (see Notes).

---

## Notes
- **The 2.5 km and 5 km files are not two resolutions of the same product.** The 2.5 km grid:
  - covers a smaller area;
  - has no pressure levels;
  - carries a different, NDFD-style parameter set.

  Code ported from the retired domains, where the finer grid did carry upper-air fields, will not find them here. Use the 5 km grid for anything above the surface.
- **The 2.5 km files come in 3-hour blocks.** At forecast hours divisible by 3, the file bundles the valid hour plus the two preceding hourly steps for instantaneous fields. For example, the f24 file carries hours 22–24 and the f12 file carries 10–12. Off-block hours contain only their own valid hour. The 5 km files always contain a single valid hour. Check `forecast_time` / `valid_time` when subsetting (re-verified 2026-11-03 on both cores).
- **Identifying the core from the GRIB2 header:** `generatingProcessIdentifier` is **116** for ARW and **84** for FV3. Both use centre `kwbc`, `productionStatusOfProcessedData` 0, `shapeOfTheEarth` 6 and complex packing with spatial differencing.
- **The files are not limited to Guam on NOMADS just after the cutover.** Directories dated before the 12 UTC cycle of November 3, 2026 still hold output from the retired domains until they age out. Filter on the `guam` token rather than assuming every file in the tree is Guam.
- **The HiresW model software is published as-is.** NCO has posted the code at https://www.nco.ncep.noaa.gov/pmb/codes/nwprod/hiresw.v8.1.15/, marked deprecated, with no warranty, bug fixes or support. It covers the whole HiresW codebase, including the running Guam configuration.
- The FV3 core replaced the NMMB core. Older NCEP documentation sometimes labels the second core with NMMB-era terminology.

---

## Recent version history
- **v8.1.15** — current code version (NCO code archive). From **November 3, 2026 at 12 UTC** this version runs the Guam domain only; the CONUS, Alaska, Hawaii and Puerto Rico domains were retired under SCN 26-47 as RRFSv1 and REFS became operational.

---

## Official documentation
- NCEP product page: https://www.nco.ncep.noaa.gov/pmb/products/hiresw/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hiresw/prod/
- NOMADS GRIB filter (Guam): https://nomads.ncep.noaa.gov/gribfilter.php?ds=hiresguam
- NWS SCN 26-47, AAC update of October 2, 2026 (termination of the CONUS/AK/HI/PR HiresW domains, Guam preserved; effective November 3, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-47_Updated_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS_aac.pdf
- NWS SCN 26-47, AAB update of September 9, 2026 (superseded; the last revision printing the Guam exception cleanly as `*guam*`): https://www.weather.gov/media/notification/pdf_2026/SCN26-47_Updated_Retire_NAM_SREF_HREF_HiresW_NAM_MOS.aab.pdf
- NWS PNS 25-41 (legacy model cessation; Guam exempted): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- HiresW model software, deprecated and provided as-is: https://www.nco.ncep.noaa.gov/pmb/codes/nwprod/hiresw.v8.1.15/
- WRF-ARW (NCAR): https://www.mmm.ucar.edu/models/wrf
- FV3 (NOAA/GFDL): https://www.gfdl.noaa.gov/fv3/
