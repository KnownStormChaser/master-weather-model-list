# ETSS (Extra-Tropical Storm Surge model)

## What this model is
**ETSS** is NOAA's operational deterministic storm surge model for extra-tropical systems along the U.S. coastline. It runs four times daily and produces hourly storm surge and storm tide guidance out to 102 hours for the contiguous U.S., Alaska and Puerto Rico.

The model is a modified version of **SLOSH** (Sea, Lake and Overland Surges from Hurricanes). SLOSH was adapted in the 1990s to take Global Forecast System winds as input instead of a parametric tropical wind model, producing ETSS (Kim et al. 1996). Current versions use **hourly 13 km GFS wind and pressure** as atmospheric forcing.

Three distinct water level quantities are distributed, and the distinction matters:
- **`stormsurge`** — the surge component alone
- **`stormtide`** — surge plus tide, i.e. total water level
- **`tide`** — the astronomical tide (published on one grid only)

ETSS is initialised with a water level anomaly derived per computational domain from the average of station 5-day anomalies, where a station's 5-day anomaly is the mean hourly difference between observation and modelled surge-plus-tide. Post-processing then combines the raw surge guidance with station-based tidal predictions and observations, where available, to produce bias-corrected total water level guidance at stations.

> **ETSS no longer has an independent version number.** Under SCN 23-80 (effective 15 August 2023), MDL merged ETSS v2.5 into the **P-ETSS** package, stating that going forward ETSS would be part of the P-ETSS system with no separate version. This is why ETSS is distributed from the `petss/prod` directory rather than a directory of its own, and why the current release is identified as **P-ETSS v1.4.5**. ETSS and [P-ETSS](petss.md) are separate models — one deterministic, one ensemble — sharing a single software package, a single distribution path, and identical distribution grids.

ETSS should be used as guidance for extra-tropical systems. Every station bulletin it produces carries the literal header line `NOT VALID FOR TROPICAL STORMS`. For tropical cyclones, NOAA directs users to P-Surge instead.

---

## Who runs it
- **Organization:** NOAA / National Weather Service / Meteorological Development Laboratory (MDL). MDL has since moved into the new **Office of Modeling Development (OMD)**; documentation still appears under the MDL Virtual Lab pages. Products are attributed in-band to `DOC/NOAA/NWS/OSTI/MDL`.
- **Original development:** Kim, Chen and Shaffer (1996), building on the SLOSH model of Jelesnianski, Chen and Shaffer (1992)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** U.S. East Coast, Gulf of America, U.S. West Coast, Alaska, and Puerto Rico / U.S. Virgin Islands
- **Domain structure:** SLOSH basins rather than a single grid. ETSS uses large extra-tropical basins nested with the finer tropical SLOSH basins, so that the broad extent of the extra-tropical grids is combined with the finer overland detail of the tropical ones.
- **Basin count:** **43 computational basins**, live-verified from `init_wl.txt` on the 2026-07-23 00Z cycle. These run from Penobscot Bay down the East Coast, through the Gulf, up the West Coast, and across Alaska to Puerto Rico and the Virgin Islands, and include the five fine-resolution Alaska basins nested within the coarser Bering/Beaufort/Chukchi basin — Purdue Bay (`hscc`), Wainwright (`hawi`), Nome (`home`), Kotzebue (`hotz`) and King Salmon (`hakn`) — plus the coarse regional basins `e` (Eastern Coast), `g` (Gulf of America), `n` (Western Coast), `k` (Gulf of Alaska) and `m` (Bering Sea and Arctic).
- **Inundation coverage:** **Yes.** Original ETSS deliberately omitted overland flooding so it would run efficiently on operational computers. Overland calculation was added for the East Coast and Gulf in ETSS v2.0 (May 2015) and extended to all U.S. coastal areas, based on surge plus tide, in v2.1 (November 2015).

---

## Basic details
- **Model type:** Deterministic storm surge / total water level model
- **Core hydrodynamic model:** SLOSH, modified for extra-tropical forcing
- **Dimensionality:** 2D depth-averaged
- **Forecast length:** **102 hours** — live-verified. The hourly gridded products contain **103 GRIB2 messages** with step values `0` through `102`.
- **Update frequency / cycles:** **4× daily (00, 06, 12, 18 UTC)**
- **Temporal output resolution:** Hourly, on grids and in station text; 1-hour interval in SHEF
- **Initial water level condition:** Per-basin anomaly derived from station 5-day anomalies, published each cycle as `etss.tCCz.init_wl.txt` in **feet**. Live-verified: ETSS and [P-ETSS](petss.md) use the **same 43-basin list** but **different anomaly values**, consistent with ETSS computing the anomaly against its deterministic surge and P-ETSS against the ensemble mean.

---

## Grid and bathymetry
- **Grid type:** SLOSH polar/elliptical computational basins, post-processed onto regular NDFD-family distribution grids
- **Bathymetry / topography:** SLOSH basin bathymetry and topography, updated basin by basin over successive versions (Alaska, CONUS West, East and Gulf basins updated in the ETSS 2.x series; Seattle, San Francisco, Puerto Rico, U.S. Virgin Islands, Fort Myers and Kotzebue domains upgraded in P-ETSS v1.4.3)
- **Bottom friction:** Spatially varying, introduced in ETSS v2.5 / P-ETSS v1.3 (August 2023). Earlier versions used a uniform value.
- **Wetting and drying:** Yes — overland inundation is computed

### Distribution grids (all live-verified, 2026-07-23 00Z)

| Grid | Projection | Ni × Nj | Spacing | Relationship to NDFD |
|---|---|---|---|---|
| `con2p5km` | Lambert conformal | 2145 × 1377 | 2539.703 m | NDFD CONUS 2.5 km, unmodified |
| `con625m` | Lambert conformal | 8577 × 5505 | 634.925 m | NDFD CONUS refined 4× |
| `ala3km` | Polar stereographic | 1649 × 1105 | 2976.563 m | NDFD Alaska 6 km refined 2× |
| `pr625m` | Mercator | 677 × 449 | 625.0 m | NDFD Puerto Rico 1.25 km refined 2× |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, first grid point 20.192°N, 238.446°E. Alaska polar stereographic: LaD 60.0°N, first grid point 40.530°N, 181.429°E. Puerto Rico Mercator: LaD 20.0°N, first grid point 16.977°N, 291.972°E.

These are **bit-identical to the [P-ETSS](petss.md) grids**. The `con2p5km` grid is also the CONUS grid used by [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) and [STOFS-3D-Atlantic](stofs-3d-atlantic.md).

---

## Vertical datum and reference level
- **Vertical datum:** **TBD for the gridded products.** SLOSH-family models reference water levels to **NGVD29** or **NAVD88** depending on basin, per MDL's storm surge FAQ, and datum information is maintained per station (P-ETSS v1.3.6 re-synchronised station tidal constituents and station datums; v1.4.5 updated datum information for 17 stations). **Live-verified: the GRIB2 messages carry no datum identifier at all** — every message encodes `typeOfLevel = surface`, `level = 0`. Which datum applies to each grid is not recoverable from the files.
- **SHEF station output is MLLW, in feet** (live-verified from the bulletin headers).
- **Station text output is in tenths of a foot** (live-verified from the bulletin headers), datum not stated in-band.
- **No above-ground-level products.** Unlike [P-ETSS](petss.md), which publishes both `dat` and `agl` variants, every ETSS gridded product is datum-referenced (`typeOfLevel = surface`). There is no inundation-depth product.
- **What the water level fields represent:**
  - `stormsurge` — surge above the reference datum, excluding tide
  - `stormtide` — surge plus tide (total water level)
  - `tide` — astronomical tide alone
  - `max.stormtide` — maximum storm tide over the run
- **Datum conversion offsets provided?** No.
- **Sea level rise handling:** Accounted for **statistically at stations**, not dynamically. The station post-processing methodology developed in 2000 exists specifically to account for sea level rise, waves, river flooding and model error, none of which the hydrodynamic model itself represents.

> **Caution — the gridded and station products are not equivalent.** The gridded GRIB2 output is raw model guidance. The station output has been bias-corrected against recent observations and combined with station tidal predictions, and carries statistical adjustments for sea level rise, waves and river discharge that the grids do not. The two will not agree, and the difference is intentional.

---

## Tide handling
- **Are tides included?** Yes, but **not computed dynamically in the surge model**. ETSS computes surge; tide is combined with it to form storm tide. Inundation has been calculated from surge plus tide since v2.1.
- **Tidal source:** Station-based tidal predictions, with tidal constituents maintained per station. Tidal phase corrections have been applied in several releases (v2.2 for CD2, ETP3 and AP3; v2.3.6 for two Alaska stations), and the West Coast constituent set was upgraded in P-ETSS v1.4.3.
- **Separation of components:** **Yes, and unusually completely.** Surge, storm tide and tide are all published as separate products — though not all three on every grid (see the coverage matrix below).
- **Tide–surge interaction:** Not represented. Because tide is added rather than integrated into the surge computation, nonlinear tide–surge interaction is absent. This is a substantive physical difference from [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), which integrates tides inside ADCIRC.

---

## Forcing and coupling
- **Meteorological forcing:** [GFS](../../../nwp_models/global/usa/gfs.md) wind and mean sea level pressure at **hourly, 13 km** resolution. This replaced 3-hourly 0.5° GFS in ETSS v2.3 (February 2021), which had itself replaced 1° winds in 2014.
- **Wave contribution:** Not modelled dynamically. Accounted for statistically in the station post-processing only.
- **River discharge:** Not modelled dynamically. Accounted for statistically in the station post-processing only.
- **Ocean forcing / boundary conditions:** SLOSH basin boundaries; details TBD
- **Ice forcing:** Not documented (TBD)

---

## Data assimilation
- **Assimilates water level observations:** No, not in the conventional sense — there is no analysis cycle.
- **Observation use:** Substantial, in two places.
  1. **Initialisation.** Each run starts from a water level anomaly computed per basin from station 5-day anomalies against observations.
  2. **Post-processing.** A station-based bias correction built on recent observations, extended in v1.3 with a Fourier-based scheme to improve station surge guidance. Bias correction can be toggled off per station: v1.4.5 disabled it for 36 stations while adding it for 9 Eastern Region and 4 Alaska Region stations.

---

## What it provides

**Gridded, GRIB2** — `etss.tCCz.<PRODUCT>.<GRID>.grib2`

| Grid | `stormsurge` | `stormtide` | `max.stormtide` | `tide` |
|---|---|---|---|---|
| `con2p5km` — CONUS 2.5 km | ✅ 22 MB | ✅ 27 MB | ✅ 283 KB | ❌ |
| `con625m` — CONUS 625 m | ❌ | ✅ 207 MB | ✅ 2.2 MB | ✅ 194 MB |
| `ala3km` — Alaska 3 km | ✅ 60 MB | ✅ 63 MB | ✅ 633 KB | ❌ |
| `pr625m` — Puerto Rico 625 m | ✅ 9.2 MB | ✅ 7.8 MB | ✅ 85 KB | ❌ |

**The asymmetry is real, not a listing artefact** — every ❌ above returns HTTP 404 when requested directly. The high-resolution CONUS 625 m grid is the only one carrying `tide`, and the only one **not** carrying `stormsurge`. If you want isolated surge at 625 m over CONUS it is not published; subtract `tide.con625m` from `stormtide.con625m` yourself. There is also no `max.stormsurge` product on any grid — the maximum envelope exists only for storm tide.

**Station, text** — `etss.tCCz.<PRODUCT>.<RGN>.txt`, for `stormsurge` and `stormtide`

| Code | Region | Stations |
|---|---|---|
| `east` | U.S. East Coast | 210 |
| `west` | U.S. West Coast | 83 |
| `goam` | Gulf of America | 79 |
| `goak` | Gulf of Alaska | 20 |
| `nwak` | Northwest Alaska | 40 |
| `prvi` | Puerto Rico / U.S. Virgin Islands | 14 |
| | **Total** | **446** |

Live-verified counts. The `prvi` total of 14 matches the 14 PR/VI stations named in SCN 26-63. The `goam` code reflects the Gulf naming change applied in P-ETSS v1.3.8 (SCN 25-25, March 2025).

**SHEF** — `etss.tCCz.shef.tar.gz` unpacks to six regional bulletins named `shef.etss.tCCz.totalwater.<RGN>`, covering **451 unique stations**. Storm tide only (`STORM SURGE + TIDE`), referenced to **MLLW in feet**, hourly (`DIH01`), physical element `HMIFV`, WMO header `SRUS70 KWNO TIDTWE`. Station identifiers are NWS Handbook-5 (e.g. `NLHC3`), not the CO-OPS numerics used in the text products.

**CSV** — `etss.tCCz.csv.tar.gz`, station output in CSV

**WMO bulletins** — `etss.tCCz.mdlsurge.{a,e,g,k,w,z}`, plain-text storm surge bulletins in the legacy broadcast format, carrying station **names only, no identifiers**. Live-verified mapping:

| File | WMO header | Stations | First station |
|---|---|---|---|
| `.e` | `FQUS23 KWNO` | 61 | Eastport, ME |
| `.g` | `FQGX23 KWNO` | 18 | Naples, FL |
| `.w` | `FQPZ23 KWNO` | 21 | Cherry Point, WA |
| `.a` | `FQAK23 KWNO` | 26 | Port Clarence Ent., AK |
| `.k` | `FQGA23 KWNO` | 15 | Ketchikan, AK |
| `.z` | `FQAC23 KWNO` | 14 | Shishmaref, AK |

155 stations in total — roughly a third of the 446 in the text products, so these are a curated broadcast subset rather than a complete rendering.

**Diagnostic** — `etss.tCCz.init_wl.txt`, the per-basin initial water level anomaly in feet with basin abbreviations

Note that ETSS publishes no `meta.txt`; [P-ETSS](petss.md) does.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and FTP)
- **License:** **U.S. Government work — public domain.** NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded GRIB2 on four distribution grids, plus station text, SHEF and CSV.
- **Data formats:** **GRIB2 in a Fortran-record wrapper** (see *Notes* — this matters), **plain text**, **SHEF**, **CSV**

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/petss/prod/etss.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/petss/prod/etss.YYYYMMDD/
```
Note the directory is **`petss/prod`**, not `etss/prod` — a consequence of the 2023 package merge. ETSS and [P-ETSS](petss.md) share it, as `etss.YYYYMMDD/` and `petss.YYYYMMDD/`. Retention is approximately **two days**.

File naming: `etss.tCCz.PRODUCT.GRID.grib2` for grids, `etss.tCCz.PRODUCT.RGN.txt` for stations, where `CC` is the cycle (00, 06, 12, 18). Note the maximum-envelope product is `max.stormtide` with a **dot**, not an underscore.

> **There is no AWS mirror and no long-term archive.** Unlike the STOFS family, ETSS is not part of NOAA Open Data Dissemination — no `noaa-*-petss-pds` or equivalent bucket exists. Combined with the two-day NOMADS retention, this means **there is no public archive of ETSS output**. Anyone needing a time series must harvest it themselves on a rolling basis. This is the single biggest practical limitation of the dataset.

> **The NDGD route is closed.** ETSS and P-ETSS data were **removed from the National Digital Guidance Database** under SCN 25-57, effective 2 September 2025. Older documentation and third-party guides still point at NDGD paths such as `tgftp.nws.noaa.gov/SL.us008001/ST.opnl/DF.gr2/DC.ndgd/GT.slosh/` — as of July 2026 that directory tree still exists but is empty, with its last modification in 2020. Do not build against it.

- **Approximate volume:** ~600 MB per cycle, ~2.4 GB per day, dominated by `stormtide.con625m` (207 MB) and `tide.con625m` (194 MB). Roughly one-seventeenth the volume of [P-ETSS](petss.md).

---

## Notes

> **Live-verified, and the single most important practical fact: the `.grib2` files are not plain GRIB2.** As with [P-ETSS](petss.md), every file is wrapped in **Fortran unformatted sequential record markers** — each GRIB2 message is preceded and followed by a 4-byte little-endian record length. A file therefore begins with four bytes of length, not the `GRIB` magic, and `wgrib2`, `eccodes`, `cfgrib` and `xarray` will fail or mis-parse it as delivered.
>
> Verified on five ETSS files across all four grids: leading and trailing markers matched on every record, every record body began with `GRIB`, and the marker structure consumed each file exactly with no trailing bytes. Strip it before decoding:
>
> ```python
> import struct
> def strip_fortran_records(path):
>     d = open(path, 'rb').read()
>     out, off = b'', 0
>     while off < len(d) - 4:
>         n = struct.unpack('<I', d[off:off+4])[0]
>         if n == 0 or off + 8 + n > len(d):
>             break
>         out += d[off+4:off+4+n]
>         off += 8 + n
>     return out
> ```
>
> The wrapper also explains why on-disk file sizes exceed the directory listing figures by roughly 8 bytes per message.

- **GRIB2 encoding by product** (live-verified, 2026-07-23 00Z, `pr625m` and `con625m`):

  | Product | Parameter | eccodes `shortName` | eccodes `name` | Messages | Steps |
  |---|---|---|---|---|---|
  | `stormsurge` | 10/3/**193** | `etsrg` | Extra Tropical Storm Surge | 103 | `0` … `102` |
  | `stormtide` | 10/3/**250** | `unknown` | — | 103 | `0` … `102` |
  | `tide` | 10/3/**251** | `unknown` | — | 103 | `0` … `102` |
  | `max.stormtide` | 10/3/**250** | `unknown` | — | 1 | `102` |

  All carry `centre = kwbc`, `subCentre = 14` (MDL), `generatingProcessIdentifier = 16`, PDT 0, `typeOfLevel = surface`, `level = 0`, no bitmap, `missingValue = 9999`.

- **Two of the four products decode as `unknown`.** Parameters 10/3/250 and 10/3/251 fall in the NCEP local-use range 192–254 and are absent from the eccodes tables at version 2.48. Apply the standard fallback: read `discipline`, `parameterCategory` and `parameterNumber` directly and select on the number. Only `stormsurge` resolves to a name, and that name (`etsrg`) is shared with [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), which uses 10/3/193 for its sub-tidal water level.

- **The tide parameter differs from STOFS.** ETSS encodes astronomical tide as 10/3/**251**; STOFS-2D-Global encodes its harmonic tidal prediction as 10/3/**194** (`elevhtml`). Two NOAA models publish the same physical quantity under different NCEP local parameter numbers. Both, however, use 10/3/250 for total water level.

- **`generatingProcessIdentifier = 16` is confirmed live**, matching the assignment made in SCN 17-63 specifically so P-Surge (**12**), ETSS (**16**) and P-ETSS (**18**) could be told apart. This is a genuinely reliable model discriminator — unlike the STOFS family, where the same key varies within a single cycle. It is the cleanest way to distinguish ETSS from P-ETSS output if filenames have been lost.

- **`max.stormtide` is stamped as an instantaneous field at step 102.** It contains a single message with `stepType = instant`, not `max`, and step `102`. The value is the maximum over the whole run, but nothing in the message says so — a reader trusting the step metadata would take it for an instantaneous 102-hour forecast.

- **No bitmap; land and out-of-domain points are the literal value 9999.0.** Filter explicitly. Valid fractions vary widely by grid and product: on the 00Z cycle, `max.stormtide` covers 81.4% of `pr625m`, 50.3% of `ala3km` and just 10.4% of `con2p5km`, while `tide.con625m` covers only 7.5%.

- **GRIB2 values are metres to three decimal places, but the native data is in tenths of a foot.** SCN 17-63 reduced the encoding from five decimals to three, noting no precision was lost because the native values are tenths of a foot and are uniquely recoverable. This is confirmed by the station text products, whose headers read `IN TENTHS OF FT`. Expect quantisation on the order of 0.03 m if you treat gridded values as continuous.

- **Station text format.** Each regional file opens with a WMO header line, a title line carrying the units and the `NOT VALID FOR TROPICAL STORMS` warning, and a column-header line marking 01Z/06Z/12Z/18Z/00Z. Each station then occupies six lines: a name line combining the 7-digit CO-OPS station ID with the site name, followed by five rows of hourly values (four rows of 24 plus a final row of 6) giving 102 hourly forecasts in tenths of a foot.

- **A two-degree westward error existed in East Coast wind interpolation.** Corrected in P-ETSS v1.4.3 (SCN 25-57, effective 2 September 2025). Any archived ETSS output from before that date carries the error on the East Coast.

- **The tide–surge relationship is additive, not interactive.** Because tide is combined with surge in post-processing rather than integrated into the hydrodynamic solution, `stormtide` is essentially `stormsurge` plus a tidal prediction. Nonlinear interaction between the two — which matters in shallow, frictional environments — is not represented.

- **Physics deliberately excluded, then restored statistically.** The hydrodynamic model does not represent waves, river flooding, sea level rise, or model bias. All four are handled by the station post-processing. This means the station products carry corrections that the gridded products do not, and neither is a complete picture on its own.

- **Relationship to other systems:**
  - Ensemble sibling: [P-ETSS](petss.md) — same package, same distribution directory, same SLOSH configuration, same four distribution grids, but driven by a 52-member GEFS + GEPS ensemble rather than deterministic GFS. P-ETSS publishes probability and exceedance products plus above-ground-level inundation, but no gridded surge-only or tide product.
  - Tropical counterpart: P-Surge — SLOSH driven by NHC advisory error statistics, run only when a tropical cyclone is active. ETSS should not be used for tropical systems.
  - Overlapping deterministic system: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — global ADCIRC, tides integrated dynamically, no station bias correction on grids. ETSS and STOFS-2D-Global both provide U.S. coastal surge guidance four times daily and will disagree; they are different models with different physics, not versions of one another.
  - Viewers (not data sources): ET-Surge 2.0 at https://slosh.nws.noaa.gov/etsurge2.0/ and the original at https://slosh.nws.noaa.gov/etsurge

### Verification status
GRIB2 internals, grid definitions, station counts and file formats in this entry were **live-verified** against the 2026-07-23 00Z cycle by fetching files directly from NOMADS. Note that NOMADS serves individual files reliably but may refuse directory listings to automated clients; fetch by constructed filename rather than by crawling.

One item remains open: **the vertical datum of the gridded products**. The GRIB2 messages carry no datum identifier, and MDL documentation states only that SLOSH-family basins use NGVD29 or NAVD88 without mapping basins to datums. Resolving this requires either the per-basin datum table from MDL or a comparison against a station of known datum.

---

## Upcoming changes

### P-ETSS v1.5 — proposed, not yet scheduled
**PNS 26-32** (16 April 2026) solicited public comment on proposed upgrades to P-ETSS v1.5 through 16 May 2026. Because ETSS ships inside the P-ETSS package, a v1.5 implementation would change ETSS as well. As of 24 July 2026 no Service Change Notice had been issued and no implementation date announced; the current operational release remains **v1.4.5**. A draft science briefing on the v1.5 improvements is linked from the MDL public notices page.

---

## Recent version history

Since August 2023, ETSS carries the P-ETSS package version.

| Version | Date | Notes |
|---|---|---|
| P-ETSS v1.4.5 | 7 Jul 2026 | SCN 26-63. Bias-calculation toggled off for 36 stations; bias correction added for 9 Eastern Region and 4 Alaska Region stations; SHEF support for 8 Alaska and 14 Puerto Rico / USVI stations; datum information updated for 17 stations. |
| P-ETSS v1.4.4 | 10 Nov 2025 | Station output fixes; Kipnuk, AK anomaly calculation corrected. |
| P-ETSS v1.4.3 | 2 Sep 2025 | SCN 25-57. Domain upgrades for Seattle, San Francisco, Puerto Rico, USVI, Fort Myers and Kotzebue; West Coast tidal constituents upgraded; **corrected a two-degree westward shift in East Coast wind interpolation**; **ETSS and P-ETSS removed from the NDGD**. |
| P-ETSS v1.3.8 / v1.3.9 | Mar 2025 | SCN 25-25. Gulf naming convention updated, then corrected. |
| **ETSS v2.5** / P-ETSS v1.3 | 15 Aug 2023 | SCN 23-80. Spatially varying bottom friction; five fine-resolution Alaska basins nested in the BBC basin; Fourier-based station post-processing. **Last independent ETSS version number.** |
| ETSS v2.4 | 28 Jun 2022 | Transitioned to WCOSS2. |
| ETSS v2.3.6 | 7 Sep 2021 | SCN 21-81. Tidal phase correction for two Alaska stations. |
| ETSS v2.3.5 | 27 Jul 2021 | SCN 21-70. Palm Beach (PB3) initial water level method adjusted. |
| ETSS v2.3 | 25 Feb 2021 | SCN 20-106. **Switched to hourly 13 km GFS** from 3-hourly 0.5°. |
| ETSS v2.2 | 6 Dec 2017 | SCN 17-63. Tidal phase shift corrections; **forecast extended to 102 h from 96 h**; GRIB2 process ID changed 12 → 16; GRIB2 encoding reduced to three decimal places. |
| ETSS v2.1 | 3 Nov 2015 | TIN 15-39. New Alaska basin allowing flow through the Bering Strait; **overland inundation from surge plus tide extended to all U.S. coastal areas**. |
| ETSS v2.0 | 19 May 2015 | TIN 15-18. **Overland calculations introduced** for the East Coast and Gulf of Mexico. |
| ETSS v1.5 | 14 Oct 2014 | TIN 14-31. 0.5° GFS winds (from 1°); Bering Sea merge mask corrected; 2.5 km CONUS and 3 km Alaska grids introduced. |
| ETSS | 1 Feb 2011 | TIN 10-64. Gulf of Mexico grid replaced with higher resolution. |
| ETSS | 26 Aug 2008 | TIN 08-39. First gridded products: 5 km CONUS, 6 km Alaska. |
| ETSS | 10 Jul 2007 | TIN 07-26. Forecast extended to 96 h from 48 h. |

---

## Official documentation
- MDL ETSS project page: https://vlab.noaa.gov/web/mdl/etss
- MDL storm surge public notices and full change log: https://vlab.noaa.gov/web/mdl/technical-notices-storm-surge
- MDL storm surge FAQ (datum discussion): https://vlab.noaa.gov/web/mdl/faq-storm-surge
- MDL SLOSH page: https://vlab.noaa.gov/web/mdl/slosh
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/petss/prod/
- SCN 26-63 — P-ETSS v1.4.5, effective 7 July 2026: https://www.weather.gov/media/notification/pdf_2026/scn26-63_petss_v1.4.5.pdf
- PNS 26-32 — P-ETSS v1.5 comment solicitation: https://www.weather.gov/media/notification/pdf_2026/pns26-32_P-ETSS_v1.5.pdf
- SCN 25-57 — P-ETSS v1.4.3, NDGD removal: https://www.weather.gov/media/notification/pdf_2025/scn25-57_PETSS_v1.4.0.pdf
- SCN 23-80 — P-ETSS v1.3 and ETSS v2.5, package merge: https://www.weather.gov/media/notification/pdf_2023_24/scn23-80_p-etss1.3.0_etss2.5.0.pdf
- SCN 17-63 — ETSS v2.2 and first P-ETSS, GRIB2 process IDs: https://www.weather.gov/media/notification/pdfs/scn17-63etss_petss_aac.pdf
- Liu, Taylor and Schuster (2015), "Creating Inundation Guidance from NWS's Extra-Tropical Storm Surge Model": https://www.weather.gov/media/mdl/ETSS2_AMS_abstract_extend.pdf
- Kim, S. C., J. Chen and W. A. Shaffer, 1996: An Operational Forecast Model for Extratropical Storm Surges along the U.S. East Coast. *Preprints, Conference on Coastal Oceanic and Atmospheric Prediction*, Amer. Meteor. Soc., 281–286.
- Jelesnianski, C. P., J. Chen and W. A. Shaffer, 1992: SLOSH: Sea, Lake and Overland Surges from Hurricanes. *NOAA Technical Report NWS 48*, 71 pp.
