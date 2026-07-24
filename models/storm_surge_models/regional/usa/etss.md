# ETSS (Extra-Tropical Storm Surge model)

## What this model is
**ETSS** is NOAA's operational deterministic storm surge model for extra-tropical systems along the U.S. coastline. It runs four times daily and produces hourly storm surge and storm tide guidance for the contiguous U.S., Alaska and Puerto Rico.

The model is a modified version of **SLOSH** (Sea, Lake and Overland Surges from Hurricanes). SLOSH was adapted in the 1990s to take Global Forecast System winds as input instead of a parametric tropical wind model, producing ETSS (Kim et al. 1996). Current versions use **hourly 13 km GFS wind and pressure** as atmospheric forcing.

Three distinct water level quantities are distributed, and the distinction matters:
- **`stormsurge`** — the surge component alone
- **`stormtide`** — surge plus tide, i.e. total water level
- **`tide`** — the astronomical tide (published on one grid only)

ETSS is initialised with a water level anomaly derived per computational domain from the average of station 5-day anomalies, where a station's 5-day anomaly is the mean hourly difference between observation and modelled surge-plus-tide. Post-processing then combines the raw surge guidance with station-based tidal predictions and observations, where available, to produce bias-corrected total water level guidance at stations.

> **ETSS no longer has an independent version number.** Under SCN 23-80 (effective 15 August 2023), MDL merged ETSS v2.5 into the **P-ETSS** package, stating that going forward ETSS would be part of the P-ETSS system with no separate version. This is why ETSS is distributed from the `petss/prod` directory rather than a directory of its own, and why the current release is identified as **P-ETSS v1.4.5**. ETSS and [P-ETSS](petss.md) are separate models — one deterministic, one ensemble — sharing a single software package and a single distribution path.

ETSS should be used as guidance for extra-tropical systems. For tropical cyclones, NOAA directs users to P-Surge instead.

---

## Who runs it
- **Organization:** NOAA / National Weather Service / Meteorological Development Laboratory (MDL). MDL has since moved into the new **Office of Modeling Development (OMD)**; documentation still appears under the MDL Virtual Lab pages.
- **Original development:** Kim, Chen and Shaffer (1996), building on the SLOSH model of Jelesnianski, Chen and Shaffer (1992)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** U.S. East Coast, Gulf of America, U.S. West Coast, Alaska, and Puerto Rico / U.S. Virgin Islands
- **Domain structure:** SLOSH basins rather than a single grid. ETSS uses large extra-tropical basins nested with the finer tropical SLOSH basins, so that the broad extent of the extra-tropical grids is combined with the finer overland detail of the tropical ones. Five fine-resolution Alaska basins — Deadhorse (SCC), Wainwright (AWI), Kotzebue (OTZ), Nome (OME) and King Salmon (AKN) — are nested within the coarser Bering, Beaufort and Chukchi Sea (BBC) basin.
- **Distributed grid resolutions:** 2.5 km and 625 m for CONUS, 3 km for Alaska, 625 m for Puerto Rico (see the grid table under *Data availability*)
- **Inundation coverage:** **Yes.** Original ETSS deliberately omitted overland flooding so it would run efficiently on operational computers. Overland calculation was added for the East Coast and Gulf in ETSS v2.0 (May 2015) and extended to all U.S. coastal areas, based on surge plus tide, in v2.1 (November 2015).

---

## Basic details
- **Model type:** Deterministic storm surge / total water level model
- **Core hydrodynamic model:** SLOSH, modified for extra-tropical forcing
- **Dimensionality:** 2D depth-averaged
- **Forecast length:** **102 hours.** Extended from 96 h to 102 h in ETSS v2.2 (SCN 17-63, effective December 2017), having earlier gone from 48 h to 96 h in 2007. (Documentation-sourced; not independently verified — see *Verification status*.)
- **Update frequency / cycles:** **4× daily (00, 06, 12, 18 UTC)** — all four confirmed present in the live directory listing
- **Temporal output resolution:** Hourly
- **Initial water level condition:** Per-domain anomaly derived from the average of station 5-day anomalies (observation minus modelled surge + tide). The value applied in each cycle is published as `etss.tCCz.init_wl.txt`.

---

## Grid and bathymetry
- **Grid type:** SLOSH polar/elliptical computational basins, post-processed onto regular distribution grids
- **Distribution grid resolutions:** CONUS 2.5 km and 625 m, Alaska 3 km, Puerto Rico 625 m
- **Grid definitions (projection, dimensions, corner points):** **TBD** — not verifiable, see *Verification status*
- **Bathymetry / topography:** SLOSH basin bathymetry and topography, updated basin by basin over successive versions (Alaska, CONUS West, East and Gulf basins updated in the ETSS 2.x series; Seattle, San Francisco, Puerto Rico, U.S. Virgin Islands, Fort Myers and Kotzebue domains upgraded in P-ETSS v1.4.3)
- **Bottom friction:** Spatially varying, introduced in ETSS v2.5 / P-ETSS v1.3 (August 2023). Earlier versions used a uniform value.
- **Wetting and drying:** Yes — overland inundation is computed

---

## Vertical datum and reference level
- **Vertical datum:** **TBD.** SLOSH-family models reference water levels to **NGVD29** and **NAVD88** depending on basin, per MDL's storm surge FAQ, and datum information at stations is maintained per station (P-ETSS v1.3.6 re-synchronised station tidal constituents and station datums; v1.4.5 updated datum information for 17 stations). Which datum applies to each distributed grid product is not stated in the product documentation and could not be read from the files.
- **What the water level fields represent:**
  - `stormsurge` — surge above the reference datum, excluding tide
  - `stormtide` — surge plus tide (total water level)
  - `tide` — astronomical tide alone
  - `max_stormtide` — maximum storm tide over the run
- **Datum conversion offsets provided?** Not as a distributed product. Station datum metadata is maintained internally by MDL.
- **Sea level rise handling:** Accounted for **statistically at stations**, not dynamically. The station post-processing methodology developed in 2000 exists specifically to account for sea level rise, waves, river flooding and model error, none of which the hydrodynamic model itself represents.

> **Caution — the gridded and station products are not equivalent.** The gridded GRIB2 output is raw model guidance. The station output has been bias-corrected against recent observations and combined with station tidal predictions, and carries statistical adjustments for sea level rise, waves and river discharge that the grids do not. The two will not agree, and the difference is intentional.

---

## Tide handling
- **Are tides included?** Yes, but **not computed dynamically in the surge model**. ETSS computes surge; tide is combined with it to form storm tide. Inundation has been calculated from surge plus tide since v2.1.
- **Tidal source:** Station-based tidal predictions, with tidal constituents maintained per station. Tidal phase corrections have been applied in several releases (v2.2 for CD2, ETP3 and AP3; v2.3.6 for two Alaska stations), and the West Coast constituent set was upgraded in P-ETSS v1.4.3.
- **Separation of components:** **Yes, and unusually completely.** Surge, storm tide and tide are all published as separate products — though not all three on every grid (see the grid table below).
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
  1. **Initialisation.** Each run starts from a water level anomaly computed per domain from station 5-day anomalies against observations.
  2. **Post-processing.** A station-based bias correction built on recent observations, extended in v1.3 with a Fourier-based scheme to improve station surge guidance. Bias correction can be toggled off per station: v1.4.5 disabled it for 36 stations while adding it for 9 Eastern Region and 4 Alaska Region stations.

---

## What it provides

**Gridded, GRIB2**
- `stormsurge` — hourly surge
- `stormtide` — hourly surge plus tide
- `tide` — hourly astronomical tide
- `max_stormtide` — maximum storm tide over the run

**Station, text**
- `stormsurge.RGN.txt` and `stormtide.RGN.txt` for six regions
- `shef.tar.gz` — SHEF-encoded station bulletins
- `csv.tar.gz` — station output in CSV

**Diagnostic**
- `init_wl.txt` — the initial water level anomaly applied in the cycle
- `mdlsurge.{a,e,g,k,w,z}` — small per-basin MDL surge files; content and basin mapping **TBD**

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and FTP)
- **License:** **U.S. Government work — public domain.** NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded GRIB2 on four distribution grids, plus station text, SHEF and CSV.
- **Data formats:** **GRIB2** (grids), **plain text** (station), **SHEF** (bulletins, in a tar archive), **CSV** (in a tar archive)
- **Station list:** Not published as a standalone file. Station identifiers appear inside the regional text products and use CO-OPS station IDs where an association exists; some legacy NWS Handbook-5 identifiers were replaced by CO-OPS IDs in ETSS v2.2.

### Products by grid (from the live directory listing, 2026-07-23)

| Grid | `stormsurge` | `stormtide` | `max_stormtide` | `tide` |
|---|---|---|---|---|
| `con2p5km` — CONUS 2.5 km | ✅ 22 MB | ✅ 27 MB | ✅ 283 KB | ❌ |
| `con625m` — CONUS 625 m | ❌ | ✅ 207 MB | ✅ 2.2 MB | ✅ 194 MB |
| `ala3km` — Alaska 3 km | ✅ 60 MB | ✅ 63 MB | ✅ 633 KB | ❌ |
| `pr625m` — Puerto Rico 625 m | ✅ 9.2 MB | ✅ 7.8 MB | ✅ 85 KB | ❌ |

**The coverage matrix is asymmetric and easy to trip over.** The high-resolution CONUS 625 m grid is the only one carrying `tide`, and the only one **not** carrying `stormsurge`. If you want isolated surge at 625 m over CONUS, it is not published — you would have to subtract `tide.con625m` from `stormtide.con625m` yourself. Conversely, if you want tide on any grid other than CONUS 625 m, it does not exist.

### Station text regions

| Code | Region |
|---|---|
| `east` | U.S. East Coast |
| `west` | U.S. West Coast |
| `goam` | Gulf of America |
| `goak` | Gulf of Alaska |
| `nwak` | Northwest Alaska |
| `prvi` | Puerto Rico / U.S. Virgin Islands |

The `goam` code reflects the Gulf naming change applied in P-ETSS v1.3.8 (SCN 25-25, March 2025) and corrected in v1.3.9.

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/petss/prod/etss.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/petss/prod/etss.YYYYMMDD/
```
Note the directory is **`petss/prod`**, not `etss/prod` — a consequence of the 2023 package merge. ETSS and [P-ETSS](petss.md) share it, as `etss.YYYYMMDD/` and `petss.YYYYMMDD/`. Retention is approximately **two days**.

File naming: `etss.tCCz.PRODUCT.GRID.grib2` for grids, `etss.tCCz.PRODUCT.RGN.txt` for stations, where `CC` is the cycle (00, 06, 12, 18).

> **There is no AWS mirror and no long-term archive.** Unlike the STOFS family, ETSS is not part of NOAA Open Data Dissemination — no `noaa-*-petss-pds` or equivalent bucket exists. Combined with the two-day NOMADS retention, this means **there is no public archive of ETSS output**. Anyone needing a time series must harvest it themselves on a rolling basis. This is the single biggest practical limitation of the dataset.

> **The NDGD route is closed.** ETSS and P-ETSS data were **removed from the National Digital Guidance Database** under SCN 25-57, effective 2 September 2025. Older documentation and third-party guides still point at NDGD paths such as `tgftp.nws.noaa.gov/SL.us008001/ST.opnl/DF.gr2/DC.ndgd/GT.slosh/` — as of July 2026 that directory tree still exists but is empty, with its last modification in 2020. Do not build against it.

- **Approximate volume:** ~600 MB per cycle, ~2.4 GB per day, dominated by `stormtide.con625m` (207 MB) and `tide.con625m` (194 MB).

---

## Notes

### Verification status
Unlike most entries in this catalog, **the GRIB2 internals of ETSS could not be live-verified.** ETSS has no S3 mirror, and NOMADS was not reachable for direct file retrieval during compilation. Accordingly:

- **From the live directory listing (2026-07-23/24):** file inventory, product-by-grid coverage matrix, file sizes, cycle count, retention, naming convention, region codes.
- **From official NOAA documentation:** model lineage, forcing, forecast length, version history, domain structure, post-processing methodology, NDGD removal.
- **Not verified, marked TBD:** GRIB2 parameter numbers and `shortName` values, grid definitions (projection, `Ni`/`Nj`, corner coordinates), `generatingProcessIdentifier` as actually encoded, bitmap and missing-value handling, per-product vertical datum as encoded in the files, forecast hour range as actually present in the messages.

These should be filled in by anyone able to decode a live file. Given the pattern documented for [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — where NCEP local-use parameters in the 192–254 range decode as `shortName = unknown` — it is likely that ETSS products need the same fallback of reading `discipline`, `parameterCategory` and `parameterNumber` directly, but this has **not** been confirmed for ETSS and no parameter numbers are asserted here.

### Other notes
- **`generatingProcessIdentifier` is the documented model discriminator.** SCN 17-63 changed ETSS's GRIB2 process ID from 12 to **16**, explicitly so that P-Surge (**12**), ETSS (**16**) and P-ETSS (**18**) could be told apart. If that assignment still holds, it is a cleaner discriminator than anything available in the STOFS family, where the same key is inconsistent within a single cycle. Worth confirming against a live file.
- **GRIB2 values are metres to three decimal places, but the underlying data is in tenths of a foot.** SCN 17-63 reduced the encoding from five decimals to three, noting no precision was lost because the native values are tenths of a foot and are uniquely recoverable from three-decimal metres. Expect quantisation artefacts on the order of 0.03 m if you treat the values as continuous.
- **A two-degree westward error existed in East Coast wind interpolation.** Corrected in P-ETSS v1.4.3 (SCN 25-57, effective 2 September 2025). Any archived ETSS output from before that date carries the error on the East Coast.
- **The tide–surge relationship is additive, not interactive.** Because tide is combined with surge in post-processing rather than integrated into the hydrodynamic solution, `stormtide` is essentially `stormsurge` plus a tidal prediction. Nonlinear interaction between the two — which matters in shallow, frictional environments — is not represented.
- **Physics deliberately excluded, then restored statistically.** The hydrodynamic model does not represent waves, river flooding, sea level rise, or model bias. All four are handled by the station post-processing. This means the station products carry corrections that the gridded products do not, and neither is a complete picture on its own.
- **Relationship to other systems:**
  - Ensemble sibling: [P-ETSS](petss.md) — same package, same distribution directory, same underlying SLOSH configuration, but driven by a multi-model ensemble (31-member GEFS plus 21-member GEPS as of v1.1) rather than deterministic GFS.
  - Tropical counterpart: P-Surge — SLOSH driven by NHC advisory error statistics, run only when a tropical cyclone is active. ETSS should not be used for tropical systems.
  - Overlapping deterministic system: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — global ADCIRC, tides integrated dynamically, no station bias correction on grids. ETSS and STOFS-2D-Global both provide U.S. coastal surge guidance four times daily and will disagree; they are different models with different physics, not versions of one another.
  - Viewers (not data sources): ET-Surge 2.0 at https://slosh.nws.noaa.gov/etsurge2.0/ and the original at https://slosh.nws.noaa.gov/etsurge

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
