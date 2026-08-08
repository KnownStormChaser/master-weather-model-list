# NDFD (National Digital Forecast Database)

## What this model is
The National Digital Forecast Database (NDFD) is the National Weather Service's **official gridded public forecast** — the digital form of the forecast that NWS issues to the public, distributed as raw GRIB2.

NDFD is **not a model**. It runs no atmospheric simulation and applies no post-processing algorithm of its own. Each of the 122 NWS Weather Forecast Offices (WFOs) edits a gridded forecast for its own county warning area, starting from [NBM](./nbm.md) guidance and modifying it with human judgement; national centres (WPC, SPC, NHC, OPC, CPC) supply grids for their areas of responsibility. Those pieces are collaborated across office boundaries and mosaicked centrally into a seamless national grid.

This makes NDFD the **human-in-the-loop terminus** of the U.S. forecast chain: the point where model guidance becomes the official forecast. It is also the source behind a very large share of third-party U.S. weather presentation — most consumer weather sites and apps showing "the NWS forecast" are rendering NDFD, directly or through the NDFD XML/REST services.

> **Scope note — first human-in-the-loop entry in the catalog.** The repository's existing algorithmic-not-dynamical entries ([NBM](./nbm.md), a statistical blend; [DAFS](./dafs.md), UPP diagnostics applied to [HRRR](./hrrr.md)) are still deterministic software pipelines. NDFD's content is produced by forecasters. It is catalogued here because it satisfies the repository's actual admission test — raw gridded GRIB2 through permanent, unrestricted, open channels — but readers should not treat it as a forecast *system* comparable to the surrounding entries. Its skill is not attributable to any single model.

---

## Who runs it
- **Organization:** NOAA / National Weather Service — Meteorological Development Laboratory (MDL) operates the database; content originates from the 122 WFOs and from WPC, SPC, NHC, OPC, and CPC
- **Country / region:** United States

---

## What area it covers
- **Coverage:** United States, its territories, adjacent oceanic waters, and (for tropical cyclone wind probabilities) the Northern Hemisphere and North Pacific
- **Domain details:** **24 area directories**, comprising 8 master domains and 16 CONUS subsectors.

### Master domains (live-verified 2026-08-07, grid headers decoded from live GRIB2)

| Area directory | Projection | Grid points | Spacing |
|---|---|---|---|
| `AR.conus` | Lambert conformal (LoV 265°, tangent 25°) | 2145 × 1377 | 2539.7 m |
| `AR.alaska` | Polar stereographic | 1649 × 1105 | 2976.56 m |
| `AR.hawaii` | Mercator | 321 × 225 | 2500 m |
| `AR.guam` | Mercator | 193 × 193 | 2500 m |
| `AR.puertori` | Mercator | 339 × 225 | 1250 m |
| `AR.oceanic` | Mercator | 2517 × 1793 | 10 km |
| `AR.nhemi` | Lambert conformal | 1473 × 1025 | 5079.41 m |
| `AR.npacocn` | Mercator | 1473 × 1073 | 10 km |

CONUS first grid point 20.192 °N / 238.446 °E; `shapeOfTheEarth = 1` (spherical, specified radius).

### CONUS subsectors — decimated, not cropped

The 16 subsector directories (`AR.crgrlake`, `AR.crmissvy`, `AR.crplains`, `AR.crrocks`, `AR.ergrlake`, `AR.midatlan`, `AR.neast`, `AR.nplains`, `AR.nrockies`, `AR.pacnwest`, `AR.pacswest`, `AR.seast`, `AR.smissvly`, `AR.splains`, `AR.srockies`, `AR.umissvly`) are **not** subsets of the CONUS grid at CONUS resolution. Live-verified on `AR.neast`: 187 × 229 at **5079.41 m** — exactly half the CONUS resolution, on the same Lambert projection. Users wanting full-resolution regional data must subset `AR.conus` themselves.

---

## Basic details
- **Model type:** Official gridded forecast database — human-edited mosaic; **not an NWP integration**
- **Model system / core:** Not applicable. Primary guidance source is [NBM](./nbm.md); WFOs edit in AWIPS/GFE, and national centres contribute grids for their areas of responsibility
- **Dynamical formulation:** Not applicable
- **Convection-allowing:** Not applicable (grid spacing is a forecast-presentation resolution, not a model resolution)
- **Horizontal resolution:** 2.5 km CONUS/Hawaii, ~3 km Alaska, 1.25 km Puerto Rico, 2.5 km Guam, 10 km oceanic, ~5 km NHemi — see table above
- **Vertical levels:** Not applicable — NDFD is a **surface / sensible-weather database**. All elements are single-level (2 m, 10 m, or column-integrated diagnostics). There are no pressure-level or model-level fields.
- **Forecast length:** Structured as three valid-period tiers, present as directories under each area:
  - `VP.001-003` — days 1–3
  - `VP.004-007` — days 4–7
  - `VP.008-450` — days 8–450 (CPC extended-range and seasonal probabilities; CONUS, Alaska, and CONUS subsectors only)
- **Update frequency / cycles:**
  - **Days 1–3, CONUS: twice hourly.** Live-verified from the WMO archive — `YEUZ98_KWBN` (CONUS temperature, days 1–3) has **48 files on 2026-08-06**, issued at approximately HH:16 and HH:46. This matches TIN 15-28 (effective 2015-06-30), which raised CONUS days 1–3 from hourly to twice hourly.
  - **Days 4–7: 5× daily.** `YEUZ97_KWBN` had 5 files on 2026-08-06 at 0533, 1133, 1733, 2133, and 2333 UTC — the 00/06/12/18/22 UTC issuances posted roughly H−27 min. The 22 UTC issuance is when the database is extended by 24 h (TIN 06-72, effective 2006-11-28).
  - **Non-CONUS domains: hourly.** `YEAZ88_KWBN` observed at HH:47 each hour.
  - **Days 8–450:** CPC cadence — 14-day products daily, 30-day monthly, 90-day monthly.
- **Temporal output resolution:** Element-dependent and non-uniform within a single file. Live-verified on `AR.conus/VP.001-003/ds.temp.bin` (2026-08-07 05 UTC, 47 messages): **hourly for steps 1–37, then 3-hourly to step 67**. Interval elements differ again — `ds.maxt.bin` carries 3 messages with 12-hour statistical windows (`7-19`, `31-43`, `55-67`); `ds.pop12.bin` uses 12-hour probability windows.

---

## Data assimilation
- **Data assimilation:** Not applicable. NDFD performs no analysis. Assimilation happens upstream in the models feeding [NBM](./nbm.md).

---

## Initial and boundary conditions
- **Guidance source:** [NBM](./nbm.md) is the primary starting point for WFO grids. NBM now serves directly as the days 4–7 CONUS forecast with only minor WPC editing (see the NBM entry). [NAM](./nam.md)-DNG historically populated NDFD grids as well — relevant to track given NAM's **2026-10-06 retirement**.
- **Boundary conditions:** Not applicable. The equivalent concept is inter-office collaboration: WFO grids are reconciled across county warning area boundaries before mosaicking, which is why NDFD is seamless rather than showing office seams.

---

## What it provides

**CONUS `VP.001-003` carries 49 elements** (live-verified 2026-08-07); `VP.004-007` carries 30. Element counts by domain:

| Area | days 1–3 | days 4–7 | days 8–450 |
|---|---|---|---|
| `AR.conus` | 49 | 30 | 12 |
| `AR.neast` (subsector) | 36 | 24 | 12 |
| `AR.puertori` | 28 | 17 | — |
| `AR.hawaii` | 23 | 17 | — |
| `AR.alaska` | 18 | 15 | 12 |
| `AR.guam` | 17 | 16 | — |
| `AR.nhemi` / `AR.npacocn` | 6 | 6 | — |
| `AR.oceanic` | 5 | 5 | — |

**Core sensible weather:** temperature (`temp`), dewpoint (`td`), apparent temperature (`apt`), wet bulb globe temperature (`wbgt`), max/min temperature (`maxt`, `mint`), relative humidity (`rhm`), max/min RH (`maxrh`, `minrh`), sky cover (`sky`), wind direction/speed/gust (`wdir`, `wspd`, `wgust`), visibility (`vis`), ceiling (`cig`).

**Precipitation:** 12-hour probability of precipitation (`pop12`), QPF (`qpf`), snow amount (`snow`), snow level (`snowlevel`), ice accumulation (`iceaccum`).

**Categorical / coded:** weather (`wx`), watches–warnings–advisories (`wwa`) — see the decoding warning below.

**Convective (SPC-sourced):** total and extended severe thunderstorm probability (`ptotsvrtstm`, `ptotxsvrtstm`), tornado / hail / thunderstorm-wind probabilities (`ptornado`, `phail`, `ptstmwinds`) and their extended counterparts (`pxtornado`, `pxhail`, `pxtstmwinds`), convective hazard outlook (`conhazo`).

**Fire weather:** critical fire weather (`critfireo`), dry lightning (`dryfireo`), fire-weather relative humidity and dewpoint depression (`fret`, `fretdep`, `frettot`).

**Aviation:** low-level wind shear direction, height, and speed (`llwsdir`, `llwshgt`, `llwsspd`).

**Marine:** significant wave height (`waveh`).

**Tropical cyclone (NHC-sourced):** cumulative and incremental wind speed probabilities above 34, 50, and 64 kt (`tcwspdabv{34,50,64}{c,i}`), plus tropical-cyclone threat grids (`tcfrt`, `tcsst`, `tctt`, `tcwt`).

**Extended range (CPC-sourced, `VP.008-450`):** probability of above/below normal temperature and precipitation at 14-day, 30-day, and 90-day ranges (`tmpabv14d`, `tmpblw90d`, etc.) — 12 elements. See the scope flag under *Notes*.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** Public domain (U.S. government work; CC0-equivalent). The NODD statement requests attribution for unaltered data, prohibits stating or implying NOAA endorsement, and prohibits presenting modified data as original unaltered NOAA data.

  > **One licence-shaped caveat, flagged rather than resolved.** The NCEI THREDDS catalog metadata carries a `rights` field stating that electronic downloads are free but that fees apply for data certification and distribution on physical media. This is standard NCEI boilerplate covering their certification service, not a restriction on the data, and it does not affect the public-domain status of the GRIB2 files. It is noted here because it is the only licence-adjacent statement in any of the distribution metadata — the public-domain framing otherwise rests entirely on the NODD statement.

- **Is the data downloadable?** Yes
- **Data formats:** GRIB2. Live-verified on CONUS `maxt`: centre **8** (US National Weather Service — NWSTG), `tablesVersion = 1`, `typeOfGeneratingProcess = 2` (forecast), **complex packing with spatial differencing**, no bitmap on numeric fields. Files are element-concatenated: one file holds every forecast step for one element on one grid. **No `.idx` sidecars** anywhere — byte-range subsetting of the kind available for NOMADS GRIB2 is not possible here.

### Official download locations

**1. NWS TGFTP — the complete distribution**

```
https://tgftp.nws.noaa.gov/SL.us008001/ST.opnl/DF.gr2/DC.ndfd/AR.<area>/VP.<period>/ds.<element>.bin
```

Live rolling files, overwritten in place. **This is the only route carrying all 24 areas** — the 16 CONUS subsectors appear nowhere else. An experimental tree exists in parallel at `ST.expr/` (HTTP 200 verified).

An FTP route at `ftp://tgftp.nws.noaa.gov/SL.us008001/ST.opnl/DF.gr2/DC.ndfd/` is documented and widely referenced. **Not verified** — port 21 to 140.172.138.79 hangs from the verification environment, which is consistent with an egress restriction rather than a server-side failure. **TBD:** confirm from an unrestricted network before relying on it.

**2. AWS Open Data (NODD) — `s3://noaa-ndfd-pds/`**

Registry: https://registry.opendata.aws/noaa-ndfd/ · anonymous access, no credentials.

Three distinct trees with different structures and different purposes:

- **`opnl/`** — live mirror of the operational `ds.*.bin` files. **Only 8 areas**: the master domains. The 16 CONUS subsectors are absent. Byte-identical to TGFTP (`opnl/AR.conus/VP.001-003/ds.maxt.bin` = 3,239,651 bytes, matching the TGFTP file exactly).
- **`expr/`** — experimental elements, 5 areas. See *Notes*.
- **`wmo/`** — **the archive.** Organized `wmo/<element>/YYYY/MM/DD/<WMOHEADER>_<CCCC>_<YYYYMMDDHHMM>`, continuous back to 2020. A different artifact from the `opnl/` files — see the wrapper warning below.

> **`opnl/` and `expr/` have no history.** Bucket versioning is off: `list-object-versions` on `opnl/AR.conus/VP.001-003/ds.maxt.bin` returns a single entry with `VersionId: null`. Objects are overwritten in place every cycle. **All NDFD archive access goes through `wmo/`, NCEI, or the user's own harvesting.**

Three spreadsheets of full-resolution element definitions sit at the bucket root (`NDFDelem_fullres_201906.xls`, `..._202203.xls`, `..._202206.xls`); the most recent is from June 2022.

**3. NCEI archive — THREDDS**

```
https://www.ncei.noaa.gov/thredds/catalog/model/ndfd.html
```

Four dataset scans, all serving OPeNDAP, NetcdfSubset, WCS, WMS, NCML, ISO, and plain HTTP:

| Catalog | Extent (verified 2026-08-07) |
|---|---|
| `model-ndfd-file-old` (historical) | 2018-06 → 2020-05, 24 months |
| `model-ndfd-file` (current) | 2020-06 → 2026-08, **57 of 75 expected months** |
| `model-ndfd-file_kwbn` | 2020-06 → 2026-08, 57 months, KWBN-originated bulletins only |
| `model-ndfd-file_kwbn-old` | historical, KWBN-only |

> **The NCEI archive has an 18-month hole.** Months present after 2024-08 are only **2025-08 and 2026-04 through 2026-08**. Everything from 2024-09 to 2026-03 is missing except 2025-08. Verified as a genuine gap, not a catalog-scan artifact: a direct request for `model-ndfd-file/access/202501/catalog.xml` returns **HTTP 404**. Anyone needing continuous history across 2024–2026 must use the AWS `wmo/` tree, which has no such gap.

The unfiltered catalog also carries non-KWBN bulletins — NHC-originated files (`LABZ97_KNHC_...`) appear alongside KWBN ones. The `_kwbn` catalogs restrict to core NDFD.

NCEI also lists an alternate route for data before 2008-10-06 via the Archive Information Request System (AIRS), limited to one day per request. Product page: https://www.ncei.noaa.gov/products/weather-climate-models/national-digital-forecast-database

**4. NCEI prod-model browser**

```
https://www.ncei.noaa.gov/oa/prod-model/index.html#national-digital-forecast-database
```

Web front end onto the same NCEI holdings.

**Not on NOMADS.** NDFD is distributed through MDL/TGFTP rather than NCEP's production suite, so it does not appear under `nomads.ncep.noaa.gov/pub/data/nccf/com/`. Its principal input, [NBM](./nbm.md), does.

---

## Notes

> **The `wmo/` archive files are SBN-framed and do not begin with `GRIB`.** Live-verified on `wmo/maxt/2026/08/06/YGAZ98_KWBN_202608060046`: each file opens with an 18-byte NOAAPORT/SBN length frame followed by a WMO abbreviated heading, and multiple bulletins are concatenated:
>
> ```
> ****0000043543****\nYGAZ98 KWBN 060046\r\r\n  ← first bulletin, GRIB at offset 80
> ****0000014615****\nYGAC00 KWBN 060046\r\r\n  ← second bulletin, same file
> ```
>
> ecCodes scans forward for the `GRIB` magic and therefore parses these correctly, but any reader that assumes byte 0 is the start of a GRIB2 message — or that one file is one bulletin — will fail or silently read only part of the file. This is the same class of problem as the Fortran record wrappers on [ETSS](../../../storm_surge_models/regional/usa/etss.md) and [P-ETSS](../../../storm_surge_models/regional/usa/petss.md), and it means the `wmo/` archive and the `opnl/` live files are **not interchangeable artifacts**: `opnl/` gives one element on one full grid across all steps, `wmo/` gives WMO-tiled bulletins keyed by header.

> **`ds.wx` and `ds.wwa` cannot be decoded with stock GRIB2 tables.** Both use NWS local-use parameter numbers — weather is discipline 0 / category 1 / **parameter 192**, watches-warnings-advisories is discipline 0 / category 19 / **parameter 217** — and both carry a **compressed GRIB2 Section 2 (Local Use)** holding the string table that maps encoded integers to weather types and hazard names. Plain numeric elements such as `maxt` have no Section 2 at all. ecCodes 2.34.1 reports `shortName = unknown` and `name = unknown` for both, and the gridded values are meaningless without the Section 2 table. Decoding requires **degrib** or NDFD-aware tooling. Verified section layout for `ds.wx.bin`: sections 1, **2 (2015 bytes)**, 3, 4, 5, 6, 7; for `ds.maxt.bin`: sections 1, 3, 4, 5, 6, 7.

> **Scope flag — `VP.008-450` contains the CPC extended-range and seasonal outlooks as raw gridded GRIB2.** This conflicts with existing repository guidance. The climate model template instructs entries to "distinguish the raw distributed model output (in scope) from viewer-only official outlook products built on top of it, e.g., CPC seasonal outlooks (out of scope)", and the wiki's *Systems Not in the Catalog* page is slated to cover CPC graphical maps on the same basis. That guidance assumes the CPC outlooks exist only as rendered imagery. They do not: `ds.tmpabv90d.bin` is 13 GRIB2 messages of probability-of-above-normal temperature on the full 2145 × 1377 CONUS 2.5 km Lambert grid, PDT 9, `indicatorOfUnitOfTimeRange = 3` (month), `forecastTime` 1–13 with `lengthOfTimeRange = 3` — the 13 overlapping three-month seasonal leads. The 30-day product is a single message at 1-month lead, 1-month length. **This needs a decision before the `COPERNICUS.md` / climate-category scope wording is finalized**, and it bears on whether CPC warrants its own entry rather than only a wiki mention. Flagged here, not resolved.

> **Encoding defect in the 14-day outlook files.** `ds.tmpabv14d.bin` declares `indicatorOfUnitOfTimeRange = 2` (day) but writes `forecastTime = 168` — a value in **hours**. ecCodes multiplies through and derives `startStep = 4032`, i.e. 168 days, giving a step range of `4032-4200` against a `dataDate` of 2026-08-08 and a `validityDate` of 2026-08-21. The overall-time-interval keys (`yearOfEndOfOverallTimeInterval` etc.) are correct and should be used instead of the derived step. The 30-day and 90-day files use month units correctly and are unaffected.

- **The `expr/` tree is roughly one-fifth abandoned.** 58 elements under `expr/AR.conus/VP.001-003/`; **46 updated within the last two days, 12 stale by about a year** — `cig`, `llwsdir`, `llwshgt`, `llwsspd`, `vis` (all 2025-06-26), and `fret`, `fretdep`, `tcfrt`, `tcsst`, `tctt`, `tcwt`, `wbgt` (all 2025-03-03). Every one of those twelve now appears in the **operational** tree, so these look like leftovers from elements that graduated to operational status without the experimental copies being removed. Live experimental-only elements worth knowing about include **`heatrisk`** (HeatRisk), **`ppi`**, **`snowratio`**, and the snow-exceedance percentiles (`snow24e10`/`snow24e90`, `snow48e*`, `snow72e*`). Do not assume presence in `expr/` implies an active experimental product — check the timestamp.

- **Oceanic high-seas grids are experimental.** OPC's high-seas gridded forecasts in NDFD were opened for public comment under **PNS 25-81** (issued 2025-12-22) with a comment period running through **2026-09-30**. The `AR.oceanic` domain carries only 5 elements (`waveh`, `wdir`, `wgust`, `wspd`, `wwa`) and should be treated as experimental pending a Service Change Notice.

- **Relationship to [NBM](./nbm.md) is the single most important thing to understand about NDFD.** NBM is the guidance; NDFD is the official forecast. For days 4–7 over CONUS the two are now very close, since NBM serves directly as that forecast with only minor WPC editing. For days 1–3 they diverge more, because that is where WFO editing concentrates. Neither is a substitute for the other: **NBM is reproducible and attributable to a documented algorithm; NDFD is not.** Comparative verification between them is effectively a measure of forecaster value-added, and should be interpreted with that in mind.

- **Watch the [NAM](./nam.md) retirement.** NAM-DNG has historically populated NDFD grids. NAM retires **2026-10-06** under SCN 26-47, concurrent with [RRFS](./rrfs.md) implementation. Re-verify the guidance chain afterwards.

- **The XML/REST web services are out of scope but are what most consumers actually use.** MDL operates SOAP and REST services returning NDFD data as Digital Weather Markup Language (DWML) — point and small-area extractions, not gridded files. Three tiers exist: `graphical.weather.gov/xml` (legacy on-prem, 3-hour / 5 km, frozen at roughly the 2016 element set), `preview.weather.gov/xml` (experimental on-prem, 1-hour / 2.5 km), and `digital.weather.gov/xml` (since April 2022, AWS-hosted, 1-hour / 2.5 km, carrying the post-2016 elements the on-prem services lack). MDL asks that point requests be made no more than once per hour. These are **not** in catalog scope — XML point extraction is not gridded forecast data — but they are worth naming, since a large fraction of third-party "NWS forecast" displays are built on them rather than on the GRIB2 files.

- **No `.idx` sidecars, and files are element-concatenated.** A single CONUS days 1–3 element file runs from a few hundred kilobytes (`tcwspdabv*`, 3.4 KB) to ~51 MB (`wspd`, `wgust`). Full CONUS days 1–3 is roughly 500 MB per refresh, at twice hourly. There is no server-side subsetting on TGFTP or AWS; NCEI's THREDDS NetcdfSubset and OPeNDAP services are the only route to partial retrieval, and they cover the archive rather than the live feed.

---

## Recent version history

NDFD does not carry a version number in the way NBM or HRRR do. It evolves by element additions and cadence changes announced through Technical Implementation Notices and Service Change Notices rather than through numbered releases.

### 2025-12-22 — PNS 25-81: experimental OPC high-seas gridded forecasts
Public comment solicited through 2026-09-30 on new high-seas gridded forecasts from the Ocean Prediction Center. Corresponds to the `AR.oceanic` domain.

### 2022-04 — XML web services migrate to AWS
`digital.weather.gov/xml` moved to AWS-hosted prototype servers at 1-hour / 2.5 km resolution, exposing post-2016 elements unavailable from the frozen on-prem services. A new `XMLformat` parameter was added to all SOAP functions, breaking some legacy SOAP POST clients.

### 2015-06-30 — TIN 15-28: CONUS days 1–3 to twice hourly
Update frequency for CONUS forecast days 1–3 doubled from once to twice per hour. Confirmed still in effect by live file counts (48 `YEUZ98_KWBN` files on 2026-08-06).

### 2006-11-28 — TIN 06-51 / 06-72: database extension moves to 22 UTC
The daily 24-hour extension of the database (adding a new day 7) moved from 18 UTC to 22 UTC, allowing forecasters to incorporate later model guidance and complete inter-office collaboration. Days 4–7 files gained a fifth daily update as a result. Confirmed still in effect by the observed 5×/day `YEUZ97_KWBN` cadence.

---

## Official documentation
- MDL NDFD home: https://vlab.noaa.gov/web/mdl/ndfd
- NCEI product page: https://www.ncei.noaa.gov/products/weather-climate-models/national-digital-forecast-database
- NCEI THREDDS catalog: https://www.ncei.noaa.gov/thredds/catalog/model/ndfd.html
- NCEI prod-model browser: https://www.ncei.noaa.gov/oa/prod-model/index.html#national-digital-forecast-database
- AWS Open Data registry: https://registry.opendata.aws/noaa-ndfd/
- NDFD XML/REST web services: https://digital.weather.gov/xml/ and https://graphical.weather.gov/xml/rest.php
- TIN 15-28 (CONUS days 1–3 to twice hourly): https://www.weather.gov/media/notification/tins/tin15-28ndfd_latency_aaa.pdf
- TIN 06-51 (database extension to 22 UTC): https://www.weather.gov/media/notification/tins/tin06-51ndfd.pdf
- TIN 06-72 (reminder, with FTP posting times): https://www.weather.gov/media/notification/tins/tin06-72ndfd_update_time_remind.pdf
- NWS notifications index (SCN/PNS/TIN): https://www.weather.gov/notification/
