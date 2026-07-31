# HRRR (High-Resolution Rapid Refresh)

## What this model is
The High-Resolution Rapid Refresh (HRRR) is NOAA's hourly-updating convection-allowing numerical weather prediction system, designed for very short-range forecasting of rapidly-evolving high-impact weather such as thunderstorms, fog, low clouds, wind shifts, and precipitation systems.

Operating at 3 km horizontal resolution over the contiguous United States and Alaska, HRRR explicitly resolves convective storms rather than parameterizing them, making it the primary operational guidance source for severe weather forecasting, aviation operations, fire weather, wind energy, and short-term decision support across the U.S.

HRRR is foundational infrastructure for several downstream NOAA products:
- **National Blend of Models ([NBM](./nbm.md))** — HRRR is one of the highest-weighted inputs for short-range CONUS guidance
- **Localized Aviation Model Output Statistics (LAMP)** — relies on HRRR cloud fields
- **Real-Time Mesoscale Analysis (RTMA)** — uses HRRR near-surface fields as background
- **National Water Model** — uses HRRR precipitation forecasts as forcing
- **[DAFS](./dafs.md)** — the Domestic Aviation Forecast System post-processes HRRR fields into icing and turbulence guidance

The current operational version is **HRRRv4** (operational since 2 December 2020), which is the **final version** of HRRR. No further upgrades are planned; the system has been frozen since the joint RAPv5/HRRRv4 implementation. HRRR is expected to be retired alongside [RAP](./rap.md) with the eventual transition to RRFSv2 (see [Future outlook](#future-outlook)).

Since HRRRv4 (December 2020), HRRR also produces operational wildfire smoke forecasts as integrated output fields, replacing the earlier experimental HRRR-Smoke parallel run. Smoke aerosols feed back on radiation and the atmospheric forecast, making HRRR one of the first operational weather models in the U.S. to include direct smoke-meteorology coupling.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL); operated at NCEP

---

## What area it covers
- **Operational coverage:** Contiguous United States (CONUS) and Alaska
- **Experimental sub-domains** (not operational): Hawaii, Caribbean/Puerto Rico, San Francisco 1 km nest, IMPACTS 1 km nest

Grid geometry decoded with ecCodes 2.48.0 from live 2026-07-30 00 UTC output:

| Domain | Projection | Grid | Spacing | Reference | First grid point | Points |
|---|---|---|---|---|---|---|
| **CONUS** | Lambert conformal | 1799 × 1059 | 3 km | LaD 38.5°, LoV 262.5°, Latin1 = Latin2 = 38.5° (tangent) | 21.138°N / 237.280°E | 1,905,141 |
| **Alaska** | polar stereographic | 1299 × 919 | 3 km | LaD 60° | 41.613°N / 185.117°E | 1,193,781 |

> The two domains use **different projections**, not merely different extents — code that assumes a single Lambert grid for "HRRR" will mis-handle the Alaska files. Alaska output additionally carries an `.ak.` infix in every filename.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing)
- **Model system / core:** WRF-ARW (Advanced Research WRF, version 3.x)
- **Dynamical formulation:** Non-hydrostatic, fully compressible, on a mass-based (sigma-pressure hybrid) vertical coordinate
- **Convection-allowing:** Yes — deep convection is explicitly resolved at 3 km grid spacing; no cumulus parameterization is used
- **Native horizontal resolution:** 3 km
- **Vertical levels:** 50
- **Vertical coordinate:** Hybrid sigma-pressure
- **Model top:** 20 hPa
- **Forecast length (CONUS, live-verified):**
  - **48 hours** (49 steps, f00–f48) at 00, 06, 12, 18 UTC
  - **18 hours** (19 steps, f00–f18) at the other 20 cycles
- **Forecast length (Alaska, live-verified):**
  - Runs 8× daily at 00, 03, 06, 09, 12, 15, 18, 21 UTC
  - **48 hours** at 00, 06, 12, 18 UTC; **18 hours** at 03, 09, 15, 21 UTC
- **Update frequency:** Hourly for CONUS (24× daily); every 3 hours for Alaska (8× daily)
- **Temporal output resolution:**
  - Hourly for `wrfnat`, `wrfprs`, `wrfsfc`
  - **15-minute subhourly** output in the `wrfsubh` product, always f00–f18 regardless of cycle length (each file holds four 15-minute sub-steps — see *Product inventory*)
- **Observed publication latency:** first CONUS step of the 00 UTC cycle written ~00:50 UTC (~T+50 m), last step of a 48-hour cycle ~T+2 h. Alaska runs slightly ahead of CONUS (00:43 UTC for `wrfnatf00.ak`).

---

## Data assimilation
HRRR runs an **hourly cycling hybrid ensemble-variational** data assimilation system at 3 km, using the Gridpoint Statistical Interpolation (GSI) framework. The CONUS and Alaska domains use different DA architectures:

### CONUS — HRRRDAS (HRRR Data Assimilation System, since v4)
- **36-member 3 km ensemble** running with explicit convective storms
- Each member integrated for 1 hour during the DA cycle
- **Ensemble Kalman Filter** assimilation including direct radar reflectivity assimilation
- The ensemble mean provides the initial conditions for the deterministic HRRR spin-up and analysis process
- Provides flow-dependent background-error covariances for the deterministic HRRR's hybrid analysis
- Partially cycled from RAP analyses to incorporate global-scale information from GFS via the RAP→HRRRDAS chain

The HRRRDAS ensemble background fields are **publicly distributed** — see *The `nwges/` guess streams* under Data availability.

### Alaska — RAP-driven initialization (no separate ensemble DA)
- Initial conditions derived from the latest [RAP](./rap.md) analysis (HRRRDAS is CONUS-only)
- Hourly 3 km radar reflectivity assimilation during the 1-hour spin-up period
- Cloud and hydrometeor analysis to initialize stratiform cloud layers

### Common across both domains
- **Radar reflectivity assimilation every 15 minutes during the 1-hour pre-forecast spin-up**, using digital filter initialization to ingest precipitation system structure
- Cloud and hydrometeor analysis to initialize stratiform cloud layers from METAR, satellite, and other observations
- Conventional surface, aircraft, radiosonde, mesonet, and satellite observation assimilation

The combination of dense radar DA, cloud analysis, and (for CONUS) the 36-member ensemble background makes HRRR's initialization one of the most observation-rich in operational mesoscale NWP.

---

## Initial and boundary conditions
- **CONUS initial conditions:** HRRRDAS ensemble mean analysis (HRRRv4); previously RAP analysis (HRRRv1–v3)
- **Alaska initial conditions:** RAP analysis (no HRRRDAS for Alaska)
- **Lateral boundary conditions:** [RAP](./rap.md), updated each cycle from the most recent available RAP forecast

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convection and severe storm evolution (explicitly resolved)
- Precipitation type, intensity, and accumulation
- Low clouds, ceilings, visibility, and fog
- Fire-weather-relevant fields including near-surface wind and humidity
- Wildfire smoke fields (near-surface smoke concentration and vertically integrated smoke), with smoke aerosols feeding back on radiation and the atmospheric forecast
- Convective indices including CAPE, CIN, helicity, and updraft helicity
- Aviation-relevant fields including icing, turbulence, and ceilings
- Subhourly (15-minute) updates of selected fields for nowcasting use

HRRR is primarily intended for **surface, boundary-layer, and storm-scale analysis** rather than synoptic-scale upper-air diagnostics. NOAA explicitly notes that full 3-dimensional upper level fields are not provided for the HRRR, as there is little relevant synoptic-scale information gained from the resolution difference between the HRRR and its parent, the RAP.

---

## Relationship to other models
- **[RAP](./rap.md):** Parent model. RAP supplies lateral boundary conditions and (for Alaska HRRR) initial conditions. RAP also provides background fields and global-scale information that flow into HRRRDAS through the partial cycling chain. The two systems share much of the same physics suite and DA infrastructure.
- **[NBM](./nbm.md):** HRRR is one of the most heavily-weighted deterministic inputs for short-range CONUS guidance.
- **[DAFS](./dafs.md):** Post-processes HRRR output into aviation icing (IFI) and turbulence (GTG) guidance; carries no analysis of its own.
- **[RRFS](./rrfs.md) and [REFS](../../ensemble_models/regional/usa/refs.md):** Future replacement for HRRR and RAP. RRFSv1 implements **October 6, 2026 at 12 UTC** under SCN 26-48 but does **not** retire HRRR — HRRR retirement is tied to the future RRFSv2 (MPAS-based) timeline. Notably, **HRRR contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains**, so HRRR is an explicit operational input to REFS from October 6, 2026 onward until HRRR itself is retired.

### Downstream NOAA products that depend on HRRR
- **Localized Aviation Model Output Statistics (LAMP)** — uses HRRR cloud fields
- **Real-Time Mesoscale Analysis (RTMA)** — uses HRRR near-surface fields as background
- **National Water Model** — uses HRRR precipitation forecasts as atmospheric forcing
- **National Blend of Models ([NBM](./nbm.md))** — uses all HRRR fields as input

### HRRR-Cast — experimental AI sibling
NOAA OAR/GSL released **HRRR-Cast** in July 2025, an AI-based experimental sibling that emulates HRRR forecasts using a neural network trained on HRRR analysis data. It is not a replacement for the operational HRRR — it runs experimentally at NWS EMC for evaluation, with HRRR-Cast v2 (September 2025) adding ensemble capabilities, increased vertical resolution (12 → 20 levels), and additional surface variables. See the [HRRR-Cast entry](./hrrrcast.md).

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:** **Public domain (U.S. government work; CC0-equivalent).** Distributed via NOAA Open Data Dissemination (NODD): data are open to the public and may be used as desired. NOAA requests attribution, prohibits stating or implying NOAA endorsement or affiliation, and requires that modified data not be presented as unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 with `.idx` index sidecars (primary); NetCDF for the `nwges/` guess streams; **Zarr** for the University of Utah cloud-native archive; BUFR and gzipped TAR for soundings

### Product inventory

Live-verified against `hrrr.20260730/` on 2026-07-30. Per-step sizes are means across a 48-hour cycle; record counts are from the f01 step.

**CONUS** — `hrrr.YYYYMMDD/conus/`

| File family | Steps per extended / standard cycle | Records | Size/step |
|---|---|---|---|
| `hrrr.tCCz.wrfnatfFF.grib2` — native hybrid levels | 49 / 19 | 1133 | 770 MB |
| `hrrr.tCCz.wrfprsfFF.grib2` — pressure levels | 49 / 19 | 708 | 443 MB |
| `hrrr.tCCz.wrfsfcfFF.grib2` — surface / 2D fields | 49 / 19 | 170 | 161 MB |
| `hrrr.tCCz.wrfsubhfFF.grib2` — 15-minute subhourly | 19 / 19 | 196 | 192 MB |
| `hrrr.tCCz.bufrsnd.tar.gz` — bundled soundings | 1 per cycle | — | 23 MB |
| `hrrr.tCCz.class1.bufr.tm00` — BUFR soundings | 1 per cycle | — | 98 MB |

**Alaska** — `hrrr.YYYYMMDD/alaska/`, all filenames carrying an `.ak.` infix

| File family | Steps per extended / standard cycle | Records | Size/step |
|---|---|---|---|
| `hrrr.tCCz.wrfnatfFF.ak.grib2` | 49 / 19 | 1127 | 495 MB |
| `hrrr.tCCz.wrfprsfFF.ak.grib2` | 49 / 19 | — | 262 MB |
| `hrrr.tCCz.wrfsfcfFF.ak.grib2` | 49 / 19 | 169 | 94 MB |
| `hrrr.tCCz.wrfsubhfFF.ak.grib2` | 19 / 19 | — | 133 MB |
| `hrrr.tCCz.bufrsnd.tar.ak.gz` | 1 per cycle | — | 2.6 MB |
| `hrrr.tCCz.class1.bufr.ak.tm00` | 1 per cycle | — | 19 MB |

**Total volume: ≈ 1.12 TB per day** — CONUS 867 GB (4,416 objects) plus Alaska 253 GB (1,952 objects). `wrfnat` alone accounts for 434 GB/day on CONUS. This makes HRRR one of the largest single-model open datasets in this catalog; roughly two and a half times [RAP](./rap.md)'s daily volume and comparable to a full day of [GFS](../../global/usa/gfs.md).

> **`wrfsubh` structure.** Each `wrfsubh` file is *not* a single time step. It holds **45 distinct fields at four 15-minute offsets** (196 records), covering the quarter-hours within the labelled forecast hour. The subhourly product is produced for f00–f18 in **every** cycle, including the 48-hour ones — the extended cycles do not extend subhourly output.

### Distribution channels

**1. NOMADS (NCEP operational distribution)**

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/hrrr/prod/
  hrrr.YYYYMMDD/{conus,alaska}/
  nwges/{hrrrdasges,hrrrges_sfc}/
```

**Retention is two days** — `hrrr.20260730/` and `hrrr.20260731/` only, as of 2026-07-31. The same short window as [RAP](./rap.md), and unsurprising at ~1.12 TB/day. Anything older must come from AWS, Azure, or GCS.

NOMADS additionally carries the **exploded `bufrsnd.tCCz/` directories** of per-station BUFR sounding files under `conus/` (one directory per cycle). Note that these appear only under `conus/` — the Alaska subtree has no `bufrsnd.tCCz/` directories, only the bundled `.tar.ak.gz`.

> Both the OPeNDAP (`/dods/`) and FTPPRD (`ftpprd.ncep.noaa.gov`, `ftp.ncep.noaa.gov`) routes were retired on **23 February 2026** under SCN 25-81 and SCN 25-82 respectively. Neither is a valid HRRR access path any more.

**2. AWS Open Data (NODD) — real-time + the deepest archive of any model in this catalog**

- **S3 bucket:** `s3://noaa-hrrr-bdp-pds/` — ARN `arn:aws:s3:::noaa-hrrr-bdp-pds`, region **`us-east-1`**
- **Browser access:** https://noaa-hrrr-bdp-pds.s3.amazonaws.com/index.html
- **AWS CLI (no account required):** `aws s3 ls --no-sign-request s3://noaa-hrrr-bdp-pds/`
- **SNS new-object notifications:** `arn:aws:sns:us-east-1:123901341784:NewHRRRObject`
- **Registry:** https://registry.opendata.aws/noaa-hrrr-pds/
- **AWS-maintained docs:** https://github.com/awslabs/open-data-docs/tree/main/docs/noaa/noaa-hrrr

Archive: `hrrr.YYYYMMDD/` from **2014-07-30** to present — **4,384 directories** spanning twelve years, with exactly **one calendar gap: 2016-03-19**. The archive predates HRRR's own operational implementation (30 September 2014), so the earliest directories are pre-operational.

**3. Google Cloud Storage (NODD) — real-time + identical deep archive**

- **Bucket:** `gs://high-resolution-rapid-refresh/` — anonymous object read and JSON-API listing; storage class **`MULTI_REGIONAL`**
- **HTTPS object access:** `https://storage.googleapis.com/high-resolution-rapid-refresh/hrrr.YYYYMMDD/…`
- **Marketplace page:** https://console.cloud.google.com/marketplace/details/noaa-public/hrrr

Archive: **4,384 directories, 2014-07-30 → present, with the same single 2016-03-19 gap** — byte-for-byte matched to AWS at the date level, unlike the [RAP](./rap.md#distribution-channels) buckets where GCS is missing four days.

> ✅ **GCS carries content AWS and Azure do not.** For the 2026-07-30 CONUS subtree GCS holds **41,305 objects against 4,416 on AWS and Azure**. The surplus is the **exploded per-station BUFR soundings** — `hrrr.YYYYMMDD/conus/bufrsnd.tCCz/bufr.NNNNNN.YYYYMMDDHH`, **36,888 files/day, ~2.74 GB** — plus an `ls-l` directory manifest. AWS and Azure have no subdirectories under `conus/` at all and carry only the bundled `bufrsnd.tar.gz`.
>
> This is the **reverse** of the [GFS](../../global/usa/gfs.md#cross-cloud-equivalence) and [RAP](./rap.md#distribution-channels) pattern, where the exploded soundings are NOMADS-only and absent from every cloud. For HRRR, GCS is the only archival route to per-station BUFR soundings.

**4. Microsoft Azure (NODD) — real-time + multi-year archive**

- **Blob container:** `https://noaahrrr.blob.core.windows.net/hrrr` — public, anonymous, no SAS token required
- **Region:** East US
- **Read-only SAS token API (for BlobFuse mounts):** `https://planetarycomputer.microsoft.com/api/sas/v1/token/noaahrrr/hrrr`
- **Documentation:** https://microsoft.github.io/AIforEarthDataSets/data/noaa-hrrr.html
- **Planetary Computer dataset page:** https://planetarycomputer.microsoft.com/dataset/storage/noaa-hrrr

> **Azure's HRRR container is the exception to the 90-day rule.** It holds **1,959 days, 2021-03-21 → 2026-07-31** — not the ~90-day rolling window observed on the Azure [GFS](../../global/usa/gfs.md#data-availability) and [RAP](./rap.md#distribution-channels) containers. Consistent with this, the Microsoft dataset page for HRRR states **no** retention period, where the GFS and RAP pages both (incorrectly) claim 30 days.

> ⚠️ **Two Azure quirks (live-verified 2026-07-31):**
> 1. **Path case.** The documentation gives blob names as `HRRR.[year][month][day]/[region]/…` with a capitalised prefix. The live layout is lowercase `hrrr.YYYYMMDD/`. The page's own worked example uses lowercase, contradicting its stated pattern — and blob storage is case-sensitive, so code written to the documented form will 404.
> 2. **A malformed leading-slash prefix.** The container has a stray `/hrrr.20240416/` prefix (note the leading slash) alongside the normal top-level entries — an apparent one-off ingest artifact. Enumeration code that assumes all blobs start with `hrrr.` or `nwges/` will trip over it.

**5. The `nwges/` guess streams — HRRRDAS ensemble backgrounds**

Present on **all three clouds** and on NOMADS, `nwges/` carries the data-assimilation guess fields rather than forecast output:

- **`nwges/hrrrdasges/`** — the **36-member HRRRDAS ensemble** background, files named `hrrrdas_small_d02_YYYYMMDDHHMMfNN_memNNNN` with members `mem0001`–`mem0036`. **122,516 objects**, live through 2026-07-31, oldest object dated 2024-02-14.
- **`nwges/hrrrges_sfc/conus/`** — surface guess fields, `hrrr_YYYYMMDDHHfNNN`. 3,402 objects, also live.

> **These are NetCDF, not GRIB2** — magic bytes `CDF\002`, i.e. NetCDF classic 64-bit-offset. That makes a **36-member 3 km convection-allowing ensemble background publicly available in gridded form**, which is unusual and not mentioned in NOAA's own HRRR product documentation. Treated here as in-scope gridded data, though it is DA machinery rather than a forecast product.

**6. Zarr archive (University of Utah / MesoWest)**

- **S3 bucket:** `s3://hrrrzarr/` — region **`us-west-1`** (note: *not* `us-east-1` like the NODD buckets)
- **Browser access:** https://hrrrzarr.s3.amazonaws.com/index.html
- **Project page:** https://mesowest.utah.edu/html/hrrr/
- **Contact:** `atmos-mesowest@lists.utah.edu`

Layout is `sfc/YYYYMMDD/` and `prs/YYYYMMDD/` with per-cycle `.zarr` groups, plus `grid/HRRR_chunk_index.zarr/` for chunk lookup. **Live and current** — both streams updated on 2026-07-31.

> **Coverage is narrower than commonly stated.** The Zarr archive does **not** go back to 2014: `sfc/` begins **2016-08-23** (3,628 days) and `prs/` begins **2018-07-12** (2,931 days). It is also a curated subset, not a Zarr rendering of the full GRIB2 product set. For anything before 2016, or for `wrfnat`/`wrfsubh`, the GRIB2 buckets are the only route.

**7. `hrrr_v2.*` — historical parallel stream**

AWS and GCS both carry a `hrrr_v2.YYYYMMDD/` prefix spanning **2016-01-22 → 2016-08-23** (206 days), with the same `conus/hrrr.tCCz.wrfnat…` layout. This is the pre-implementation parallel feed for HRRRv2, which went operational on the final date of the series. Frozen; absent from Azure.

### Cross-cloud equivalence

The GRIB2 forecast files are **byte-identical** across all three clouds. For `hrrr.20260730/conus/hrrr.t00z.wrfsfcf01.grib2`:

| Cloud | Size | Checksum |
|---|---|---|
| AWS | 150,225,722 | ETag `b179ac7d79851b325d2af9a37964e623` |
| Azure | 150,225,722 | Content-MD5 `sXmsfXmFGzJdKvmjeWTmIw==` |
| GCS | 150,225,722 | md5Hash `sXmsfXmFGzJdKvmjeWTmIw==` |

Alaska object counts match exactly across all three (1,952 for 2026-07-30); only the CONUS BUFR surplus on GCS differs.

**Choose on:** GCS if you need per-station BUFR soundings or a multi-region-replicated bucket; AWS for the canonical archive and SNS notifications; Azure for anything within 2021-03-21 onward when co-located in East US. All three are equivalent for GRIB2 forecast fields.

---

## Notes
- HRRR is convection-allowing at 3 km and does not use a cumulus parameterization. Deep convection is explicitly resolved.
- HRRR and [RAP](./rap.md) share much of the same modeling and assimilation infrastructure, including a similar physics suite (MYNN-EDMF PBL, Thompson microphysics, RRTMG radiation, RUC-Smirnova land surface) and the GSI hybrid DA framework. They are best understood as a paired mesoscale (RAP) + storm-scale (HRRR) system.
- **HRRR-Smoke is no longer a separate experimental run** — wildfire smoke prediction has been integrated into operational HRRRv4 since December 2020, with smoke aerosols affecting radiation and feeding back into the meteorological forecast. The smoke fields are distributed inside the standard output; NOAA's graphics portal presents them under separate "HRRR CONUS Smoke Fields" and "HRRR Alaska Smoke Fields" pages, but these are sourced from the same run. Unlike [RAP](./rap.md#smoke-products), HRRR has **no** separate NDFD-grid smoke product files.
- **HRRRv4 is the final version of HRRR.** No further upgrades are planned. The system has been operationally frozen since 2 December 2020 and is in maintenance-only status pending eventual retirement.
- The **Alaska HRRR continues to use RAP-derived initial conditions** rather than a separate HRRRDAS ensemble. HRRRDAS is CONUS-only — which is also why `nwges/hrrrges_sfc/` has only a `conus/` subdirectory.
- HRRR's hourly cycling, dense radar assimilation, and explicit convection make it especially well-suited for severe weather forecasting, fire weather, and aviation. It is among the most heavily-used operational mesoscale models in U.S. severe weather operations.
- **Volume planning matters more for HRRR than for any other entry in this catalog.** A single CONUS 48-hour cycle of `wrfnat` is ~38 GB. Users needing surface fields only should take `wrfsfc` (161 MB/step) rather than `wrfnat` (770 MB/step) — a 4.8× reduction — and should drive partial reads from the `.idx` sidecars, which are present on all three clouds for every GRIB2 family.

---

## Open questions / outreach
- **Exploded BUFR soundings are GCS-only:** present on NOMADS (2-day window) and Google Cloud, absent from AWS and Azure. Is this a deliberate split or a sync-configuration difference? → `nodd@noaa.gov`
- **`nwges/hrrrdasges/` 36-member ensemble:** publicly distributed NetCDF with no accompanying product documentation. What is the retention policy, and is it intended as a public product or an operational side-effect? → `nodd@noaa.gov` / NOAA GSL
- **Azure `/hrrr.20240416/` leading-slash prefix:** apparent ingest artifact. Removable? → `nodd@noaa.gov`
- **Azure HRRR retention:** deep archive to 2021-03-21 with no documented policy, unlike GFS and RAP. Is the archive committed or best-effort? → `nodd@noaa.gov`
- **2016-03-19 gap:** identical on AWS and GCS, suggesting an upstream production gap rather than a sync failure. Recoverable? → `nodd@noaa.gov`

---

## Future outlook

HRRR is expected to be retired alongside [RAP](./rap.md) as part of the eventual transition to **RRFSv2** (the MPAS-based version of the [Rapid Refresh Forecast System](./rrfs.md)). Unlike the NAM, NAM Nest, HiresW (non-Guam), HREF, and SREF — which are scheduled for retirement on October 6, 2026 under [NWS Service Change Notice 26-47](https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf) (termination notice, updated July 6, 2026; RRFS/REFS implementation under companion [SCN 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf); originally proposed under PNS 25-41) — **HRRR and RAP are explicitly not part of the RRFSv1 retirement wave**. Both are expected to continue operating in parallel with RRFSv1 until the RRFSv2 transition. SCN 26-48 also assigns HRRR an active role in the new REFS, contributing two members (current and 6 h old cycles) to the CONUS and Alaska domains — formalising HRRR as a continuing operational input rather than just a legacy system in maintenance.

No formal Service Change Notice for HRRR retirement has been issued as of July 2026.

---

## Recent version history

### HRRRv4 — operational 2 December 2020 (current; final version)
The last HRRR upgrade. Joint implementation with RAPv5. Headline changes:
- **HRRRDAS** introduced — 36-member 3 km ensemble for storm-scale data assimilation, providing flow-dependent covariances and replacing RAP-derived initial conditions for CONUS
- **Wildfire smoke prediction** integrated into operational HRRR (previously experimental as HRRR-Smoke) — smoke aerosols feed back on shortwave radiation and the atmospheric forecast
- Improved cloud representation for boundary-top clouds, especially shallow cold-air layers with cold-air retention
- Better representation of cloud bands (snow squalls, hurricane bands, lake-effect bands)
- Forecast extension to 48 hours every 6 hours (previously 36 hours in HRRRv3)
- Updated physics suite with improved MYNN-EDMF planetary boundary layer scheme

### HRRRv3 — operational July 2018
- **Alaska domain added**, expanding HRRR beyond CONUS for the first time
- Forecast extension to 36 hours every 6 hours
- Improved physics including PBL and microphysics enhancements

### HRRRv2 — operational 23 August 2016
First major upgrade after operational launch. Added subgrid-scale cloud handling, improved radar DA, and other enhancements documented in Benjamin et al. (2016). The pre-implementation parallel feed for this version survives in the AWS and GCS buckets as `hrrr_v2.*` (2016-01-22 → 2016-08-23).

### HRRRv1 — operational 30 September 2014
Initial operational implementation at NCEP. CONUS-only, hourly forecasts to 15 hours, 3 km grid spacing. Notable as the first hourly-updating convection-allowing model in U.S. operations and one of the earliest such systems globally. The NODD archives begin two months earlier, on 2014-07-30, during pre-operational testing.

---

## Official documentation
- HRRR home page (NOAA GSL): https://rapidrefresh.noaa.gov/hrrr/
- HRRR at EMC: https://emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/hrrr.php
- HRRR Product Inventory at NCEP: https://www.nco.ncep.noaa.gov/pmb/products/hrrr/
- NOMADS help and GRIB filter migration guide: https://nomads.ncep.noaa.gov/info.php?page=help
- NWS Service Change Notice 20-46 (RAPv5/HRRRv4 implementation): https://www.weather.gov/media/notification/pdf2/scn20-46rap_v5_hrrr_v4_aab.pdf
- NWS PNS 25-41 (RRFS legacy model cessation, NOT including HRRR): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- AWS Open Data registry: https://registry.opendata.aws/noaa-hrrr-pds/
- AWS Open Data documentation: https://github.com/awslabs/open-data-docs/tree/main/docs/noaa/noaa-hrrr
- Azure / AIforEarthDataSets: https://microsoft.github.io/AIforEarthDataSets/data/noaa-hrrr.html
- Google Cloud Marketplace: https://console.cloud.google.com/marketplace/details/noaa-public/hrrr
- HRRR Zarr archive (MesoWest): https://mesowest.utah.edu/html/hrrr/
- HRRR-B download package (Brian Blaylock): https://github.com/blaylockbk/HRRR_archive_download
- HRRR-Cast (NOAA GSL): https://gsl.noaa.gov/news/new-upgrades-to-hrrr-cast-noaas-experimental-ai-powered-regional-model

### Key references
- Dowell, D. C., et al. (2022). *The High-Resolution Rapid Refresh (HRRR): An Hourly Updating Convection-Allowing Forecast Model. Part I: Motivation and System Description.* Wea. Forecasting, 37, 1371–1395. https://doi.org/10.1175/WAF-D-21-0151.1
- Gowan, T. A., Horel, J. D., Jacques, A. A., and Kovac, A. (2022). *Using Cloud Computing to Analyze Model Output Archived in Zarr Format.* J. Atmos. Oceanic Technol., 39, 449–462. https://doi.org/10.1175/JTECH-D-21-0106.1
