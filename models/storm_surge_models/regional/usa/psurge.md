# P-Surge (Probabilistic tropical cyclone storm Surge model)

## What this model is
**P-Surge** is NOAA's operational ensemble storm surge model for **tropical cyclones**. It is the tropical counterpart to [P-ETSS](petss.md), and unlike every other model in the NOAA surge family it is **event-driven**: it runs only while the National Hurricane Center has an active tropical cyclone, and produces nothing at all otherwise.

Its ensemble is built differently from P-ETSS. Rather than taking members from an atmospheric ensemble, P-Surge **samples the error space around NHC's official forecast**, using NHC's 5-year average along-track, cross-track, intensity and size errors. Each sample is fed to a parametric wind model, which supplies the wind and pressure forcing for a **SLOSH** run. The resulting members are combined according to their likelihood into probabilistic guidance.

Two kinds of product come out of that, matching the P-ETSS pattern:
- **Threshold probabilities** — the probability of storm tide exceeding a fixed height, published as `gt1` … `gt20` (feet)
- **Exceedance heights** — the height exceeded by a given fraction of the sampled storms, published as `e10` … `e90`

Results are merged onto the NDFD CONUS grid at **625 m**, with 2.5 km products supplied separately to NHC. Separate domains cover Puerto Rico and the U.S. Virgin Islands at 625 m and, since 2025, the Hawaiian Islands at 312.5 m.

> **P-Surge covers tropical cyclones; [ETSS](etss.md) and [P-ETSS](petss.md) cover extra-tropical systems.** The ETSS-family station bulletins carry the literal warning `NOT VALID FOR TROPICAL STORMS`. P-ETSS does extend to *weaker* tropical systems, but for a named tropical cyclone P-Surge is the intended product.

The current release is **v3.2.1**, patched 22 July 2026.

---

## Who runs it
- **Organization:** NOAA / National Weather Service / Meteorological Development Laboratory (MDL), now part of the Office of Modeling Development (OMD). Products are attributed in-band to `DOC/NOAA/NWS/OSTI/MDL`.
- **Forecast input:** National Hurricane Center official advisories
- **Country / region:** United States — Atlantic and Gulf of America coastlines, Puerto Rico and U.S. Virgin Islands, Hawaii
- **First operational:** April 2007 (v1.0)

---

## When this model runs

This section exists because P-Surge's availability pattern is unlike anything else in the catalog.

- **Trigger:** an active tropical cyclone within range of a supported domain. No storm means no data.
- **Cycles:** 4× daily (00, 06, 12, 18 UTC) **while a storm is active**, synchronised to NHC advisory issuance. Live-verified on 21 July 2026, which carried all four cycles; 22 July 2026 carried three (00, 06, 12) as the event wound down.
- **Advisory linkage:** each cycle corresponds to one NHC advisory. Live-verified on 2026-07-22: the 00Z, 06Z and 12Z cycles carry `ADVNUM` 11, 12 and 13 respectively.
- **Storm identity is in every filename.** Products are tagged with the ATCF storm identifier — `al022026` in the verified sample, which the accompanying metadata identifies as **Tropical Storm Bertha**.
- **Multiple simultaneous storms** have been supported since v3.0 (May 2023); each gets its own set of files distinguished by storm ID within the same date directory.

> **Live-verified caution — most date directories on NOMADS are empty.** On 24 July 2026 the production directory listed six date directories, but only three (`psurge.20260720`, `20260721`, `20260722`) contained anything. `psurge.20260526`, `20260601` and `20260611` were **empty husks** left behind after their data expired. A directory's existence is not evidence that data is available; check for contents.

---

## Basic details
- **Model type:** Ensemble (probabilistic) tropical cyclone storm surge model
- **Core hydrodynamic model:** SLOSH, driven by a parametric tropical wind model
- **Dimensionality:** 2D depth-averaged
- **Forecast length:** **102 hours** — encoded directly in the filename as `h102`. Live-verified: the hourly products carry 102 records with step ranges `1-2` through `102-103`. Extended from 78 h in v2.6 (April 2017); the 0–80 h CONUS products were discontinued in favour of 0–102 h in v3.2 (June 2026).
- **Temporal output resolution:** Hourly and 6-hourly, depending on product variant
- **Initial water level condition:** Published per cycle as `<stormid>_<cycle>_wlevel.dat`, giving initial water levels by named water body rather than by basin. Live-verified values for 2026-07-22 00Z: Atlantic ocean and lake 0.5, Gulf ocean and lake 0.6, Hawaii ocean and lake 0.5, plus separate Lake Okeechobee entries (canal 10.0, lake 15.0, ocean 0.0) reflecting that lake's regulated stage.

---

## Ensemble configuration
- **Ensemble construction:** Sampling of the NHC official forecast error space — along-track, cross-track, intensity and size errors, using NHC's 5-year average error statistics
- **Ensemble size:** Variable, determined by the sampling scheme rather than fixed
- **Member weighting:** By likelihood, not equal weighting (a difference from [P-ETSS](petss.md), which weights its 52 members equally)
- **Wind forcing:** Parametric tropical wind model, not NWP output. Wind ensemble initialisation has evolved substantially — based on pressure through v2.7, on initial size RMW in v2.8, and on NHC's RMW forecast from v2.9 onward.
- **Products:** exceedance heights and threshold probabilities. No ensemble mean, max or min products, unlike P-ETSS.
- **Individual members distributed?** No.

---

## Understanding the filenames

```
psurge.t<YYYYMMDDHH>z.<stormid>_<STAT>_<ACCUM>_<DATUM>.h102.<grid>.grib2
```

Note the cycle token carries a full 10-digit timestamp **and** a trailing `z` — `t2026072200z`, not `t00z`. The station SHEF products use the shorter `t00z` form instead, so the two conventions coexist within one directory.

**`<STAT>` — 29 values**

| Group | Values | Meaning |
|---|---|---|
| Exceedance | `e10` `e20` `e30` `e40` `e50` `e60` `e70` `e80` `e90` | Height exceeded by that % of sampled storms |
| Threshold | `gt1` `gt2` … `gt20` | Probability of exceeding that many **feet** |

P-Surge carries a denser threshold set than [P-ETSS](petss.md) — every foot from 1 to 20, against P-ETSS's 13 levels — and a complete exceedance ladder including the 60/70/80 levels P-ETSS omits.

**`<ACCUM>_<DATUM>` — coverage is asymmetric.** Live-verified across all 29 statistics for the 2026-07-22 00Z cycle:

| Statistic group | `cum_agl` | `cum_dat` | `inc_agl` | `inc_dat` |
|---|---|---|---|---|
| `e10`–`e50`, `e90` | ✅ | ✅ | ✅ | ✅ |
| `e60`, `e70`, `e80` | ❌ | ✅ | ❌ | ❌ |
| `gt1` | ✅ | ❌ | ✅ | ❌ |
| `gt2`–`gt20` | ✅ | ✅ | ✅ | ❌ |

That gives **86 GRIB2 files per cycle**. The 60/70/80 exceedance levels exist only as whole-event cumulative above-datum fields, and no threshold product has an `inc_dat` variant.

- **`inc` vs `cum`** — incremental (that window alone) vs cumulative (from forecast start)
- **`dat` vs `agl`** — water level above the vertical datum vs inundation depth above ground level

**Record structure differs sharply by variant** (live-verified):

| Variant | Records | Step ranges |
|---|---|---|
| `inc_dat` | 102 | `1-2`, `2-3` … `102-103` |
| `inc_agl` | 17 | `6-12`, `12-18` … `102-108` |
| `cum_agl` | 17 | `6-12`, `12-24`, `18-36` … `102-204` |
| `cum_dat` | **1** | `102-204` |

---

## Grid and bathymetry
- **Grid type:** SLOSH computational basins — "as many computational grids as necessary to cover the contiguous US's coastlines along the Atlantic and Gulf of America" — merged onto regular NDFD distribution grids
- **Distribution grid (live-verified, 2026-07-22 00Z):**

| Grid | Projection | Ni × Nj | Spacing |
|---|---|---|---|
| `conus_625m` | Lambert conformal | 8577 × 5505 | 634.926 m |

  Lambert parameters LaD 25.0°N, LoV 265.0°E, first grid point 20.192°N, 238.446°E — **bit-identical to the `con625m` grid used by [ETSS](etss.md) and [P-ETSS](petss.md)**.

- **Other domains (documented, not verified here):** Puerto Rico / U.S. Virgin Islands on the NDFD Puerto Rico grid at 625 m; Hawaii on the NDFD Hawaii grid at 312.5 m; 2.5 km CONUS products supplied to NHC. Only `conus_625m` appeared in the verified event, because the storm was an Atlantic system approaching the U.S. mainland — **which domains appear depends on where the storm is**.
- **Bottom friction:** Spatially varying, introduced in v3.0 (May 2023)
- **Wetting and drying:** Yes — overland inundation is computed, which is what the `agl` products represent

---

## Vertical datum and reference level
- **Vertical datum:** **TBD for the `dat` products.** SLOSH-family models reference to NGVD29 or NAVD88 depending on basin. Live-verified: the GRIB2 messages encode `typeOfLevel = surface`, `level = 0` and carry **no datum identifier**.
- **`agl` products** encode `typeOfLevel = heightAboveGround`, `level = 0`, and represent **inundation depth above ground**. The "> 0 feet AGL" products were discontinued in v3.1.2 (May 2025).
- **SHEF station output is MLLW, in feet** (live-verified from the bulletin headers).
- **Thresholds are in feet, encoded as metres.** Live-verified: `gt5` carries `scaledValueOfUpperLimit = 152` with `scaleFactorOfUpperLimit = 2`, i.e. 1.52 m against a true 5 ft of 1.5240 m. Note the **2-decimal encoding is coarser than [P-ETSS](petss.md)'s 5 decimals**, so P-Surge thresholds are truncated by up to ~4 mm relative to the exact foot value.
- **Probability values are in percent** (0–100).

---

## Tide handling
- **Are tides included?** Yes — SLOSH computes inundation from storm surge **and** tide, and has done since the surge-plus-tide products became operational in 2014.
- **Separation of components:** No. There is no surge-only or tide-only gridded product; everything published is combined storm tide.
- **Tide–surge interaction:** Computed within SLOSH rather than added afterward, unlike [ETSS](etss.md).

---

## Forcing and coupling
- **Meteorological forcing:** Parametric tropical cyclone wind model driven by sampled NHC forecast parameters — **not** NWP fields. This is the fundamental difference from the ETSS family, which takes GFS or GEFS/GEPS winds.
- **Wave contribution:** **Yes, but only outside CONUS.** SLOSH's wave-setup computation is activated for the Puerto Rico / USVI and Hawaii domains, where bathymetry drops off rapidly and wave setup is a larger share of coastal inundation. The CONUS runs do **not** include wave setup. Wave energy transfers were re-enabled in v3.0.3 and a wave repeatability bug fixed in v3.0.4 (2023).
- **River discharge:** Not modelled
- **Ice forcing:** Not applicable

---

## Data assimilation
- **Assimilates water level observations:** No.
- **Observation use:** Initial water levels are set per water body from `wlevel.dat` rather than from a station anomaly calculation. There is no station bias-correction stage equivalent to the ETSS/P-ETSS post-processing — a significant difference, since it means P-Surge products carry no statistical adjustment for sea level rise, model bias or river effects.

---

## What it provides

**Gridded, GRIB2** — 86 files per cycle following the grammar above, storm tide only.

**Shapefiles** — a `shpfiles/` subdirectory carrying the same products as zipped shapefiles, named `psurge_t<YYYYMMDDHH>z_<stormid>_<STAT>_<ACCUM>_<DATUM>.zip` (underscores throughout, no dots). Live-verified: **80 per cycle**, covering `cum_agl`, `cum_dat` and `inc_agl` but **not** `inc_dat` — the six hourly above-datum exceedance products have no shapefile equivalent.

**SHEF** — `psurge.t<CC>z.<stormid>.<STAT>.conus.shef` for **`e10`, `e50` and `e90` only**, three files per cycle. Storm surge plus tide, referenced to **MLLW in feet**, hourly (`DIH01`), physical element `HMIFZD1`. Experimental, introduced in v3.2 (June 2026). Note the first line is a literal placeholder: `# First line. May be replaced with WMO header info.` Missing values appear as `M`.

**Metadata and control files**
- `psurge_<cycle>_<stormid>.meta` — storm ID, storm name, synoptic time, NHC advisory number and time, development type, run type. Live-verified example:
  ```
  STRMID, AL022026
  NAME, BERTHA
  FILE, psurge_2026072200_al022026.grib2
  SYNOPTICTIME, 2026072200
  ADVNUM, 11
  ADVTIME, 2026072203
  TIMEZONE, UTC
  DEVELTYPE, TS
  RUNTYPE, OFCL
  DATAFLOW, EXTERNAL
  ```
  Note the `FILE` field names a single aggregate GRIB2 that is **not** present on NOMADS under that name — it refers to an internal artefact, not a retrievable product.
- `<cycle>.go` — a one-line trigger file: storm ID, synoptic time, domain (e.g. `al022026 2026072200 CONUS`)
- `<stormid>_<cycle>_wlevel.dat` — initial water levels by water body

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and FTP)
- **License:** **U.S. Government work — public domain.** NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded (GRIB2 and shapefile) plus station SHEF
- **Data formats:** **GRIB2**, **zipped ESRI shapefile**, **SHEF**, plain text

### Official download locations

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/psurge/prod/psurge.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/psurge/prod/psurge.YYYYMMDD/
```

P-Surge has its own directory, unlike [ETSS](etss.md) and [P-ETSS](petss.md) which share `petss/prod`.

- **Retention:** approximately **three days of populated event directories**, with empty date directories persisting considerably longer (see *When this model runs*).
- **Volume:** roughly **150 MB per cycle** for a single storm, dominated by the six `inc_dat` exceedance products at 15–18 MB each. Modest by comparison with P-ETSS, because only one grid is published and the valid area is confined to the storm's footprint.

> **No AWS mirror and no long-term archive.** P-Surge is not part of NOAA Open Data Dissemination. Combined with the short retention and the event-driven schedule, this means **historical storm surge guidance for past hurricanes is not publicly retrievable** — if you want a storm's P-Surge output, you must capture it while the storm is running.

> **The NDGD route is closed for the ETSS family** (SCN 25-57, September 2025). P-Surge's own NDGD dataflow was separately rerouted in SCN 18-36 (October 2018); its current NDGD status is TBD. The `tgftp.nws.noaa.gov/…/DC.ndgd/GT.psurge/` tree still exists but was last modified in 2020 and is empty.

---

## Notes

- **P-Surge GRIB2 files are plain GRIB2 — no Fortran wrapper.** This is worth stating explicitly because [ETSS](etss.md) and [P-ETSS](petss.md), which come from the same laboratory and share the same distribution grid, wrap every message in Fortran unformatted sequential record markers. P-Surge does not: files begin with the `GRIB` magic and decode directly with eccodes, wgrib2 or cfgrib. Do not apply the ETSS-family stripping step here.

- **GRIB2 encoding** (live-verified, 2026-07-22 00Z):

  | Product | Parameter | `shortName` | PDT | Distinguishing key |
  |---|---|---|---|---|
  | Threshold probability (`gt*`) | 10/3/192 | `surge` | **9** | `probabilityType = 1`, `scaledValueOfUpperLimit` |
  | Exceedance height (`e*`) | 10/3/192 | `surge` | **10** | `percentileValue` |

  All carry `centre = kwbc`, `subCentre = 14` (MDL), `generatingProcessIdentifier = 12`, `bitmapPresent = 0`, `missingValue = 9999`. Both product types use the same parameter — **PDT is the only thing separating a probability from a height**, and confusing them means confusing a percentage with a metre value.

- **`generatingProcessIdentifier = 12` is confirmed live**, matching the assignment made in SCN 17-63 so that P-Surge (**12**), [ETSS](etss.md) (**16**) and [P-ETSS](petss.md) (**18**) could be distinguished. Unlike the STOFS family, this key is reliable across the whole NOAA SLOSH family.

- **The exceedance labels are inverted relative to the GRIB encoding.** `e10` — the height exceeded by 10% of storms — encodes as `percentileValue = 90`. Identical to the P-ETSS convention. Invert when selecting on the percentile key.

> **Live-verified defect — the cumulative products carry malformed time intervals.** The `cum_agl` series encodes step ranges as `6-12`, `12-24`, `18-36`, `24-48` … `102-204`, and `cum_dat` as a single `102-204`. The pattern is `[T, 2T]`: the encoder appears to be setting `lengthOfTimeRange` equal to `forecastTime` instead of writing the correct cumulative interval `[0, T]`. The incremental products are encoded correctly (`inc_agl` gives `6-12`, `12-18`, … `102-108`; `inc_dat` gives `1-2` … `102-103`).
>
> The practical consequences: cumulative intervals appear to extend up to 204 hours for a 102-hour forecast, the intervals overlap, and any tool that derives a valid time from the interval end will place cumulative fields far beyond the forecast horizon. **Use the interval start as the forecast hour for `cum_*` products and ignore the end**, or key on the filename. Worth re-checking after the next upgrade.

- **`cum_dat` is a single field, not a series.** Where `cum_agl` carries 17 six-hourly cumulative fields, `cum_dat` carries exactly one message representing the whole-event cumulative. The two share a naming pattern but not a structure, so code that iterates messages must not assume a fixed count.

- **Valid coverage is extremely sparse, by design.** On the verified 2026-07-22 00Z cycle the `e10` products carried valid data at roughly 0.8% of the 47,216,385 grid points, and `gt5_cum_agl` at just 7 points. The grid is CONUS-wide; the storm's footprint is not. A file that is almost entirely 9999 is normal, not a failure — but it means the 9999 sentinel must be filtered before any statistics are computed.

- **No bitmap; land, sea and out-of-footprint points are the literal value 9999.0.** Consistent with the rest of the NOAA surge family.

- **Two filename conventions coexist in one directory.** GRIB2 files use `psurge.t2026072200z.…` (full timestamp with trailing `z`, dot-separated with underscores inside the product token); SHEF files use `psurge.t00z.…` (short cycle, dot-separated); shapefiles use `psurge_t2026072200z_…` (underscores throughout). Pattern-matching across the whole directory needs all three forms.

- **Relationship to other systems:**
  - Extra-tropical counterparts: [ETSS](etss.md) (deterministic) and [P-ETSS](petss.md) (ensemble). Same laboratory, same SLOSH core, same 625 m CONUS distribution grid — but different forcing, different scheduling, and P-Surge alone lacks a station bias-correction stage.
  - P-ETSS covers weaker tropical systems as well as extra-tropical ones, so the two overlap for marginal cases; P-Surge is the product of record for named tropical cyclones.
  - Overlapping deterministic system: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), which runs continuously and covers tropical storms implicitly through its GFS forcing, without tropical-specific wind modelling.
  - Viewers (not data sources): https://slosh.nws.noaa.gov/psurge/ and, for Puerto Rico, https://slosh.nws.noaa.gov/psurge/index.php?R=PR

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| v3.2.1 | 22 Jul 2026 | Patch. The Penv statistics post-processing stage exited a "remove mask" routine prematurely on water levels below −20.5 ft or above 36.0 ft, corrupting results; the valid range was expanded to [−20.5, 45.0] ft. |
| v3.2 | 2 Jun 2026 | SCN 26-41. Replaced the Puerto Rico computational grid; improved landfall handling in interpolation and a better method for stopping a storm run; updated error statistics; **experimental SHEF output**; discontinued 0–80 h CONUS products in favour of 0–102 h. |
| v3.1.2 | 20 May 2025 | SCN 25-31. **Hawaii products created**; wave calculations made more efficient; "> 0 feet AGL" products discontinued. |
| v3.0.8 | 11 Mar 2025 | Gulf naming conventions updated. |
| v3.0.7 | 22 Jul 2024 | Cumulative exceedance calculation corrected to match the cumulative probability method; coarse PR/VI grid to NHC discontinued. |
| v3.0.4 | 8 Aug 2023 | Wave repeatability bug corrected after wave energy transfers were re-enabled in v3.0.3. |
| v3.0 | 2 May 2023 | SCN 23-46. **Puerto Rico and U.S. Virgin Islands support with surge, tide and waves**; spatially varying bottom friction; ability to handle two simultaneous storms. |
| v2.10 | 28 Jun 2022 | Transitioned to WCOSS2. |
| v2.9 | 11 May 2021 | SCN 21-41. Wind ensemble initialised from NHC's RMW forecast rather than a constant pressure or RMW. |
| v2.8 | 29 Sep 2020 | SCN 20-81. Wind ensemble initialised from initial size RMW estimates; error groupings changed to 0–50, 50–95 and > 95 kt. |
| v2.7 | 7 May 2018 | SCN 18-36. 90% exceedance added; "> 21–25 feet" datum products dropped. |
| v2.6 | 18 Apr 2017 | SCN 17-26. **Forecast extended to 102 h from 78 h**; runs allowed for sub-tropical storms, depressions and potential/post-tropical cyclones; high-resolution South Florida basin used. |
| v2.5 | 26 May 2015 | Products on coarser 2.5 km grids; exceedance above datum at 1-hour steps. |
| v2.0 | 8 Jul 2014 | TIN 14-11/14-12. Surge **plus tide** products become operational, both above datum and as inundation heights. |
| v1.0 | 18 Apr 2007 | Initial implementation. |

---

## Official documentation
- MDL P-Surge project page: https://vlab.noaa.gov/web/mdl/psurge
- MDL storm surge public notices and full change log: https://vlab.noaa.gov/web/mdl/technical-notices-storm-surge
- MDL storm surge FAQ (datum discussion): https://vlab.noaa.gov/web/mdl/faq-storm-surge
- MDL SLOSH page: https://vlab.noaa.gov/web/mdl/slosh
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/psurge/prod/
- SCN 26-41 (Updated) — P-Surge v3.2, effective 2 June 2026: https://www.weather.gov/media/notification/pdf_2026/scn26-41_Updated_PSurge_v3_2_0.aaa.pdf
- PNS 26-06 — P-Surge v3.2 comment solicitation: https://www.weather.gov/media/notification/pdf_2026/pns26-06_P-Surge_v3.2.pdf
- SCN 25-31 (Updated) — P-Surge v3.1.2, Hawaii: https://www.weather.gov/media/notification/pdf_2025/scn25-31_update_aaa_P-Surge-v3.1.2.pdf
- SCN 23-46 (Updated) — P-Surge v3.0, Puerto Rico and USVI: https://www.weather.gov/media/notification/pdf_2023_24/scn23-46_p-surge_v3.0_aaa.pdf
- NOAA press release on the v3.0 upgrade: https://www.noaa.gov/news-release/noaa-upgrades-model-to-improve-storm-surge-forecasting
- SCN 17-63 — GRIB2 process ID assignments for P-Surge, ETSS and P-ETSS: https://www.weather.gov/media/notification/pdfs/scn17-63etss_petss_aac.pdf
- P-Surge v3.2 WMO header scheme and header list (approved 24 October 2025): linked from the MDL public notices page
- Jelesnianski, C. P., J. Chen and W. A. Shaffer, 1992: SLOSH: Sea, Lake and Overland Surges from Hurricanes. *NOAA Technical Report NWS 48*, 71 pp.
