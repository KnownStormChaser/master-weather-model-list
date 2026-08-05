# 557th WW GEPS (Global Ensemble Prediction Suite)

## What this model is
The 557th Weather Wing Global Ensemble Prediction Suite (GEPS) is a statistical multi-model ensemble forecast system produced operationally by the U.S. Air Force's 557th Weather Wing.

Unlike most ensembles, which derive members from a single center's own perturbed runs, 557th WW GEPS ingests ensemble members from **three different operational numerical modeling centers** — NOAA/NCEP, the U.S. Navy's Fleet Numerical Meteorology and Oceanography Center (FNMOC), and Environment and Climate Change Canada's Canadian Meteorological Centre (CMC) — and combines them into a single unified statistical product.

This system is the operational distribution form of the multi-center ensemble known historically as the **National Unified Operational Prediction Capability (NUOPC)** ensemble. It is conceptually related to [NAEFS](./naefs.md), which combines only the NCEP and CMC ensembles; 557th WW GEPS extends that approach by adding the Navy's ensemble as a third contributor.

> **What is distributed is a single self-contained GRIB2 file per step** — no member data, no per-center breakdown, and no supplementary directories. Everything below describes the contents of that one file family.

**Verification basis:** all structural, grid, step, and encoding claims below were verified live on **2026-08-01** against `557ww.20260731` (00Z and 12Z cycles), with GRIB2 headers decoded using ecCodes 2.48.0 and inventories cross-read from the distributed `.idx` sidecars. Claims about member composition come from NOAA's product description and are **not independently verifiable from the data** — see *Member composition is unverifiable*.

---

## Who runs it
- **Organization:** U.S. Air Force 557th Weather Wing
- **Country / region:** United States (product); multinational (member inputs)
- **Headquarters:** Offutt Air Force Base, Nebraska
- **Distribution:** NOAA / NCEP NOMADS

The GRIB2 originating centre is encoded as **57 — "U.S. Air Force – Global Weather Center"**, sub-centre 4, `generatingProcessIdentifier = 99`. This is the only product family in the catalog carrying centre 57, and it has decoding consequences (see *Decoding hazard*).

---

## What area it covers
- **Coverage:** Global
- **Grid (verified):** `regular_ll`, **720 × 361** (259,920 points), **0.5° × 0.5°**
- **Grid origin:** first point 90°N / 0°E, last point −90° / 359.5°, `scanningMode = 0` (west→east, north→south)
- **Earth shape:** `shapeOfTheEarth = 0` — spherical, radius 6,367,470 m. Note this differs from the NCEP-produced ensembles in this repository ([NAEFS](./naefs.md), [GEFS](./gefs.md)), which use `shapeOfTheEarth = 6` (6,371,229 m). Regridding or differencing against those products without accounting for the mismatch introduces a small but systematic geolocation error.

> ⚠️ **The resolution is 0.5°, not 1°.** NOAA's own NOMADS product description states that "240 hours of one-degree gridded forecast output is produced at 6 hour intervals." Every message in every file inspected is 720 × 361 at 0.5°. The 1° figure appears to be stale — possibly describing an earlier configuration (**TBD**).

Three static geolocation fields are carried as data in every file: `GEOLAT`, `GEOLON`, and surface `HGT` (orography). Latitude and longitude grids are fully redundant on a regular lat-lon grid and cost roughly 1 MB per file per field; their presence suggests the product generator was written for a non-rectilinear grid at some point.

---

## Basic details
- **Model type:** Global statistical multi-model ensemble (post-processing, not a forecast model)
- **Total members:** 63 (documented as 21 from each of three centers — not confirmable from the data, see below)
- **Contributing systems (per NOAA description):**
  - NOAA / NCEP — 21 model runs, [GEFS](./gefs.md)-based
  - U.S. Navy / FNMOC — 21 model runs, [NAVGEM ensemble](./fnmoc-ensemble.md)-based
  - Environment Canada / CMC — 21 model runs, [Canadian GEPS](../canada/geps.md)-based
- **Horizontal resolution:** 0.5° (verified)
- **Forecast length (verified):** **384 hours (16 days)**
- **Update frequency / cycles (verified):** 2× daily — 00 and 12 UTC
- **Temporal output resolution (verified):** **6-hourly throughout, 65 steps, f000–f384**, with no cadence change at any lead time
- **Publication latency (observed, 2026-07-31):** both cycles complete at **T+7h27m** — the 00Z run finished writing at 07:27 UTC, the 12Z run at 19:27 UTC
- **File sizes:** 50 MB at f000, 55–58 MB thereafter; `.idx` sidecars 14–20 KB
- **Retention on NOMADS (observed):** 2 days

> ⚠️ **The forecast length is 384 hours, not 240.** The same NOAA product description quoted above gives 240 hours (10 days). Both the 00Z and 12Z cycles of 2026-07-31 carry a complete, gap-free 6-hourly series through **f384**, verified by full directory enumeration (65 steps × 2 cycles × 2 files = 260 files, no missing steps). The published description understates the product's range by six days.

---

## Methodology

> *The template's "Data assimilation", "Initial and boundary conditions", and "Perturbations and design" sections do not apply — 557th WW GEPS runs no integration and generates no perturbations of its own. They are replaced by this section.*

The system ingests completed forecast runs from the three contributing centers and computes ensemble statistics across the pooled membership. No bias correction is documented (contrast [NAEFS](./naefs.md), which bias-corrects each contributor before pooling), and none is discoverable from the GRIB2 metadata: `typeOfGeneratingProcess` takes values **4** (ensemble forecast, 101 messages), **5** (probability forecast, 107), and **2** (forecast, 56) — never **11** (bias-corrected ensemble forecast), which is what every NAEFS message carries. Whether the contributing runs are bias-corrected upstream is **TBD**.

### Member composition is unverifiable from the product

The 63-member, 21-per-center composition comes solely from NOAA's product description. Nothing in the distributed data confirms it, and one field actively contradicts any member count:

> ⚠️ **`numberOfForecastsInEnsemble = 0` on all 101 derived-forecast messages.** The files declare an ensemble of zero members. There is no per-center metadata, no `perturbationNumber`, and no clustering information anywhere in the product. A user cannot determine from the data how many members contributed to any statistic, or from which centers.

Two further reasons to treat the 21-per-center figure with care:

- **NCEP's GEFS has run 31 members since GEFSv12.** If 557th WW GEPS still draws 21, it is using a subset — the same situation [NAEFS](./naefs.md) was in before its v7.0 upgrade of December 2023 expanded the GEFS contribution to all 31. Whether 557th WW GEPS has made an equivalent change is **TBD**; the "63 model runs" figure in the current NOMADS description suggests it has not.
- **The Canadian GEPS and the FNMOC ensemble are both genuinely 21-member systems**, so those two figures are consistent with their sources.

---

## What it provides

Each file contains a mixture of five GRIB2 product definition templates. Counts below are for the steady-state inventory (f024 onward, 264 messages):

| PDT | Meaning | Count | Notes |
|---|---|---|---|
| 4.2 | Derived forecast from all members | 101 | 52 mean (`derivedForecast = 0`), 49 std dev (`derivedForecast = 2`) |
| 4.5 | Probability forecast at a point in time | 85 | 
| 4.9 | Probability forecast over a time interval | 22 | accumulation-based thresholds |
| 4.0 | Analysis or forecast at a point in time | 54 | **carries no ensemble semantics** — see below |
| 4.8 | Statistically processed over an interval | 2 | 

`probabilityType` is **1** (above upper limit) on 92 messages and **0** (below lower limit) on 15.

### Mean and standard deviation fields

Both statistics cover the same variable set: `gh`, `t`, `u`, `v`, `r` (relative humidity), and `ws` (wind speed) on **seven pressure levels** (1000, 925, 850, 700, 500, 250, 200 hPa), plus `prmsl`, `sp`, `2t`, `2r`, `10u`, `10v`, and `10si`. This matches NOAA's description ("means/standard deviations of temperature, wind, pressure, height, and relative humidity") exactly.

**There is no dewpoint and no precipitation mean or spread.** Precipitation and snowfall appear only as threshold probabilities.

### Probability fields

Thresholds span a wide operational range, with a military-aviation emphasis unusual among the ensembles in this catalog:

- **APCP** — accumulation windows 18–24 h, 12–24 h, and 0–24 h; thresholds 0.01, 0.05, 0.1, 0.25, 0.5, 0.75, 1, 2, 5
- **ASNOW** (snowfall) — same windows; thresholds 0, 0.1, 1, 2, 4, 6, 12
- **CEIL** (ceiling) — probability below 500 ft, 1000 ft, and 3000 ft, keyed to level surfaces at 152 m, 305 m, and 914 m above ground
- **GUST** — above 15, 25, 35, 50 (kt)
- **ICI** (icing intensity) — above 58, 85, 92 at 250, 500, and 700 hPa
- **HAIL** — above 0.75, 2
- **HGT** — 500 hPa below 5400 gpm and above 5800 gpm; 700 hPa below 3000 gpm
- **TMP** — below 273.15 K, above 305.37 K, and below 266.483 / 255.372 / 244.261 K
- **PRES** — above 102500 Pa, below 100000 Pa
- Visibility, cloud cover, and several local-table parameters (see *Decoding hazard*)

### ⚠️ Fifty-four messages carry no ensemble metadata at all

The 54 **PDT 4.0** messages duplicate the same variable and level combinations as the ensemble-mean fields — `gh`, `t`, `u`, `v`, `r` on the seven pressure levels, plus `2t`, `2r`, `10u`, `10v`, `prmsl`, `ceil`, orography and the two geolocation grids. In the `.idx` sidecar these lines have an **empty statistic field**, so `HGT:500 mb:24 hour fcst:` appears alongside `HGT:500 mb:24 hour fcst:mean all members` and `…:std dev (cluster mean)`.

PDT 4.0 means "analysis or forecast at a point in time" — a *deterministic* product definition. What these fields actually contain is undocumented: they could be the ensemble mean re-encoded without ensemble semantics, a control member, or a deterministic run passed through. **TBD.** Any pipeline keying on variable and level alone will silently pick up whichever of the three copies it encounters first.

### ⚠️ Spread is encoded as a cluster statistic

The standard deviation fields use `derivedForecast = 2` — GRIB2 Code Table 4.7 value 2, *"standard deviation with respect to cluster mean"*. There is no clustering in this product, and no cluster metadata is present. The semantically correct code for an ensemble spread over all members is **4** ("spread of all members"), which is what NAEFS and GEFS use. wgrib2 renders the field honestly as `std dev (cluster mean)` in the `.idx`, which is likely to mislead users into looking for a clustering scheme that does not exist.

### Inventory ramps with lead time

Message counts are **not constant across steps**: 223 at f000, 256 at f006, 260 at f012, and 264 from f024 onward. The growth is accumulation-driven — the 12–24 h and 0–24 h precipitation and snowfall probability windows cannot exist until enough lead time has elapsed. At f000 all steps are `anl` and the PDT mix is 4.2 (99), 4.5 (70), 4.0 (54), with no interval products at all. Any code assuming a fixed message count or a fixed message ordering across steps will break at short leads.

---

## Decoding hazard: centre 57 breaks parameter resolution

**ecCodes cannot resolve shortNames for 48 of 264 messages** (18%) in the file as distributed, including entirely standard WMO parameters. The cause is the originating centre, not the parameters themselves:

| Decode configuration | Unresolved messages |
|---|---|
| As distributed (`centre = 57`) | 48 |
| `centre` overridden to 7 (`kwbc`) | 19 |
| `tablesVersion` overridden to 32, centre untouched | 48 (no change) |

Overriding `centre` to 7 immediately resolves total precipitation (`0/1/8` → `tp`) and 28 other messages. Bumping `tablesVersion` alone changes nothing. ecCodes ships no local parameter table directory for centre 57, and the fallback path fails even for parameters defined in the WMO master tables.

**Workaround:** clone each message and set `centre = 7` before reading `shortName` or `name`.

Nineteen messages remain unresolved even after the override, all with NCEP-local or high-numbered parameters:

| Parameter (discipline/category/number) | Count | `.idx` label |
|---|---|---|
| `0/1/29` | 13 | `ASNOW` (total snowfall) |
| `0/19/221` | 2 | — |
| `0/19/241`, `0/19/242`, `0/19/235`, `0/1/227` | 1 each | — |

The distributed `.idx` sidecars label all of these correctly, because wgrib2 applies NCEP parameter tables regardless of the declared centre. **For this product the `.idx` is the more reliable parameter inventory than an ecCodes decode** — an inversion of the usual relationship, and worth knowing before trusting a cfgrib or xarray load of these files.

Files also declare `tablesVersion = 1` and `packingType = grid_simple` throughout. Simple packing (rather than complex packing with spatial differencing, used by all NCEP ensemble products) is why a 264-message 0.5° file runs to 57 MB where the comparable NAEFS 0.5° file with 49 messages is 10 MB.

---

## Naming note: GEPS disambiguation

The acronym "GEPS" is used for two unrelated ensemble systems in operational forecasting:

- **557th WW GEPS** (this entry): Global Ensemble Prediction **Suite** — a U.S. Air Force statistical multi-model ensemble
- **Canadian GEPS** ([separate entry](../canada/geps.md)): Global Ensemble Prediction **System** — Environment Canada's single-center global ensemble

These are entirely different systems. The Canadian GEPS is one of the inputs to 557th WW GEPS, but the two should not be confused.

---

## Relationship to other models

Built from three single-center ensembles, each documented separately:

- **NCEP contribution:** [GEFS](./gefs.md)
- **FNMOC contribution:** the [FNMOC Ensemble](./fnmoc-ensemble.md), built around [NAVGEM](../../../nwp_models/global/usa/navgem.md)
- **CMC contribution:** [Canada's GEPS](../canada/geps.md)

- **[NAEFS](./naefs.md):** the two-center bias-corrected sibling. The two systems differ in more than membership: NAEFS bias-corrects before pooling and publishes percentiles, mode, anomalies and an EFI at 0.5° twice daily to 384 h; 557th WW GEPS publishes mean, spread, and a large threshold-probability suite at 0.5° twice daily to 384 h, without documented bias correction. The probability suite is the substantive difference — NAEFS distributes no exceedance probabilities at all.
- **NUOPC ensemble framework:** the broader multi-center framework this product implements. NAEFS predates it and operates independently.

The multi-model, multi-center design samples uncertainty more broadly than any single-model ensemble by combining members with different physics, assimilation, and error characteristics.

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:** Public domain (U.S. government work; CC0-equivalent). **No licence statement is published for the `557ww/` tree specifically** — the product is a Department of Defense output distributed through NOAA infrastructure, and the NOAA Open Data Dissemination terms that cover the NCEP-produced trees are not explicitly extended to it in any located document (**TBD**). In practice it is served without restriction alongside NCEP products.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, with wgrib2-style `.idx` sidecars for byte-range subsetting

### Distribution channel

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/557ww/prod/
  557ww.YYYYMMDD/
    GLOBAL.grib2.YYYYMMDDCC.FFFF        (CC = 00 or 12; FFFF = 0000…0384 by 0006)
    GLOBAL.grib2.YYYYMMDDCC.FFFF.idx
```

Both cycles of a day live in the same dated directory, distinguished only by the `CC` field embedded in the filename — not by a cycle subdirectory, unlike every other NOMADS ensemble tree in this catalog. Retention observed 2026-08-01: two days (`557ww.20260730`, `557ww.20260731`).

No cloud mirror is documented for this product; if none exists, the two-day NOMADS window is the entire public archive. **Not independently verified** — worth checking the NODD buckets before relying on it.

### Access notes

- The `.idx` sidecars are the practical entry point. At ~57 MB per file and 130 files per day, byte-range subsetting via the index is close to mandatory for any operational use, and the `.idx` also carries better parameter labels than an ecCodes decode (see *Decoding hazard*).
- NOMADS directory listings on this host can return HTTP 502 while file requests succeed; appending a sort query string (`?C=N&O=A`) reliably returns the listing. Absent directories return 403, missing files 404.
- NOMADS rate-limits concurrent requests and the failure mode is a spurious 404. Serialize enumerations or they will record archive gaps that do not exist.

---

## Notes

- **557th WW GEPS produces derived statistics only** — no individual member output is distributed anywhere in the tree. Users needing member data must go to the contributing single-center ensembles directly ([GEFS](./gefs.md), [FNMOC Ensemble](./fnmoc-ensemble.md), [Canadian GEPS](../canada/geps.md)).

- **The published product description is wrong on two of its four quantitative claims.** Forecast length (240 h vs. an actual 384 h) and resolution (1° vs. an actual 0.5°) are both understated; cadence (6-hourly) and cycles (00Z/12Z) are correct. This is the most divergent operator description encountered in the catalog so far, and the errors run in the direction of understating the product.

- **The 557th Weather Wing** is the lead military meteorology center of the U.S. Air Force, providing environmental situational awareness worldwide to the Air Force, U.S. Army, joint warfighters, Unified Combatant Commands, the intelligence community, and the Secretary of Defense.

- **The threshold set is built for military mission planning**, not general forecasting — ceiling below 500/1000/3000 ft, icing intensity at flight levels, gust and hail thresholds. This is a materially different product from the NAEFS percentile suite despite the two systems' shared lineage, and the two are complements rather than substitutes.

- **Although operated by the U.S. military**, the product is publicly distributed through NOMADS with no access controls.

- **The multi-center NUOPC framework** has been operational since the mid-2010s and is documented as showing consistent skill improvement over any single contributing ensemble, particularly for surface variables. This claim is carried over from the previous version of this entry and has **not been independently verified**.

---

## Official documentation
- 557th Weather Wing: https://www.557weatherwing.af.mil/
- NOMADS 557th WW product directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/557ww/prod/
