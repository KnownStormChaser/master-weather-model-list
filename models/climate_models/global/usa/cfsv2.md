# CFSv2 (Climate Forecast System version 2)

## What this model is
CFSv2 is NCEP's operational global long-range forecasting system — a fully coupled atmosphere–ocean–land–sea-ice model that runs four coupled forecasts per day out to 9 months. Operational since March 2011, it underpins NWS seasonal guidance and contributes to multi-model seasonal ensembles. The distributed data is raw coupled model output (6-hourly fields, per-variable time series, and monthly means); anomaly and probability products are computed downstream rather than distributed here.

Note that CFSv2 is an *operational seasonal prediction* system, not a climate-projection model: it issues initialized, calendar-dated forecasts on a continuous operational cycle.

---

## Who runs it
- **Organization:** NOAA / NWS / NCEP — Environmental Modeling Center (EMC); disseminated via NOMADS and NOAA Open Data Dissemination (NODD), archived at NCEI
- **Country / region:** United States
- **Coordinating body / programme:** Standalone NCEP system; contributes as a member to the [North American Multi-Model Ensemble (NMME)](../multi-national/nmme.md), as `system=2` in the [C3S seasonal multi-system](../multi-national/c3s-seasonal.md), and provides input to NWS/CPC operational seasonal outlooks

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Native spectral atmosphere at T126 (~0.937°, ~100 km); products distributed on regular lat-lon grids down to 0.5° (~56 km)

---

## Basic details
- **Model type:** Long-range coupled forecast system — time-lagged ensemble of deterministic coupled runs; single model
- **Model system / core:** CFSv2 — a GFS-based atmosphere coupled to the GFDL MOM4 ocean, with interactive sea ice and the Noah land-surface model
- **Range class:** Sub-seasonal to seasonal (out to 9 months)
- **Initialization cadence:** Four cycles per day (00/06/12/18 UTC)
- **Ensemble generation:** Time-lagged — the four daily coupled runs are accumulated over successive cycles to build a seasonal ensemble (CPC commonly forms ~40 members from the most recent runs)
- **Ensemble size:** 16 CFS runs per day — four members per cycle (`01`–`04`), where `01` is the long control run and `02`–`04` are perturbed
- **Forecast length (live-verified from the 2026-07-30 cycles):**

  | Member | Cycle | 6-hourly fields | Per-variable time series | Monthly means |
  |---|---|---|---|---|
  | `01` (control) | all four | to **+4464 h (~186 d, ~6 months)** | to **+7344 h (~306 d, ~10 months)** | 11 target months |
  | `02`–`04` | 00 UTC | to ~+94 d (one season) | — | 4 target months |
  | `02`–`04` | 06/12/18 UTC | to **exactly +45 days** | — | — |

  > **The 9-month control run is not fully covered by the `6hrly_grib_01` directory.** Those files stop at ~6 months. The `time_grib_01` per-variable series run to +7344 h, and the monthly means to 11 target months. Anyone needing 6-hourly control fields beyond ~6 months must use `time_grib_01`, not `6hrly_grib_01`.

- **Temporal output resolution:** 6-hourly (both the `6hrly_grib_*` fields and the `time_grib_*` series), plus monthly and diurnally-stratified monthly means. **No hourly product is distributed** — see *Notes*.
- **Output aggregation levels:** 6-hourly forecast fields, 6-hourly per-variable time series, monthly means, and monthly means stratified by synoptic hour (00Z/06Z/12Z/18Z)

---

## Coupled configuration
- **Atmosphere:** GFS-based spectral model, T126 (~100 km), 64 hybrid sigma-pressure levels
- **Ocean:** GFDL MOM4, ~0.25° meridionally in the tropics relaxing to 0.5°, 40 vertical levels
- **Sea ice:** Interactive sea-ice model within the coupled framework
- **Land surface:** Noah land-surface model (4 soil layers)
- **Coupling notes:** Fully coupled with no flux correction; ocean–atmosphere coupled at the model time step

---

## Initialization
- **Atmosphere IC:** CFSv2 real-time coupled data assimilation system (the CDAS / GSI-based atmospheric analysis)
- **Ocean IC:** Global Ocean Data Assimilation System (GODAS)
- **Sea ice / land IC:** From the coupled CFSv2 analysis cycle
- **Perturbation method:** Lagged initialization across cycles, plus perturbed members in the season- and 45-day runs
- **Analysis files shipped alongside forecasts:** each `6hrly_grib_NN` directory contains three analysis files at the initialization time — `pgbanl` (pressure-level, ~24 MB), `ipvanl` (isentropic PV, ~5.4 MB), and `splanl` (spectral, ~0.4 MB)

---

## Hindcasts (reforecasts)
- **Hindcast period:** Retrospective forecasts spanning approximately 1982–2010, used to define the model climatology and to calibrate real-time anomaly and probability products. *(Flag: confirm the exact span and start-date cadence against Saha et al., 2014.)*
- **Hindcast cadence / ensemble size:** 9-month reforecasts initialized at a reduced cadence (every few days), with more frequent shorter reforecasts.
- **Reference climatology period:** Derived from the reforecast set (see flag above).
- **Distributed alongside forecasts?** Separately — the reforecast/retrospective dataset is archived at NCEI rather than in the NOMADS rolling production directory, and is **not** present in any of the three NODD cloud buckets (verified: the buckets contain only `cfs.*` and `cdas.*` prefixes).

---

## Sources of predictability
Skill derives chiefly from the coupled ocean–atmosphere state (ENSO is the dominant seasonal driver), with the MJO contributing on sub-seasonal scales and land/soil-moisture memory adding regional signal.

---

## What it provides
Raw coupled forecast fields across the standard CFS output streams, all in GRIB2 with `.idx` index sidecars.

**Directory structure** (`cfs.YYYYMMDD/CC/`, live-verified 2026-07-30):

| Directory | Present at | Contents |
|---|---|---|
| `6hrly_grib_01` … `6hrly_grib_04` | all four cycles | 6-hourly full fields per member |
| `time_grib_01` … `time_grib_04` | all four cycles | per-variable 6-hourly time series |
| `monthly_grib_01` | all four cycles | monthly means, control member |
| `monthly_grib_02` … `monthly_grib_04` | **00 UTC only** | monthly means, perturbed members |

**6-hourly file families** — `<stream><validtime>.<member>.<inittime>.grb2`:

| Stream | Content | Size/step (member 01) |
|---|---|---|
| `pgbf` | pressure-level atmosphere | 22.1 MB |
| `flxf` | surface fluxes | 4.1 MB |
| `ipvf` | isentropic potential vorticity | 4.7 MB |
| `ocnf` | ocean fields | 9.7 MB |

**Per-variable time series** — `time_grib_NN/` holds **91 variables**, one GRIB2 file each:

`chi200`, `chi850`, `cprat`, `csdlf`, `csdsf`, `csusf`, `dlwsfc`, `dswsfc`, `gflux`, `icecon`, `icethk`, `ipv450`, `ipv550`, `ipv650`, `lhtfl`, `nddsf`, `ocndt2`, `ocndt5c`, `ocndt10c`, `ocndt15c`, `ocndt20c`, `ocndt25c`, `ocndt28c`, `ocnheat`, `ocnmld`, `ocnsal5`, `ocnsal15`, `ocnsild`, `ocnslh`, `ocnsst`, `ocnt15`, `ocntchp`, `ocnu5`, `ocnu15`, `ocnv5`, `ocnv15`, `ocnvv55`, `prate`, `pressfc`, `prmsl`, `psi200`, `psi850`, `pwat`, `q2m`, `q500`, `q700`, `q850`, `q925`, `runoff`, `shtfl`, `snohf`, `soilm1`–`soilm4`, `soilt1`, `t2`, `t50`, `t200`, `t250`, `t500`, `t700`, `t850`, `t1000`, `tcdcclm`, `tmax`, `tmin`, `tmp2m`, `tmphy1`, `tmpsfc`, `ulwsfc`, `ulwtoa`, `uswsfc`, `uswtoa`, `vddsf`, `vvel500`, `weasd`, `wnd10m`, `wnd200`, `wnd250`, `wnd500`, `wnd700`, `wnd850`, `wnd925`, `wnd1000`, `wndstrs`, `z200`, `z500`, `z700`, `z850`, `z1000`

> ⚠️ **The `.daily.` token in time-series filenames is misleading.** Files are named e.g. `tmp2m.01.2026073000.daily.grb2`, but decoding the index shows **1,224 records at 6-hour spacing** (+6 h through +7344 h) — the series are 6-hourly, not daily. Do not infer cadence from the filename.

**Monthly means** — `monthly_grib_NN/` holds five streams (`pgbf`, `flxf`, `ipvf`, `ocnf`, and `ocnh`, the last appearing only here), each as:
- 11 whole-month means: `<stream>.<member>.<inittime>.<YYYYMM>.avrg.grib.grb2`
- 44 diurnally-stratified means: the same with a `.00Z`/`.06Z`/`.12Z`/`.18Z` suffix — i.e. monthly means computed separately for each synoptic hour, useful for diurnal-cycle work

Pre-computed tercile/anomaly probability grids are **not** distributed through these channels — those exist only as CPC graphical outlook products (out of scope).

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:** **Public domain (U.S. government work; CC0-equivalent).** Distributed via NOAA Open Data Dissemination (NODD): data are open to the public and may be used as desired. NOAA requests attribution, prohibits stating or implying NOAA endorsement or affiliation, and requires that modified data not be presented as unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (`.grb2`), each file paired with a `.idx` index sidecar
- **Volume:** **51,880 objects and ≈309 GB per day**, dominated by `6hrly_grib_01` (123 GB) and `time_grib_01` (30 GB). The control member alone accounts for over half the daily volume.

### Distribution channels

**1. NOMADS (NCEP operational distribution) — real-time, 7-day rolling window**

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/cfs/prod/
  cfs.YYYYMMDD/{00,06,12,18}/{6hrly_grib_NN,time_grib_NN,monthly_grib_NN}/   ← forecast (in scope)
  cdas.YYYYMMDD/                                                            ← analysis (out of scope)
  monthly/cdas.YYYYMM/{monthly,time}/                                       ← analysis monthly means (out of scope)
```

Retention observed on 2026-07-31: **7 days** for both `cfs.*` and `cdas.*` (20260725–20260731).

> ⚠️ **The top-level `monthly/` directory contains only CDAS analysis output — it is out of scope.** Despite sitting in the CFS production tree, every directory beneath it is `cdas.YYYYMM/` (no `cfs.*` equivalent exists), and every file inside carries a `gdas` token — `chi200.gdas.202606.grib2`, `pgbf00.gdas.202606.12Z.grib2`, and so on. These are **monthly means of the CDAS analysis**, not of the forecast. Forecast monthly means live in the per-cycle `monthly_grib_NN/` directories described above, and nowhere else.
>
> Structure, for reference: each `cdas.YYYYMM/` splits into `monthly/` (monthly means of the `flx`, `ipv`, `ocn`, `pgb`, and `egyh` streams, each as an overall mean plus separate 00Z/06Z/12Z/18Z means) and `time/` (per-variable monthly series over the same ~91-variable list as the forecast `time_grib_*`, each variable present as a full-resolution file and a smaller `.l.` variant).
>
> **Retention here is far longer than elsewhere in the tree**: six monthly directories (2026-01 through 2026-06) against the 7-day window for the daily `cfs.*` and `cdas.*` directories. Each month is published on the 1st of the following month, around 18:00 UTC. None of it is mirrored to AWS, Azure, or GCS.

> Both the OPeNDAP (`/dods/`) and FTPPRD (`ftpprd.ncep.noaa.gov`, `ftp.ncep.noaa.gov`) routes were retired on **23 February 2026** under SCN 25-81 and SCN 25-82 respectively. Neither is a valid CFS access path any more.

**2. AWS Open Data (NODD) — real-time + the deepest forecast archive**

- **S3 bucket:** `s3://noaa-cfs-pds/` — ARN `arn:aws:s3:::noaa-cfs-pds`, region **`us-east-1`**
- **Browser access:** https://noaa-cfs-pds.s3.amazonaws.com/index.html
- **AWS CLI (no account required):** `aws s3 ls --no-sign-request s3://noaa-cfs-pds/`
- **SNS new-object notifications:** `arn:aws:sns:us-east-1:123901341784:NewCFSObject`
- **Registry:** https://registry.opendata.aws/noaa-cfs/

Archive: `cfs.YYYYMMDD/` from **2018-10-31** to present — **2,831 directories with zero calendar gaps**. `cdas.YYYYMMDD/` from 2023-04-22 (1,197 directories).

> **The AWS registry description is wrong about the bucket's contents.** It states that "the data in this bucket are the CFSv2 Operational Forecasts" and directs users to NCEI "to obtain other CFSv2 products such as the Operational Analysis." The bucket in fact carries **1,197 `cdas.*` directories** — the operational analysis — alongside the forecasts. The description also claims "hourly data," which no distributed product provides.

**3. Google Cloud Storage (NODD) — real-time + shallower forecast archive**

- **Bucket:** `gs://climate-forecast-system/` — anonymous object read and JSON-API listing; storage class `STANDARD`
- **HTTPS object access:** `https://storage.googleapis.com/climate-forecast-system/cfs.YYYYMMDD/…`
- **JSON API listing:** `https://storage.googleapis.com/storage/v1/b/climate-forecast-system/o?prefix=…&delimiter=/`

Archive: `cfs.YYYYMMDD/` from **2021-02-17** (1,985 directories) — **roughly 2.3 years shallower than AWS**. `cdas.YYYYMMDD/` from 2023-04-22 (1,197 directories), matching AWS exactly.

**4. Microsoft Azure (NODD) — real-time, 90-day rolling window for forecasts**

- **Blob container:** `https://noaacfs.blob.core.windows.net/cfs` — public, anonymous, no SAS token required
- **Read-only SAS token API (for BlobFuse mounts):** `https://planetarycomputer.microsoft.com/api/sas/v1/token/noaacfs/cfs`
- **Planetary Computer dataset page:** https://planetarycomputer.microsoft.com/dataset/storage/noaa-cfs

> **Azure splits its two streams on completely different retention policies:**
> - `cfs.*` (forecasts) — **92 days only**, 2026-05-01 → 2026-07-31, matching the ~90-day window on the Azure [GFS](../../../nwp_models/global/usa/gfs.md#data-availability) and [RAP](../../../nwp_models/regional/usa/rap.md#distribution-channels) containers
> - `cdas.*` (analysis) — **1,186 days**, back to 2023-04-22, essentially the full NODD span
>
> The shorter-retention stream is the one this entry is about. For CFSv2 forecasts older than ~3 months, Azure is not an option.

> **Azure is missing 11 `cdas.*` days** that AWS and GCS both hold, in two outage windows: **2024-12-23 → 2024-12-30** (8 days) and **2025-05-22 → 2025-05-24** (3 days). No forecast days are missing.

**5. Google Earth Engine — curated, analysis-ready, and formally deprecated**

- **Deprecated collection:** `ee.ImageCollection("NOAA/CFSV2/FOR6H")` — catalogue title carries an explicit `[deprecated]` marker and the STAC record reports `gee:status: deprecated`, `deprecated: true`
- **Successor:** `ee.ImageCollection("NOAA/CFSV2/FOR6H_HARMONIZED")` — `gee:status: ready`
- **Catalog page:** https://developers.google.com/earth-engine/datasets/catalog/NOAA_CFSV2_FOR6H

Both collections carry **22 bands at 22,264 m** (~0.2°) covering **1979-01-01 → 2026-07-30T06:00Z**: radiation flux components, latent/sensible heat flux, potential evaporation, precipitation rate, surface pressure and geopotential height, 2 m temperature and specific humidity (instantaneous plus 6-hour max/min), 10 m winds, and volumetric soil moisture at four depths (5/25/70/150 cm).

> **Two cautions.** First, the deprecated collection is **still being updated** — its temporal extent runs to the present — so a pipeline reading it will not fail loudly; it will simply be built on a collection Google has marked for removal. Migrate to `FOR6H_HARMONIZED`. Second, the 1979 start date means these collections span the **CFSR reanalysis era as well as operational CFSv2 forecasts**; they are not a pure forecast archive, and the pre-2011 portion is reanalysis, which falls outside this catalog's scope.

**6. NCEI — long-term archive and reforecasts**

- https://www.ncei.noaa.gov/access/metadata/landing-page/bin/iso?id=gov.noaa.ncdc:C00834

### Cross-cloud equivalence

All three NODD object stores carry an **identical `cfs.YYYYMMDD/` tree** — 51,880 objects for 2026-07-30 on AWS, GCS, and Azure alike — and the files are **byte-identical**. For `cfs.20260730/00/time_grib_01/tmp2m.01.2026073000.daily.grb2`:

| Cloud | Size | Checksum |
|---|---|---|
| AWS | 96,904,743 | ETag `4bf5b33fd8fa21a8fc12e5b1b160a843` |
| Azure | 96,904,743 | Content-MD5 `S/WzP9j6Iaj8EuWxsWCoQw==` |
| GCS | 96,904,743 | md5Hash `S/WzP9j6Iaj8EuWxsWCoQw==` |

All three serve **anonymous HTTP 206 byte-range requests** with `GRIB` magic at byte 0, and all three carry the `.idx` sidecars — which matters more for CFS than for most entries, since a single `time_grib` file can be ~97 MB holding 1,224 records and byte-range subsetting to a lead-time window is the normal access pattern.

**Choose on archive depth:** AWS for anything before 2021-02-17; AWS or GCS for 2021 onward; Azure only for the last ~3 months of forecasts.

---

## Notes
- **Scope boundary.** Only the `cfs.*` forecast is in scope. The co-located `cdas.*` real-time analysis (CDAS), the `monthly/cdas.YYYYMM/` analysis monthly means, and the separate CFS Reanalysis (CFSR) are all analyses — excluded under the catalog's no-analysis rule. **Two of the three top-level NOMADS directories in this tree are analysis**, and CDAS additionally has *deeper* retention than the forecast on both NOMADS (6 months of monthly means vs. 7 days) and Azure (full span vs. ~90 days). Anyone scripting `cfs/prod/` should filter on the `cfs.` prefix rather than walking the tree.
- **Single-model system.** Unlike [CanSIPS](../canada/cansips.md) or [NMME](../multi-national/nmme.md), CFSv2 is one coupled model; its ensemble comes from time-lagging and perturbations. It is itself a contributing member of NMME and of the [C3S seasonal multi-system](../multi-national/c3s-seasonal.md) (`system=2`).
- **Raw output only.** Anomalies and probabilities are user- or CPC-derived against the reforecast climatology; the CPC seasonal outlook maps are viewer-only and out of scope.
- **No hourly product is distributed**, despite the AWS registry description's claim of "hourly data." The three distributed cadences are 6-hourly (fields and time series), monthly, and diurnally-stratified monthly.
- **Two independent traps around output cadence and length.** The `.daily.` filename token on 6-hourly time-series files, and the `6hrly_grib_01` directory covering only ~6 months of a ~10-month run. Both are silent failure modes: code will read what it finds and produce a plausible-looking but truncated or mis-labelled result.
- **Operational since March 2011**, succeeding CFSv1.

---

## Recent version history
CFSv2 became operational at NCEP in March 2011, replacing CFSv1. No subsequent major operational version has superseded it. The system has been operationally stable for over fifteen years, which is unusual among the entries in this catalog and makes the archive unusually homogeneous for reforecast and verification work.

---

## Official documentation
- CFS home page: https://cfs.ncep.noaa.gov/
- CFS product inventory (NCO): https://www.nco.ncep.noaa.gov/pmb/products/cfs/
- NOMADS help and GRIB filter migration guide: https://nomads.ncep.noaa.gov/info.php?page=help
- AWS Open Data registry: https://registry.opendata.aws/noaa-cfs/
- Planetary Computer dataset page: https://planetarycomputer.microsoft.com/dataset/storage/noaa-cfs
- Google Earth Engine catalog (deprecated collection): https://developers.google.com/earth-engine/datasets/catalog/NOAA_CFSV2_FOR6H
- NCEI CFSv2 dataset page: https://www.ncei.noaa.gov/products/weather-climate-models/climate-forecast-system
- Saha et al. (2014), *The NCEP Climate Forecast System Version 2*, J. Climate: https://journals.ametsoc.org/view/journals/clim/27/6/jcli-d-12-00823.1.xml
- Saha et al. (2010), *The NCEP Climate Forecast System Reanalysis*, BAMS: https://journals.ametsoc.org/view/journals/bams/91/8/2010bams3001_1.xml
