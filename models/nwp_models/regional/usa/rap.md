# RAP (Rapid Refresh)

## What this model is
The Rapid Refresh (RAP) is NOAA's hourly-updating mesoscale numerical weather prediction model covering North America, providing short-range deterministic guidance for rapidly-evolving weather such as fronts, wind shifts, low-level temperature changes, and aviation-relevant atmospheric conditions.

RAP serves three operationally important roles:
1. **Standalone forecast system** — providing 13 km mesoscale forecasts updated every hour, used widely for aviation forecasting, mesoscale analysis, and short-range decision support
2. **Parent model for HRRR** — supplying lateral boundary conditions and first-guess fields to the convection-allowing [HRRR](./hrrr.md) at 3 km
3. **Smoke forecast component for NAQFC** — RAP-Smoke output is the operational smoke component of NOAA's National Air Quality Forecast Capability since June 2022

The current operational version is **RAPv5** (operational since 2 December 2020), which is the **final version** of RAP. No further upgrades are planned; the system has been frozen since the RAPv5/HRRRv4 implementation alongside the broader transition to the Unified Forecast System (UFS) framework. RAP is expected to be retired with the eventual transition to RRFSv2 (see [Future outlook](#future-outlook)).

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL); operated at NCEP

---

## What area it covers
- **Coverage:** North America
- **Domain details:** The distributed full-domain files (`wrfnat`, `wrfprs`, `wrfmsl`) are encoded on **NCEP local grid definition template 3.32769** (rotated latitude–longitude, Arakawa staggered E-grid) at **953 × 834 = 794,802 points**, first grid point 10.591°S / 220.914°E. Live-decoded with ecCodes 2.48.0 on 2026-07-30 03 UTC output.

> ⚠️ **Domain discrepancy.** Earlier revisions of this entry described the domain as "759 × 568 grid points on a Lambert conformal projection." No distributed RAP file carries that geometry. The Lambert figure most likely refers to the model's internal computational grid rather than the distribution grid, but this could not be confirmed from any live product — treat the computational-grid geometry as **TBD** and the 953 × 834 rotated lat-lon figure above as the authoritative *distribution* geometry.

> **Practical warning on template 3.32769.** This is an NCEP-local template, not a WMO standard one. ecCodes reports it as `gridType = ncep_32769`; many GRIB2 readers, regridding utilities, and cloud-native indexers either fail on it or silently mishandle the E-grid staggering. Users who only need a rectilinear product should work from one of the AWIPS output grids below instead of `wrfnat`/`wrfprs`.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** WRF-ARW (Advanced Research WRF, version 3.x)
- **Dynamical formulation:** Non-hydrostatic, fully compressible, on a mass-based (sigma-pressure hybrid) vertical coordinate
- **Convection-allowing:** No (deep convection is parameterized via Grell-Freitas at 13 km resolution)
- **Native horizontal resolution:** 13 km
- **Vertical levels:** 50
- **Vertical coordinate:** Hybrid sigma-pressure
- **Model top:** 10 hPa
- **Forecast length (live-verified):**
  - **51 hours** (52 steps, f00–f51) for the 03, 09, 15, 21 UTC cycles — the four "extended" cycles
  - **21 hours** (22 steps, f00–f21) for the other 20 cycles
- **Update frequency:** Hourly (24× daily)
- **Temporal output resolution:** Hourly (step deltas verified uniform at 1 h across all products)
- **Observed publication latency:** ~**T+1 h 26 m** for the first step of a cycle and ~**T+1 h 33 m** for the last step of a 21-hour cycle, measured on the 2026-07-31 12 UTC cycle via S3 `Last-Modified`.

---

## Data assimilation
RAP runs an **hourly cycling Gridpoint Statistical Interpolation (GSI) hybrid ensemble-3DVar** data assimilation system, with flow-dependent background-error covariances drawn from the GDAS-EnKF ensemble that informs the deterministic [GFS](../../global/usa/gfs.md):

- **Method:** Hybrid 3DVar combining a static background-error covariance with flow-dependent covariances from the 80-member GDAS-EnKF ensemble
- **Cycle structure:** RAP runs a continuous hourly DA cycle, with each cycle's analysis serving as the first guess for the next hour
- **Partial cycling:** Twice daily (typically 09 and 21 UTC), RAP partially cycles back to GDAS-derived initial conditions to absorb global-scale information from the deterministic GFS analysis. This refreshes the regional analysis with global atmospheric structure that may not have been captured during continuous hourly cycling.

### Assimilated observations
- Conventional surface, aircraft, and radiosonde data
- Satellite radiances (AMSU-A, ATMS, IASI, AIRS, CrIS) and atmospheric motion vectors
- GPS-RO refractivity profiles
- Mesonet surface observations (uniquely valuable at RAP's resolution)
- **Cloud and hydrometeor analysis** during the 1-hour pre-forecast spin-up — a distinctive RAP/HRRR feature that initializes ongoing precipitation systems from observed reflectivity, lightning, and satellite cloud retrievals
- Radar reflectivity (used for digital filter initialization, distinct from HRRR's full radar DA)

The cloud and hydrometeor analysis significantly improves short-range cloud and precipitation forecasts compared to a clean cold-start from upstream model fields.

---

## Initial and boundary conditions
- **Initial conditions:** Hourly RAP cycling analysis (continuous), with twice-daily partial cycling from GFS-derived backgrounds
- **Lateral boundary conditions:** GFS, with boundaries updated each cycle from the most recent available GFS forecast

---

## What it provides
Deterministic short-range forecasts of:
- Temperature, wind, humidity, and pressure (surface and upper-air)
- Cloud cover, ceilings, and visibility (with strong skill for low ceilings due to the hourly cloud analysis)
- Precipitation, precipitation type, and accumulation
- Aviation-relevant fields including icing potential, turbulence, and tropopause diagnostics
- Convective indices (CAPE, CIN, lifted index)
- Wildfire smoke fields (near-surface smoke concentration and vertically integrated smoke), with smoke aerosols feeding back on shortwave radiation and the atmospheric forecast
- Boundary-layer diagnostics, surface fluxes, and short-range mesoscale guidance

RAP analyses are also used as initial conditions for HRRR's hybrid data assimilation system (where they serve as the first guess into HRRR's own DA cycle, rather than as direct ICs).

---

## Relationship to other models
- **[HRRR](./hrrr.md):** RAP supplies the lateral boundary conditions and first-guess fields for HRRR. HRRR runs its own hourly data assimilation (using the 36-member HRRRDAS ensemble in HRRRv4) but inherits global-scale information from RAP through this background coupling. The two systems share much of the same physics suite and DA infrastructure.
- **[GFS](../../global/usa/gfs.md):** Provides the lateral boundary conditions for RAP and the partial-cycling source for RAP's twice-daily DA refresh.
- **[NBM](./nbm.md):** RAP is one of the deterministic inputs blended into the National Blend of Models for short-range guidance.
- **[RRFS](./rrfs.md):** Future replacement for RAP and HRRR. RRFSv1 (operational October 6, 2026) will not retire RAP — RAP retirement is tied to the future RRFSv2 (MPAS-based) timeline.

### NAQFC integration
RAP-Smoke (the wildfire smoke component integrated into operational RAPv5 since December 2020) became the **operational smoke component of NOAA's National Air Quality Forecast Capability (NAQFC)** on 28 June 2022, replacing HYSPLIT. HYSPLIT now provides only dust forecasts within NAQFC. This makes RAP doubly important operationally: not just as a weather forecast system, but as the underlying smoke-prediction infrastructure for U.S. air quality forecasting.

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:** **Public domain (U.S. government work; CC0-equivalent).** Distributed via NOAA Open Data Dissemination (NODD): data are open to the public and may be used as desired. NOAA requests attribution, prohibits stating or implying NOAA endorsement or affiliation, and requires that modified data not be presented as unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (with `.idx` index sidecars — but see the GCS caveat below), plus a gzipped TAR bundle of BUFR soundings per cycle

### Product inventory

Live-verified against `rap.20260730/` on 2026-07-30. Sizes and step counts are for an **extended (03/09/15/21 UTC) cycle**; standard cycles carry 22 steps instead of 52 with the same per-step sizes. Grid geometry decoded from GRIB2 Section 3 with ecCodes.

| File family | AWIPS grid | Projection / geometry | Records | Size/step |
|---|---|---|---|---|
| `rap.tCCz.wrfnatfFF.grib2` | native | rotated lat-lon E-grid (`ncep_32769`), 953 × 834 | 1090 | 317 MB |
| `rap.tCCz.wrfprsfFF.grib2` | native | rotated lat-lon E-grid (`ncep_32769`), 953 × 834 | 791 | 241 MB |
| `rap.tCCz.wrfmslfFF.grib2` | native | rotated lat-lon E-grid (`ncep_32769`), 953 × 834 | — | 51 MB |
| `rap.tCCz.awp130pgrbfFF.grib2` | 130 | Lambert conformal, 451 × 337, 13.545 km | 355 | 18.5 MB |
| `rap.tCCz.awp130bgrbfFF.grib2` | 130 | Lambert conformal, 451 × 337, 13.545 km | 1011 | 41.9 MB |
| `rap.tCCz.awip32fFF.grib2` | 221 | Lambert conformal, 349 × 277, 32.463 km | 596 | 20.1 MB |
| `rap.tCCz.awp252pgrbfFF.grib2` | 252 | Lambert conformal, 301 × 225, 20.318 km | 343 | 9.9 MB |
| `rap.tCCz.awp252bgrbfFF.grib2` | 252 | Lambert conformal, 301 × 225, 20.318 km | — | 16.8 MB |
| `rap.tCCz.awp236pgrbfFF.grib2` | 236 | Lambert conformal, 151 × 113, 40.635 km | 343 | 3.6 MB |
| `rap.tCCz.awp242fFF.grib2` | 242 | polar stereographic (Alaska), 553 × 425, 11.25 km | 590 | 27.4 MB |
| `rap.tCCz.awp243fFF.grib2` | 243 | regular lat-lon (E. N. Pacific), 126 × 101, 0.4°, 10–50°N / 190–240°E | 330 | 3.5 MB |
| `rap.tCCz.awp200fFF.grib2` | 200 | Lambert conformal (Puerto Rico), 108 × 94, 16.232 km | 330 | 1.2 MB |
| `rap.tCCz.bufrsnd.tar.gz` | — | station soundings, one bundle per cycle | — | 55 MB |

**Total volume: ≈ 484 GB per day** (15,588 objects), dominated by `wrfnat` (203 GB/day) and `wrfprs` (154 GB/day). The `p`/`b` suffixes on grids 130 and 252 distinguish primary and secondary parameter sets; note that on grid 130 the `bgrb` file is the larger of the two.

### Smoke products

Separate from the smoke variables carried inside the standard output, RAP emits **dedicated smoke files on three NDFD grids** — and these behave differently from everything else in the tree:

| File | AWIPS grid | Projection / geometry | Variable |
|---|---|---|---|
| `rap_smokeCS.t03z.{sfc,pbl}.1hr_227.grib2` | 227 | Lambert conformal, 1473 × 1025, 5.079 km (CONUS) | see below |
| `rap_smokeAK.t03z.{sfc,pbl}.1hr_198.grib2` | 198 | polar stereographic, 825 × 553, 5.953 km (Alaska) | see below |
| `rap_smokeHI.t03z.{sfc,pbl}.1hr_196.grib2` | 196 | Mercator, 321 × 225, 2.5 km (Hawaii) | see below |

- **`sfc` files** carry `MASSDEN` at 8 m above ground — near-surface smoke mass density
- **`pbl` files** carry `COLMD` over the entire atmosphere — vertically integrated (column) smoke mass density

> **These are produced at the 03 UTC cycle only.** Verified stable across 2025-08-01, 2026-02-01, 2026-06-01, 2026-07-15, 2026-07-29 and 2026-07-30: exactly six smoke files per day, all `t03z`, never at 09/15/21 UTC despite those also being extended cycles. Each file is a **single GRIB2 containing all 52 time steps** as separate messages (analysis plus 1–51 hour forecasts), rather than one file per step as everywhere else in the tree.

Smoke variables *also* appear inside the standard products — `wrfnat` carries 52 smoke-related records per step, `wrfprs` two, and `awp130pgrb` one — so the dedicated files are a repackaging onto NDFD grids for downstream NAQFC use, not the only route to smoke fields.

### Distribution channels

**1. NOMADS (NCEP operational distribution)**

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/rap/prod/rap.YYYYMMDD/
```

**GRIB filter (parameter/level/region subsetting over HTTP)** — four RAP datasets are advertised on the NOMADS index:

```
https://nomads.ncep.noaa.gov/cgi-bin/filter_rap.pl       (ds=rap)
https://nomads.ncep.noaa.gov/cgi-bin/filter_rap32.pl     (ds=rap32)
https://nomads.ncep.noaa.gov/cgi-bin/filter_rap242.pl    (ds=rap242)
https://nomads.ncep.noaa.gov/cgi-bin/filter_rap243.pl    (ds=rap243)
```

> **Retention is not the ~10 days previously stated here, and could not be fully verified.** All four GRIB filter pages offered only **two** directory options on 2026-07-31 (`rap.20260730`, `rap.20260731`). The `/pub/data/` directory listing returned persistent HTTP 502 from the verification environment, so the depth of the underlying tree is **TBD** — two days is a confirmed lower bound, not the retention figure. Given RAP's ~484 GB/day volume a short window is plausible. Anyone needing more than a couple of days should use AWS or GCS regardless.

> Both the OPeNDAP (`/dods/`) and FTPPRD (`ftpprd.ncep.noaa.gov`, `ftp.ncep.noaa.gov`) routes were retired on **23 February 2026** under SCN 25-81 and SCN 25-82 respectively. Neither is a valid RAP access path any more.

**2. AWS Open Data (NODD) — real-time + complete archive**

- **S3 bucket:** `s3://noaa-rap-pds/` — ARN `arn:aws:s3:::noaa-rap-pds`, region **`us-east-1`**
- **Browser access:** https://noaa-rap-pds.s3.amazonaws.com/index.html
- **AWS CLI (no account required):** `aws s3 ls --no-sign-request s3://noaa-rap-pds/`
- **SNS new-object notifications:** `arn:aws:sns:us-east-1:123901341784:NewRAPObject` (Lambda and SQS protocols only)
- **Registry:** https://registry.opendata.aws/noaa-rap/

Archive: `rap.YYYYMMDD/` from **2021-02-22** to present — **1,986 directories with zero calendar gaps** across the full 2021-02-22 → 2026-07-31 span (verified by date enumeration). This is the most complete RAP archive of the three clouds.

**3. Microsoft Azure (NODD) — real-time, 90-day rolling window**

- **Blob container:** `https://noaarap.blob.core.windows.net/rap` — public, anonymous, no SAS token required
- **Region:** East US
- **Read-only SAS token API (for BlobFuse mounts):** `https://planetarycomputer.microsoft.com/api/sas/v1/token/noaarap/rap`
- **Documentation:** https://microsoft.github.io/AIforEarthDataSets/data/noaa-rap.html
- **Planetary Computer dataset page:** https://planetarycomputer.microsoft.com/dataset/storage/noaa-rap

Live window on 2026-07-31: **91 days** (`rap.20260502/` → `rap.20260731/`). Object counts match AWS exactly (15,588 for 2026-07-30), so Azure is a complete but shallow mirror.

> ⚠️ **Two documentation-vs-reality discrepancies on Azure (both live-verified 2026-07-31):**
>
> 1. **Retention.** The Microsoft dataset page states "Data are retained for 30 days." The live container holds **91 days** — the same 90-day window observed on the Azure [GFS](../../global/usa/gfs.md#data-availability) container.
> 2. **Path pattern.** The page documents blob names as `rap.[date]/[product][date]/rap.t[CC]z.[variable]f[FH].grib2`, i.e. with an intermediate `[product][date]/` directory. **No such directory exists** — the live layout is a flat `rap.YYYYMMDD/rap.tCCz.<variable>f<FH>.grib2`. The page's own worked example contradicts its own pattern. Code written to the documented pattern will 404.

**4. Google Cloud Storage (NODD) — real-time + near-complete archive**

- **Bucket:** `gs://rapid-refresh/` — anonymous object read and JSON-API object listing; storage class `STANDARD`
- **HTTPS object access:** `https://storage.googleapis.com/rapid-refresh/rap.YYYYMMDD/…`
- **JSON API listing:** `https://storage.googleapis.com/storage/v1/b/rapid-refresh/o?prefix=…&delimiter=/`
- **Marketplace page:** https://console.cloud.google.com/marketplace/product/noaa-public/rapid-refresh

Archive: 2021-02-22 → present, **1,982 directories**.

> ⚠️ **GCS has a four-day archive hole and is missing index sidecars.**
>
> 1. **Missing dates:** `rap.20211231/`, `rap.20220101/`, `rap.20220102/`, `rap.20220103/` are present on AWS and absent from GCS. These are the only date-level differences between the two buckets in either direction.
> 2. **Missing `.idx`:** for the 2026-07-30 day, GCS holds 14,286 objects against AWS's 15,588 — a shortfall of exactly **1,302 files, all of them `.idx` sidecars**. Every GRIB2 data file is present. The gap is systematic and covers the whole archive: **`wrfnat` (648/day), `wrfmsl` (648/day), and all six smoke files** have no index on GCS. The AWIPS-grid products do carry their indexes there.
>
> **Consequence:** `.idx`-driven byte-range subsetting of the native-level and MSL products (cfgrib/kerchunk/`wgrib2 -i` workflows) does not work against GCS. Use AWS or Azure, or build your own index.

> Note that AWS itself only began writing `.idx` sidecars for `wrfnat`/`wrfmsl` on **2024-04-14** (absent 2024-04-13, present 2024-04-14). Pre-April-2024 cycles have no native-level index on any cloud.

**5. NCEI — long-term archive**

- https://www.ncei.noaa.gov/products/weather-climate-models/rapid-refresh-update

### Other prefixes in the RAP buckets

Both the AWS and GCS buckets carry two additional top-level prefixes that are **not** RAP forecast output and should not be mistaken for it:

- **`rap_e.YYYYMMDD/`** — the observation dumps feeding RAP's early analysis cycle (`prepbufr`, `1bamua`, `1bhrs4`, `1bmhs`, `satwnd`, `nexrad`, `lgycld`). These are BUFR observation files, not gridded model output, and therefore fall outside this repository's scope. The stream is **frozen**: 2021-02-22 → 2022-06-28 on both clouds, with current-date directories empty. Absent from Azure.
- **`narre.YYYYMMDD/`** — a two-day stub (2020-12-01, 2020-12-02) only. Not a usable NARRE archive. Absent from Azure. NARRE itself is scheduled for retirement on October 6, 2026 under SCN 26-47.

### Cross-cloud equivalence

The GRIB2 data files are **byte-identical** across all three clouds. For `rap.20260730/rap.t03z.awp236pgrbf01.grib2`:

| Cloud | Size | Checksum |
|---|---|---|
| AWS | 3,606,315 | ETag `20482880d8fe4d437de057fff9496aeb` |
| Azure | 3,606,315 | Content-MD5 `IEgogNj+TUN94Ff/+Ulq6w==` |
| GCS | 3,606,315 | md5Hash `IEgogNj+TUN94Ff/+Ulq6w==` |

All three serve **anonymous HTTP 206 byte-range requests** with `GRIB` magic at byte 0.

**Choose on archive completeness and locality:** AWS for anything requiring the full archive or native-level indexes; GCS as an equivalent alternative outside the four missing days and where `wrfnat`/`wrfmsl` indexes are not needed; Azure only for the last ~90 days.

---

## Notes
- RAP and [HRRR](./hrrr.md) share much of the same modeling and assimilation infrastructure, including a similar physics suite, the GSI hybrid DA framework, and the cloud/hydrometeor initialization scheme. They are best understood as a paired mesoscale + storm-scale system rather than independent models.
- Unlike HRRR, RAP uses a cumulus parameterization (Grell-Freitas scheme with sub-grid downdrafts and stochastic convective triggering). The 13 km resolution does not resolve individual convective storms.
- RAP's hourly cycling, mesonet observation use, and cloud analysis make it especially well-suited for aviation forecasting, low-ceiling/visibility prediction, and short-range mesoscale analysis. It is one of the most heavily-used operational mesoscale models in U.S. aviation operations.
- **RAPv5 is the final version of RAP.** No further upgrades are planned. The system has been operationally frozen since 2 December 2020 and is in maintenance-only status pending eventual retirement.
- RAP-Smoke is no longer a separate experimental run — wildfire smoke prediction has been integrated into operational RAPv5 since December 2020, with smoke aerosols affecting radiation and feeding back into the meteorological forecast. This same RAPv5/HRRRv4 upgrade integrated smoke into HRRR as well. The dedicated NDFD-grid smoke files documented above are a 03 UTC-only repackaging on top of that integration.
- **RAP is an unusually heavy dataset for its resolution** — ~484 GB/day, more than the global [GFS](../../global/usa/gfs.md) atmos tree produces in a single cycle. Nearly three-quarters of that is the two full-domain native/pressure-level products. Users who need only CONUS pressure-level fields should take `awp130pgrb` (18.5 MB/step) rather than `wrfprs` (241 MB/step).

---

## Open questions / outreach
- **NOMADS retention depth for `rap/prod`:** GRIB filter exposes two days; directory listing unreachable during verification. What is the committed window? → `nodd@noaa.gov` / NCEP dataflow
- **Smoke at 03 UTC only:** is the single-cycle cadence intentional (NAQFC's daily input requirement) or a residue of the experimental RAP-Smoke schedule? → NOAA GSL
- **GCS missing `.idx` for `wrfnat`/`wrfmsl`:** systematic across the whole archive. Sync-configuration gap or deliberate? → `nodd@noaa.gov`
- **GCS missing 2021-12-31 → 2022-01-03:** four-day hole absent from AWS. Backfillable? → `nodd@noaa.gov`
- **Native computational grid geometry:** distributed files are 953 × 834 rotated lat-lon; the "759 × 568 Lambert" figure in circulation is unattributed. Which describes the model's internal grid? → NOAA GSL
- **`rap_e.` frozen since 2022-06-28:** closed archive or stalled feed? → `nodd@noaa.gov`

---

## Future outlook

RAP is expected to be retired alongside [HRRR](./hrrr.md) as part of the eventual transition to **RRFSv2** (the MPAS-based version of the [Rapid Refresh Forecast System](./rrfs.md)). Unlike the NAM, NAM Nest, HiresW (non-Guam), HREF, and SREF — which are scheduled for retirement on October 6, 2026 under [NWS Service Change Notice 26-47](https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf) (termination notice, updated July 6, 2026; RRFS/REFS implementation under companion [SCN 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf); originally proposed under PNS 25-41) — **RAP and HRRR are explicitly not part of the RRFSv1 retirement wave**. Both are expected to continue operating in parallel with RRFSv1 until the RRFSv2 transition.

No formal Service Change Notice for RAP retirement has been issued as of July 2026. The RRFSv2 timeline itself has not been publicly committed; it depends on resolution of issues identified in RRFSv1 evaluation (notably tropical cyclone tracking and convective precipitation overprediction with the FV3 core, which are intended to be addressed by the MPAS dynamical core in v2).

---

## Recent version history

### RAPv5 — operational 2 December 2020 (current; final version)
The last RAP upgrade. Joint implementation with HRRRv4. Headline changes:
- **Wildfire smoke prediction** integrated into operational RAP (previously experimental as RAP-Smoke) — smoke aerosols feed back on shortwave radiation and the atmospheric forecast
- Improved cloud representation for boundary-top clouds, especially shallow cold-air layers
- Better representation of cloud bands (snow squalls, hurricane bands, lake-effect bands)
- Updated physics suite consistent with HRRRv4
- Improved data assimilation including better surface observation handling
- Subsequent maintenance updates have been bug fixes only — no scientific changes

### RAPv4 — operational August 2016
Earlier major upgrade introducing significant DA enhancements including extended mesonet observation use and improved 2 m temperature handling. Documented in Benjamin et al. (2016).

### Earlier history
RAP became operational at NCEP in May 2012, replacing the long-running **Rapid Update Cycle (RUC)** model that had been operational since 1994. RUC ran at successively finer resolutions (60 km in 1994, 40 km in 1998, 20 km in 2002, 13 km in 2008) before transitioning to the WRF-ARW-based RAP. RAP-Smoke was first introduced experimentally in 2016 before becoming part of operational RAPv5 in 2020.

---

## Official documentation
- RAP home page (NOAA GSL): https://rapidrefresh.noaa.gov/
- RAP at EMC: https://emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/rap.php
- RAP product inventory (NCO): https://www.nco.ncep.noaa.gov/pmb/products/rap/
- RAP FAQ: https://rapidrefresh.noaa.gov/RR.faq.html
- NOMADS help and GRIB filter migration guide: https://nomads.ncep.noaa.gov/info.php?page=help
- NWS Service Change Notice 20-46 (RAPv5/HRRRv4 implementation): https://www.weather.gov/media/notification/pdf2/scn20-46rap_v5_hrrr_v4_aab.pdf
- NWS PNS 25-41 (RRFS legacy model cessation, NOT including RAP): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- AWS Open Data registry: https://registry.opendata.aws/noaa-rap/
- Azure / AIforEarthDataSets: https://microsoft.github.io/AIforEarthDataSets/data/noaa-rap.html
- Google Cloud Marketplace: https://console.cloud.google.com/marketplace/product/noaa-public/rapid-refresh

### Key references
- Benjamin, S. G., et al. (2016). *A North American Hourly Assimilation and Model Forecast Cycle: The Rapid Refresh.* Mon. Wea. Rev., 144, 1669–1694. https://doi.org/10.1175/MWR-D-15-0242.1
- James, E. P., et al. (2019). *Rapidly-updating high-resolution predictions of smoke, visibility, and smoke-weather interactions using satellite fire products within the Rapid Refresh and High-Resolution Rapid Refresh coupled with smoke (RAP/HRRR-smoke).* 35th Conf. on Environmental Information Processing Technologies, Phoenix, AZ.
