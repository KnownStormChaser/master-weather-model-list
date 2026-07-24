# P-ETSS (Probabilistic Extra-Tropical Storm Surge model)

## What this model is
**P-ETSS** is NOAA's operational ensemble storm surge model for extra-tropical systems and weaker tropical systems along the U.S. coastline. It is the probabilistic counterpart to [ETSS](etss.md): the same SLOSH-derived hydrodynamic model, run once per ensemble member instead of once.

P-ETSS takes wind and pressure from **31 members of the 0.25° (27 km) GEFS** and **21 members of the 0.5° (55 km) Meteorological Service of Canada GEPS**, giving **52 members** in total. Each member is run through ETSS to produce an hourly storm tide field, and the 52 results are combined with equal weighting into probabilistic guidance.

Two kinds of probabilistic product come out of that:
- **Threshold probabilities** — the probability of storm tide exceeding a fixed height (e.g. probability of > 5 feet), published as `gt0` … `gt16`
- **Exceedance heights** — the height exceeded by a given fraction of members (e.g. 10% exceedance = the height only 10% of the members beat), published as `e10` … `e90`
- Plus ensemble **`max`**, **`mean`** and **`min`**

Like ETSS, P-ETSS is initialised from a per-basin water level anomaly derived from station 5-day anomalies — here computed against the ensemble mean — and its station output is bias-corrected against tidal predictions and observations in post-processing.

> **P-ETSS is the parent package.** Since SCN 23-80 (August 2023) ETSS has shipped inside the P-ETSS package and no longer carries its own version number. The two models are distributed side by side from the same directory as `petss.YYYYMMDD/` and `etss.YYYYMMDD/`. The current release is **P-ETSS v1.4.5** (7 July 2026).

---

## Who runs it
- **Organization:** NOAA / National Weather Service / Meteorological Development Laboratory (MDL), now part of the Office of Modeling Development (OMD)
- **Country / region:** United States
- **First operational:** December 2017 (P-ETSS v1.0, 21-member GEFS only)

---

## Basic details
- **Model type:** Ensemble (probabilistic) storm surge / total water level model
- **Core hydrodynamic model:** SLOSH via [ETSS](etss.md), run once per ensemble member
- **Dimensionality:** 2D depth-averaged
- **Forecast length:** **102 hours** — live-verified. The `1hr` products carry 102 hourly records with step ranges `1-2` through `102-103`; the `6hr` products carry 17 six-hourly records with step ranges `6-12` through `102-108`.
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly (`1hr` products) and 6-hourly (`6hr` products)
- **Initial water level condition:** Per-basin anomaly from station 5-day anomalies against the ensemble mean, published each cycle as `petss.tCCz.init_wl.txt`

---

## Ensemble configuration
- **Ensemble size:** **52 members** — 31 GEFS + 21 GEPS
- **Ensemble type:** Multi-model / multi-centre (NAEFS). The surge model itself is not perturbed; all spread comes from the driving atmospheric ensembles.
- **Perturbation method:** Inherited entirely from GEFS and GEPS
- **Member weighting:** Equal
- **Products:** exceedance heights, threshold probabilities, and ensemble max/mean/min (see *What it provides*)
- **Individual members distributed?** No. Only derived statistics are published; the 52 member fields are not.

> **Live-verified — the ensemble is partly time-lagged, and this is not visible in the data.** MSC's GEPS runs only at 00Z and 12Z. The P-ETSS **06Z and 18Z** cycles therefore use GEPS guidance that is one cycle (6 hours) old, and when GEPS is late the **00Z and 12Z** cycles may fall back to GEPS that is two cycles (12 hours) old. The 52-member ensemble is consequently a mix of synchronous GEFS members and lagged GEPS members, in a proportion that varies by cycle and is not recorded in the GRIB2 output. The `petss.tCCz.meta.txt` file records only whether NAEFS forcing was used at all, not the GEPS age.
>
> This is the reason the GEFS-only product family exists — see below.

---

## The two product families

Every gridded product is published twice, under two filename prefixes:

| Prefix | Ensemble | Members |
|---|---|---|
| `petss.tCCz.stormtide_…` | **NAEFS** — GEFS + GEPS | 52 |
| `petss.tCCz.gefs_stormtide_…` | **GEFS only** | 31 |

The same split applies to the CSV bundles (`csv.tar.gz` vs `gefs_csv.tar.gz`). The NAEFS family is the primary product; the GEFS-only family is fully synchronous and free of the GEPS lag described above.

**Live-verified (2026-07-23 00Z):** the run's `meta.txt` reads

```
DATE=20260723_00
STATUS=GOOD
FORCING=NAEFS
COMMENT=P-ETSS results are from NAEFS forcing without any warning
```

confirming that the unprefixed family is the NAEFS combination and that a per-cycle `STATUS` flag exists to signal degraded forcing.

> **Caution — the two families are indistinguishable inside the GRIB2.** Decoding `stormtide_gt16_6hr_cum_agl.pr625m.grib2` and `gefs_stormtide_gt16_6hr_cum_agl.pr625m.grib2` from the same cycle gives byte-for-byte equivalent metadata: same parameter, same PDT 9, same `generatingProcessIdentifier = 18`, same grid, same step ranges. Nothing in the message records the ensemble size or composition. **The filename prefix is the only way to tell a 52-member product from a 31-member one.** Preserve it through any ingest pipeline.

---

## Understanding the filenames

With 22 statistics × 4 grids × 4 time/datum variants × 2 families, a single cycle contains **704 GRIB2 files**. The naming grammar is regular:

```
petss.tCCz.[gefs_]stormtide_<STAT>_<WINDOW>_<ACCUM>_<DATUM>.<GRID>.grib2
```

**`<STAT>` — 22 values**

| Group | Values | Meaning |
|---|---|---|
| Exceedance | `e10` `e20` `e30` `e40` `e50` `e90` | Height exceeded by that % of members |
| Threshold | `gt0` `gt1` … `gt10`, `gt13`, `gt16` | Probability of exceeding that many **feet** |
| Summary | `max` `mean` `min` | Ensemble maximum, mean, minimum |

Note the gaps: exceedance skips 60/70/80, and thresholds jump 10 → 13 → 16.

**`<WINDOW>_<ACCUM>_<DATUM>` — only 4 of the 8 conceivable combinations exist**

| Variant | Records | Step ranges | `typeOfLevel` |
|---|---|---|---|
| `1hr_inc_dat` | 102 | `1-2` … `102-103` | `surface` |
| `6hr_inc_agl` | 17 | `6-12` … `102-108` | `heightAboveGround` |
| `6hr_cum_agl` | 17 | `6-12` … `102-108` | `heightAboveGround` |
| `6hr_cum_dat` | 17 | `6-12` … `102-108` | `surface` |

There is no `6hr_inc_dat`, no `1hr_cum_*` and no `1hr_*_agl`.

- **`inc` vs `cum`** — incremental (that window alone) vs cumulative (from forecast start through that window)
- **`dat` vs `agl`** — water level above the vertical datum vs inundation depth above ground level

**`<GRID>` — 4 values**, all refinements of NDFD grids (see the grid table below).

---

## Grid and bathymetry
- **Grid type:** SLOSH computational basins, post-processed onto regular NDFD-family distribution grids
- **Basins:** The `init_wl.txt` file enumerates the operational basins each cycle. Live-verified on 2026-07-23 00Z, 40 basins run from Penobscot Bay through the Gulf and West Coast to Alaska, Puerto Rico and the Virgin Islands, including the five nested fine-resolution Alaska basins (Purdue Bay `hscc`, Wainwright `hawi`, Nome `home`, Kotzebue `hotz`, King Salmon `hakn`) and the coarse `e` / `g` / `n` / `k` / `m` regional basins.
- **Bottom friction:** Spatially varying (introduced in P-ETSS v1.3, August 2023)
- **Wetting and drying:** Yes — overland inundation is computed, which is what the `agl` products represent

### Distribution grids (all live-verified from the 2026-07-23 00Z cycle)

| Grid | Projection | Ni × Nj | Spacing | Relationship to NDFD |
|---|---|---|---|---|
| `con2p5km` | Lambert conformal | 2145 × 1377 | 2539.703 m | NDFD CONUS 2.5 km, unmodified |
| `con625m` | Lambert conformal | 8577 × 5505 | 634.926 m | NDFD CONUS refined 4× |
| `ala3km` | Polar stereographic | 1649 × 1105 | 2976.563 m | NDFD Alaska 6 km refined 2× |
| `pr625m` | Mercator | 677 × 449 | 625.0 m | NDFD Puerto Rico 1.25 km refined 2× |

Lambert parameters: LaD 25.0°N, LoV 265.0°E. Alaska polar stereographic: LaD 60.0°N. The `con2p5km` grid is bit-identical to the CONUS grid used by [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) and [STOFS-3D-Atlantic](stofs-3d-atlantic.md); the other three are refinements rather than the base grids.

---

## Vertical datum and reference level
- **Vertical datum:** **TBD** for the `dat` products. SLOSH-family models reference to NGVD29 or NAVD88 depending on basin, and station datum metadata is maintained per station by MDL (v1.4.5 updated it for 17 stations). The GRIB2 messages encode `typeOfLevel = surface` with `level = 0` and carry no datum identifier.
- **`agl` products** encode `typeOfLevel = heightAboveGround`, `level = 0`, and represent **inundation depth above ground**, not water level above a datum. These are only meaningful where the model floods overland.
- **Units:** GRIB2 values are metres for heights, and **percent (0–100)** for the threshold probability products.
- **Thresholds are in feet, encoded as metres.** Live-verified: `gt16` carries `scaledValueOfUpperLimit = 487680` with `scaleFactorOfUpperLimit = 5`, i.e. 4.8768 m — exactly 16.000 feet. `gt1` gives 0.3048 m, exactly 1 foot.
- **Sea level rise, waves, river discharge:** Not modelled. Accounted for statistically in the **station** post-processing only, exactly as in [ETSS](etss.md). The gridded products carry no such correction.

> **`typeOfLevel` is a reliable in-file discriminator for `dat` vs `agl`,** which is useful because almost nothing else in the message distinguishes the variants. It does **not** distinguish `inc` from `cum` — see below.

---

## Tide handling
- **Are tides included?** Yes, in the `stormtide` products — surge plus tide. Tide is combined with surge rather than integrated into the hydrodynamic solution, so nonlinear tide–surge interaction is not represented.
- **Gridded products are storm tide only.** There is no gridded `stormsurge` product for P-ETSS. Isolated surge appears only in the station text files. This differs from [ETSS](etss.md), which does publish gridded `stormsurge` on three of its four grids.
- **Tidal source:** Station-based tidal predictions with per-station constituents; West Coast constituents upgraded in v1.4.3.

---

## Forcing and coupling
- **Meteorological forcing:** 31-member [GEFS](../../../ensemble_models/global/usa/gefs.md) at 0.25° (27 km) plus 21-member MSC GEPS at 0.5° (55 km)
- **GEPS availability:** 00Z and 12Z only — see the time-lag caution under *Ensemble configuration*
- **Wave contribution:** Not modelled. The Puerto Rico and U.S. Virgin Islands runs in particular are surge + tide only, with no wave setup.
- **River discharge:** Not modelled dynamically
- **Ice forcing:** Not documented (TBD)

---

## Data assimilation
- **Assimilates water level observations:** No analysis cycle.
- **Observation use:** Initialisation from station 5-day anomalies against the ensemble mean, plus station-based bias correction in post-processing (Fourier-based scheme since v1.3). Bias correction is toggleable per station — v1.4.5 disabled it for 36 stations and added it for 13 others.

---

## What it provides

**Gridded, GRIB2** — storm tide only, 704 files per cycle across the grammar described above.

**Station, text** — 5 statistics (`e10`, `e90`, `max`, `mean`, `min`) × 2 products (`stormsurge`, `stormtide`) × 6 regions = 60 files per cycle. Note that stations carry only two exceedance levels against the grids' six.

| Region code | Region |
|---|---|
| `east` | U.S. East Coast |
| `west` | U.S. West Coast |
| `goam` | Gulf of America |
| `goak` | Gulf of Alaska |
| `nwak` | Northwest Alaska |
| `prvi` | Puerto Rico / U.S. Virgin Islands |

**Bundled station output**
- `csv.tar.gz` — NAEFS station output in CSV
- `gefs_csv.tar.gz` — GEFS-only station output in CSV
- `shef.tar.gz` — SHEF-encoded station bulletins

**Diagnostic**
- `init_wl.txt` — per-basin initial water level anomaly in feet, with basin abbreviations
- `meta.txt` — run date, `STATUS` flag, and which forcing was used

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and FTP)
- **License:** **U.S. Government work — public domain.** NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** Both — gridded GRIB2 on four grids, plus station text, SHEF and CSV
- **Data formats:** **GRIB2 in a Fortran-record wrapper** (see *Notes* — this matters), plain text, SHEF, CSV
- **Station list:** Not published standalone; identifiers appear inside the regional text products, using CO-OPS station IDs where available

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/petss/prod/petss.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/petss/prod/petss.YYYYMMDD/
```
Shared with [ETSS](etss.md) as `etss.YYYYMMDD/`. Retention is approximately **two days**.

> **No AWS mirror and no long-term archive.** P-ETSS is not part of NOAA Open Data Dissemination; no `noaa-*-petss-pds` bucket exists. Together with the two-day NOMADS retention this means **there is no public archive** — a time series must be harvested on a rolling basis.

> **The NDGD route is closed.** ETSS and P-ETSS data were removed from the National Digital Guidance Database under SCN 25-57, effective 2 September 2025. Older third-party guides pointing at `tgftp.nws.noaa.gov/…/DC.ndgd/GT.slosh/` are obsolete; that tree is empty.

- **Approximate volume:** roughly **10–11 GB per cycle**, on the order of **40 GB per day** — estimated from listed file sizes, dominated by the `1hr_inc_dat` products on `con625m` (up to 197 MB each). P-ETSS is roughly 17× the volume of ETSS.

---

## Notes

> **Live-verified, and the single most important practical fact: the `.grib2` files are not plain GRIB2.** Every file is wrapped in **Fortran unformatted sequential record markers** — each GRIB2 message is preceded and followed by a 4-byte little-endian record length. A file therefore begins with four bytes of length, not the `GRIB` magic, and `wgrib2`, `eccodes`, `cfgrib` and `xarray` will fail or mis-parse it as delivered.
>
> Verified on five files from the 2026-07-23 00Z cycle across all four grids: leading and trailing markers matched on every record, every record body began with `GRIB`, and the marker structure consumed each file exactly with no trailing bytes. Strip it before decoding:
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
> Whether this wrapper is intentional or an artefact of the MDL packing chain is unknown (TBD), but it has been present consistently and should be assumed.

- **GRIB2 encoding by product type** (live-verified, 2026-07-23 00Z):

  | Product | Parameter | eccodes `shortName` | PDT | Distinguishing key |
  |---|---|---|---|---|
  | Threshold probability (`gt*`) | 10/3/**250** | `unknown` | **9** | `probabilityType = 1`, `scaledValueOfUpperLimit` |
  | Exceedance height (`e*`) | 10/3/**192** | `surge` | **10** | `percentileValue` |
  | Ensemble mean | 10/3/**250** | `unknown` | **8** | `stepType = avg` |
  | Ensemble max / min | 10/3/**250** | `unknown` | **8** | `stepType = max` / `min` |

  All carry `centre = kwbc`, `subCentre = 14` (MDL) and `generatingProcessIdentifier = 18`.

- **`generatingProcessIdentifier = 18` confirms the documented model discriminator is still in force.** SCN 17-63 assigned P-Surge **12**, ETSS **16** and P-ETSS **18** specifically so the three could be told apart. Live decoding confirms 18 for P-ETSS — a cleaner discriminator than anything available in the STOFS family, where the key is inconsistent within a single cycle. It does **not**, however, separate the NAEFS and GEFS-only families, which both report 18.

- **The exceedance labels are inverted relative to the GRIB encoding.** `e10` — "the height exceeded by 10% of members" — is encoded as `percentileValue = 90`. Anyone selecting on `percentileValue` must invert: `e10` → 90th percentile, `e90` → 10th percentile. Confirmed live on `stormtide_e10_6hr_cum_dat.pr625m.grib2`.

- **The `e*` products carry a parameter number that contradicts their filename.** They encode as 10/3/192, which eccodes resolves to `surge` (NCEP local "Storm Surge"), while the filename says `stormtide` and the product genuinely is surge **plus tide**. The threshold and summary products use 10/3/250 instead. Trust the filename, not the parameter name.

- **`inc` and `cum` are numerically different but carry identical GRIB2 metadata.** Comparing `stormtide_gt1_6hr_cum_agl` against `stormtide_gt1_6hr_inc_agl` from the same cycle: same PDT, same step ranges (`6-12` … `102-108`), same level type, same threshold encoding — yet the values differ from the second window onward. The cumulative series is monotonically non-decreasing, as expected. **Only the filename distinguishes them.** As with the family prefix, this must survive your ingest.

- **No bitmap; land and out-of-domain points are the literal value 9999.0.** Consistent with the rest of the NOAA surge family. Valid fractions vary enormously by grid and product — on `pr625m`, the `agl` products carry only 24,351 valid points of 303,973 (8.0%) while the `dat` products carry 247,345 (81.4%), because `agl` is defined only where the model floods.

- **The incremental products drop points from the mask at later steps.** In `stormtide_gt1_6hr_inc_agl`, points valid at the first window return 9999 at later windows, so the valid mask is step-dependent. The cumulative product keeps a fixed mask. Compute masks per step for `inc` products rather than once per file.

- **Relationship to other systems:**
  - Deterministic sibling: [ETSS](etss.md) — same package, same directory, same SLOSH configuration, driven by deterministic GFS instead of an ensemble. ETSS publishes gridded storm surge and a tide product; P-ETSS publishes neither.
  - Tropical counterpart: P-Surge — SLOSH driven by NHC advisory error statistics, event-triggered. P-ETSS covers extra-tropical systems and *weaker* tropical systems; P-Surge covers tropical cyclones proper.
  - Driving ensembles: [GEFS](../../../ensemble_models/global/usa/gefs.md) and MSC GEPS.
  - Overlapping deterministic systems: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) and [STOFS-3D-Atlantic](stofs-3d-atlantic.md), which share the `con2p5km` grid definition but use different physics and datums.
  - Viewer (not a data source): https://slosh.nws.noaa.gov/petss

---

## Upcoming changes

### P-ETSS v1.5 — proposed, not yet scheduled
**PNS 26-32** (16 April 2026) solicited public comment on proposed upgrades to P-ETSS v1.5 through 16 May 2026, with a draft science briefing linked from the MDL public notices page. As of 24 July 2026 no Service Change Notice had been issued and no implementation date announced; the operational release remains **v1.4.5**. Because [ETSS](etss.md) ships in the same package, a v1.5 implementation would change both models.

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| v1.4.5 | 7 Jul 2026 | SCN 26-63. Bias-calculation toggled off for 36 stations; bias correction added for 9 Eastern Region and 4 Alaska Region stations; SHEF support for 8 Alaska and 14 Puerto Rico / USVI stations; datum information updated for 17 stations. |
| v1.4.4 | 10 Nov 2025 | Corrected an inconsistency in the 06Z/18Z **NAEFS** station output caused by failing to skip the initial 0–6 h period when using 6-hour-delayed GEPS; Kipnuk, AK anomaly calculation corrected. |
| v1.4.3 | 2 Sep 2025 | SCN 25-57. Domain upgrades for Seattle, San Francisco, Puerto Rico, USVI, Fort Myers and Kotzebue; West Coast tidal constituents upgraded; corrected a two-degree westward shift in East Coast wind interpolation; **ETSS and P-ETSS removed from the NDGD**. |
| v1.3.9 | 28 Mar 2025 | Corrected a header for the Gulf of America stations. |
| v1.3.8 | 12 Mar 2025 | SCN 25-25. Gulf naming convention updated. |
| v1.3.7 | 14 Nov 2024 | Out-of-bounds error in a merge stage corrected. |
| v1.3.6 | 20 Jun 2024 | Station tidal constituents and datums re-synchronised; New London, CT NWSLI updated `NLNC3` → `NLHC3`. |
| v1.3.5 | 15 Nov 2023 | Mask issue over Delaware / Maryland corrected. |
| v1.3.4 | 27 Sep 2023 | SCN 23-94. SHEF encoding error and several Alaska stations corrected. |
| v1.3 | 15 Aug 2023 | SCN 23-80. Spatially varying bottom friction; five fine-resolution Alaska basins nested in the Bering/Beaufort/Chukchi basin; Fourier-based station post-processing. **ETSS v2.5 merged into the package; ETSS version numbering ends.** |
| v1.2 | 28 Jun 2022 | Transitioned to WCOSS2. |
| v1.1.5 | 7 Sep 2021 | SCN 21-81. Tidal phase correction for two Alaska stations. |
| v1.1.4 | 27 Jul 2021 | SCN 21-70. Palm Beach (PB3) initial water level method adjusted. |
| v1.1 | 25 Feb 2021 | SCN 20-106. **Expanded to 52 members** (31 GEFS + 21 GEPS) from 21; ETSS switched to hourly 13 km GFS. |
| v1.0 | 6 Dec 2017 | SCN 17-63. First operational version, 21-member GEFS. GRIB2 process ID 18 assigned to distinguish P-ETSS from ETSS (16) and P-Surge (12). |

---

## Official documentation
- MDL P-ETSS project page: https://vlab.noaa.gov/web/mdl/petss
- MDL storm surge public notices and full change log: https://vlab.noaa.gov/web/mdl/technical-notices-storm-surge
- MDL storm surge FAQ (datum discussion): https://vlab.noaa.gov/web/mdl/faq-storm-surge
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/petss/prod/
- SCN 26-63 — P-ETSS v1.4.5, effective 7 July 2026: https://www.weather.gov/media/notification/pdf_2026/scn26-63_petss_v1.4.5.pdf
- PNS 26-32 — P-ETSS v1.5 comment solicitation: https://www.weather.gov/media/notification/pdf_2026/pns26-32_P-ETSS_v1.5.pdf
- SCN 25-57 — P-ETSS v1.4.3, NDGD removal: https://www.weather.gov/media/notification/pdf_2025/scn25-57_PETSS_v1.4.0.pdf
- SCN 23-80 — P-ETSS v1.3 and ETSS v2.5, package merge: https://www.weather.gov/media/notification/pdf_2023_24/scn23-80_p-etss1.3.0_etss2.5.0.pdf
- SCN 20-106 — P-ETSS v1.1, expansion to 52 members: https://www.weather.gov/media/notification/pdf2/scn20-106p-etssaac.pdf
- SCN 17-63 — first P-ETSS, GRIB2 process ID assignments: https://www.weather.gov/media/notification/pdfs/scn17-63etss_petss_aac.pdf
- P-ETSS v1.0 WMO header scheme (approved 24 May 2017): linked from the MDL public notices page
