# HYCOM2D-MARO (Météo-France Storm Surge Model — Mediterranean, AROME-forced)

## What this model is
HYCOM2D-MARO is the AROME-forced Mediterranean configuration of Météo-France's operational **storm surge** forecast system. It publishes the **surge residual only** — the meteorological departure of sea level from the astronomical tide — not total water level.

> **This is not the American HYCOM ocean model.** The system is built on a **barotropic (2D) version of the HYCOM code**, the same codebase as the US Navy / NOAA HYCOM ocean prediction system, but configured as a depth-averaged tide-and-surge model with no baroclinic structure, no temperature or salinity, and no 3D currents. It belongs under `storm_surge_models/`, not `ocean_models/`.

Developed by **SHOM** with Météo-France under the **HOMONIM** project, commissioned by the Direction Générale pour la Prévention des Risques under the interministerial *Submersions Rapides* plan, and operational at Météo-France since **14 January 2014**.

MARO runs on a grid **identical to [HYCOM2D-MARP](./hycom2d-marp-france.md)'s** and differs from it in one input: AROME winds (~1.3 km) rather than ARPEGE (~10 km). The trade-off is range — AROME's shorter horizon caps this configuration at **51 h** against MARP's 102 h. The pair is the surge analogue of the [WW3-MARO](../../../wave_models/regional/france/ww3-maro-france.md) / [WW3-MARP](../../../wave_models/regional/france/ww3-marp-france.md) wave pair, on the same domain and with the same forcing contrast.

`MARO` decomposes as **M**editerranean + **AR**OME.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **GRIB2 originating centre (verified):** `lfpw` — French Weather Service, Toulouse (centre 85), subCentre 0, **`generatingProcessIdentifier = 161`**

> **The process identifier is the only in-file discriminator between the four HYCOM2D configurations.** Verified values: **MARO 161**, [MARP](./hycom2d-marp-france.md) **134**, [WARO](./hycom2d-waro-france.md) **159**, [WARP](./hycom2d-warp-france.md) **131**. The two Mediterranean configurations share an identical grid and parameter set, so in an archive with filenames lost this key is the only way to tell MARO from MARP. Note the numbering does not group by domain or by forcing in any obvious pattern.

---

## What area it covers
- **Coverage:** Western and central Mediterranean — the French Mediterranean seaboard, the Gulf of Lion, Corsica, Sardinia, the Balearics, the Tyrrhenian Sea and the Strait of Sicily approaches
- **Domain (verified):** 45°N–35°N, 6°W–17°E — the `MEDIT0042` grid, identical to MARP's
- **Inundation coverage:** **None.** The domain stops at the coastline. 64,553 of 133,273 grid points are valid (48.44%); the rest are bitmap-masked. No overland cells, no wetting and drying, no inundation depth.

---

## Basic details
- **Model type:** Deterministic storm surge model
- **Core hydrodynamic model:** HYCOM, barotropic configuration
- **Dimensionality:** **2D depth-averaged (barotropic)**
- **Forecast length (verified):** **51 h at every cycle**
- **Update frequency / cycles (verified):** **4× daily** (00, 06, 12, 18 UTC)
- **Temporal output resolution:** hourly instantaneous; 3-hourly and 24-hourly statistical fields

> **The model descriptif describes a cadence that no longer exists.** `descriptif-modele-hycom2d.pdf` states the AROME-forced runs are launched **5× daily** at 00, 03, 06, 12 and 18 UTC, reaching 42 h, 39 h, 36 h, 42 h and 36 h respectively. Live enumeration on 2026-08-09 found **4 cycles, each with 52 files reaching 51 h**, and **no 03Z run exists** in the object store at any sampled date. `description-paquets-modele-hycom2d.pdf` gives 0–51 h at four cycles and is correct.
>
> The same document is also wrong about the ARPEGE configurations, where its 102/72/114/60 quartet is recurring boilerplate that appears in the MFWAM documentation too. Treat `descriptif-modele-hycom2d.pdf` as reliable for model physics and validation statistics, and `description-paquets-modele-hycom2d.pdf` as the authority on cadence, grids and ranges.

---

## Grid and bathymetry
- **Grid type (distributed):** regular latitude–longitude, **553 × 241**, 133,273 points
- **Grid type (native):** **curvilinear**, targeting ~1 km resolution along the French coast. The published product is a regridded regular-latlon extraction; nearshore detail from the native run is not fully recoverable.
- **Horizontal resolution (verified):** **0.041667° = exactly 1/24°** (~3.9 km zonal at 40°N, ~4.6 km meridional)
- **Bathymetry source:** SHOM, updated with the latest available survey data under HOMONIM
- **Wetting and drying:** No

> **The grid and land–sea mask are identical to [MARP](./hycom2d-marp-france.md)'s** — verified by direct array comparison of the bitmap on the 2026-08-10 12Z cycle, which matches exactly. Any regridding weights, coastline masks or valid-point indices computed for one configuration apply unchanged to the other.

> **The advertised resolution is a rounding, twice over.** Titled "Résolution 0,04°" and named `MEDIT0042`, but `descriptif-modele-hycom2d.pdf` states 1/24° and the headers confirm `iDirectionIncrementInDegrees = 0.041667` — about 4% coarser than the "0.04°" label.

> **Longitude is encoded in the 0–360 convention and wraps the prime meridian**: headers give first `354.0` (−6°) and last `17.0`. Use the decoded coordinate array, not the header pair.

---

## Vertical datum and reference level
- **Vertical datum:** **Not applicable in the usual sense.** The published field is a *residual*, not a water level referenced to a datum, so no chart datum, LAT or MSL reference is needed to interpret it — and none is stated.
- **What the field is measured relative to:** the model's own astronomical tide simulation.
- **Datum conversion offsets provided?** No, and not needed for the residual — but the product cannot be converted to total water level without an external tidal prediction.
- **Units:** metres. The parameters carry no decodable units, but the sampled range of −0.046 to +0.086 on a calm August day, together with the documentation's discussion of biases in centimetres, is consistent with metres and no other plausible unit.
- **Mean sea level trend / SLR handling:** not documented. **TBD.**

> **Documented bias — the model under-predicts the surges that matter.** Long simulations give a mean bias of **−2 cm**, worsening to **−5 cm** for the strongest surges. Across 22 storm events the peak surge was underestimated by about **10 cm** on average (8 cm on maximum total height), with a **34-minute phase lag**. Reported for the system as a whole rather than per configuration, and not corrected in the distributed fields.

---

## Tide handling
- **Are tides included?** **Modelled but not distributed.** The dataset description states the system integrates a tide simulation, and bottom friction was tuned to reproduce the tide on both domains. All three published parameters are surge residuals.
- **Tidal forcing source:** not documented. **TBD.**
- **Separation of components:** only the surge residual is published — neither the tidal level nor the combined total water level.
- **Tide–surge interaction:** modelled **nonlinearly within the run**, so the residual already accounts for tide–surge coupling. This matters less on this domain than on the Atlantic one, given the Mediterranean's small tidal range.

> **You cannot get total water level from this product alone.** An independent tidal prediction is required. SHOM publishes tidal predictions for French ports separately.

---

## Forcing and coupling
- **Meteorological forcing — wind:** [AROME](../../../nwp_models/regional/france/arome.md) (~1.3 km over France). This is the sole documented difference from [MARP](./hycom2d-marp-france.md), and the reason for the shorter range.
- **Meteorological forcing — pressure:** AROME MSLP. Not separately documented, but a barotropic surge model necessarily consumes it; the inverse-barometer contribution is co-equal with wind stress.
- **Wave contribution:** **None.** No wave setup or radiation-stress coupling is documented, and unlike the Atlantic configurations this one does not force a wave model either — [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md) takes HYCOM2D water level on the Atlantic side only.
- **River discharge / freshwater forcing:** not documented. **TBD.**
- **Ocean forcing / boundary conditions:** not documented. **TBD.**
- **Nested inside / parent for:** neither. This configuration has no documented coupling to any other model in the family.

---

## Data assimilation
- **Assimilates water level observations:** **No.** Tide gauge observations were used for calibration and validation, not real-time correction.

---

## What it provides
**Three parameters**, all on `meanSea` (level 0):

| Doc name | Description | D,C,N | PDT | Present at |
|---|---|---|---|---|
| `SURC_INS` | Instantaneous surge | 10,3,**192** | 0 | every step, 0–51 h |
| `SURC_MAX3` | Maximum surge over the preceding 3 h | 10,3,**193** | 8 | every step divisible by 3 (17 steps: 3…51) |
| `SURC_MAX24` | Maximum surge over the preceding 24 h | 10,3,**194** | 8 | steps 24 and 48 only |

**Message count per file varies** — 1, 2 or 3 depending on the step, giving **71 messages across 52 files** per cycle:

- **1 message** — step 0, and every step not divisible by 3
- **2 messages** — steps divisible by 3 but not 24 (verified at 003H, 051H)
- **3 messages** — steps 24 and 48 (verified at 024H)

File sizes track this: ~44–46 KB for one message, ~90–98 KB for two. **`SURC_MAX24` appears only twice per run** — the shortest 24-hour-maximum coverage of any configuration in the family.

> **Encoding defect 1 — the parameters are unresolvable from headers alone.** All three use discipline 10, category 3, numbers **192–194**, the WMO local-use range, while declaring `localTablesVersion = 0`. ecCodes 2.48.0 returns `unknown` for name, shortName and units. Identify by numeric triplet and PDT/time-range, not by `shortName`.

> **Encoding defect 2 — the statistical fields are labelled as averages but contain maxima.** Both use PDT 4.8 with `typeOfStatisticalProcessing = 0` (*average*; maximum is code 2), so ecCodes reports `stepType = avg`. The data are maxima. A pipeline trusting `stepType` will read peak surge as a mean and **under-report the hazard**. This defect is common to all four HYCOM2D configurations.

> ### The time window on this configuration is correct
>
> `SURC_MAX3` at step N contains the maximum over `{N−2, N−1, N}` — exactly the range the headers declare. Verified against candidate windows built from the hourly instantaneous fields of the 2026-08-10 12Z cycle, at two independent steps. Differences are in units of the message's own 9-bit quantisation step (`q`), so a fit under ~1 q is exact to the packing precision:
>
> | Window | MARO @ +24h | MARO @ +48h |
> |---|---|---|
> | max{N−2, N−1} | 6.5 q | 9.3 q |
> | **max{N−2, N−1, N}** | **1.0 q** | **0.5 q** |
> | max{N−3, N−2, N−1} | 7.2 q | 11.0 q |
> | max{N−3 … N} | 4.9 q | 6.0 q |
>
> This matters because **the two Atlantic configurations are offset**: on [WARP](./hycom2d-warp-france.md) and [WARO](./hycom2d-waro-france.md) the same parameter excludes the end step, covering only `{N−2, N−1}`, which leaves every third hour outside any maximum window.
>
> **The offset is domain-specific, not forcing-specific.** Testing all four configurations on the same cycle settles it:
>
> | Configuration | Domain | Forcing | Best-fit window | Fit |
> |---|---|---|---|---|
> | **MARO** (this entry) | MEDIT | AROME | max{N−2, N−1, N} ✓ correct | 1.0 / 0.5 q |
> | [MARP](./hycom2d-marp-france.md) | MEDIT | ARPEGE | max{N−2, N−1, N} ✓ correct | 0.6 / 0.3 q |
> | [WARO](./hycom2d-waro-france.md) | COTWEU | AROME | max{N−2, N−1} ✗ offset | 0.8 / 0.4 q |
> | [WARP](./hycom2d-warp-france.md) | COTWEU | ARPEGE | max{N−2, N−1} ✗ offset | 0.5 / 0.9 q |
>
> Both Mediterranean configurations match their metadata; both Atlantic ones do not, regardless of which atmosphere drives them. The maximum-surge fields on this domain can be used as labelled.

**No** total water level, tidal level, depth-averaged current, inundation depth, or wave setup field is distributed.

### Packing precision
`grid_jpeg` (JPEG 2000) at **9 bits per value** — coarser than the MFWAM packages' 16-bit `grid_ccsds`. Nine bits gives 512 quantisation levels spanning each message's own value range, so absolute precision is dynamic: about 0.24 mm at the sampled range here, and proportionally coarser during a large surge event. Because the Mediterranean surge range is small, this configuration has the finest effective precision of the four — roughly four times finer than the Atlantic configurations at comparable conditions.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Output geometry:** **Gridded fields only.**
- **Data formats:** GRIB2 (`grid_jpeg` packing)
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-maro-resolution-0-04deg
- **Portal dataset id:** `65bd17779ec6ae3f87a53348`

> **The 10-minute station time series described in the documentation are not published here.** `descriptif-modele-hycom2d.pdf` states the system produces both 2D gridded fields and surge time series at 10-minute resolution for a list of named sites. Only the gridded fields appear on the portal; a survey of all 10 datasets under the `pnt-vague-surcote` tag found no station product and no site-position metadata. Under the storm surge template's point-output exception this would have been in scope; it is simply not distributed through this channel. **TBD** where it goes.

### Access paths
**The portal exposes the latest cycle only** — 52 GRIB2 resources plus 2 documentation PDFs, refreshed in place. The portal file list is complete; all 52 steps are present.

**The object store carries the rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/HYCOM2D-MARO/004/SP1/<FILE>
```

The grid token is `004`, not `0042`.

### Retention — 15 days from cycle time, expiring at cycle granularity
Objects expire **15 days after their nominal cycle time**, not 15 days after they were written — a bucket-wide lifecycle policy shared with every product in the `vague-surcote` tree. Confirmed twice on 2026-08-10 by watching the boundary advance: the `2026-07-26T06Z` cycle vanished between 04:45 and 06:12 UTC, and `2026-07-26T12Z` was gone by 18:45, each exactly 15 days after its own cycle time.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Complete | Latency |
|---|---|---|
| 00Z | 03:04 | **T+3h04m** |
| 06Z | 11:24 | T+5h24m |
| 12Z | 16:13 | T+4h13m |
| 18Z | 23:23 | T+5h23m |

Each cycle publishes in about one minute. **The 00Z cycle is the earliest-arriving product in the entire `vague-surcote` tree** at T+3h04m — verified order at the 2026-08-09 00Z cycle: HYCOM2D-WARO 03:01 → **HYCOM2D-MARO 03:04** → HYCOM2D-MARP 03:33 → HYCOM2D-WARP 03:48 → MFWAM GLOB01 04:27 → WW3 packages 04:43–04:58.

Note the **latency spread is wider than MARP's** — 3h04m to 5h24m here against 3h15m to 4h27m for the ARPEGE sibling. The 06Z cycle in particular lands an hour later than MARP's, consistent with AROME availability rather than surge computation being the constraint. Schedule harvesting against the worst case, not the 00Z figure.

- **Volume:** 3.16–3.31 MiB per cycle, ~13 MiB/day — **the smallest product in the tree** by a factor of two.

---

## Notes
- **Operational status:** active, in continuous operation since 14 January 2014.

- **The MARO/MARP pair is a clean forcing comparison on the surge side.** Identical grid, identical land–sea mask, identical bathymetry, identical parameter set and cycles — differing only in whether the driving atmosphere is convection-permitting. Restrict comparisons to leads ≤ 51 h, where both have data. This mirrors the [WW3-MARO](../../../wave_models/regional/france/ww3-maro-france.md) / [WW3-MARP](../../../wave_models/regional/france/ww3-marp-france.md) wave pair on the same domain, so wind-forcing sensitivity can be examined jointly for waves and surge.

- **Prefer MARP for planning beyond two days**, MARO for the short range where AROME's resolution helps — particularly for the Gulf of Lion, where mistral and tramontane events are strongly terrain-forced and a 1.3 km atmosphere resolves the wind field far better than a 10 km one.

- **The four HYCOM2D configurations.** Two domains × two forcing models — unlike the WW3 wave family, the matrix is complete:

  | Package | Domain | Grid | Forcing | Range | Proc. ID | MAX3 window |
  |---|---|---|---|---|---|---|
  | **HYCOM2D-MARO** (this entry) | MEDIT0042 | 1/24° | AROME | 51 h | 161 | correct |
  | [HYCOM2D-MARP](./hycom2d-marp-france.md) | MEDIT0042 | 1/24° | ARPEGE | 102 h | 134 | correct |
  | [HYCOM2D-WARO](./hycom2d-waro-france.md) | COTWEU0042 | 1/24° | AROME | 51 h | 159 | offset |
  | [HYCOM2D-WARP](./hycom2d-warp-france.md) | COTWEU0042 | 1/24° | ARPEGE | 102 h | 131 | offset |

  All four verified live on 2026-08-10.

- **Portal metadata is stale**: `temporal_coverage` reads 2024-02-28 to 2024-02-29, from the dataset's February 2024 creation, and does not track the rolling window.

- **What the operator runs is larger than what is published.** The full system produces tide, total water level and site time series; the open distribution carries only the gridded surge residual on a regridded regular grid.

---

## Recent version history

No formal version history is published.

### 14 January 2014 — operational at Météo-France
The SHOM-developed barotropic HYCOM surge system entered operations, configured on the ATL and MED domains with bottom friction tuned for tidal reproduction and wind stress calibrated against one year of simulation plus 22 storm events.

> The AROME-forced cadence changed at some undated point from the 5 cycles at 42/39/36/42/36 h described in `descriptif-modele-hycom2d.pdf` to the 4 cycles at 51 h observed live and documented in the package descriptif. The change is unannounced and undated. **TBD.**

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-maro-resolution-0-04deg
- Model descriptif (FR; reliable on physics and validation, wrong on cadence): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-maro-resolution-0-04deg/20240228-171436/descriptif-modele-hycom2d.pdf
- Package descriptif (FR; the authority on grids, cycles and ranges): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-maro-resolution-0-04deg/20240228-170316/description-paquets-modele-hycom2d.pdf
- Both documents are shared across all four HYCOM2D datasets; working copies are reachable from the [MARP entry](./hycom2d-marp-france.md#official-documentation) if these URLs fail.
- HYCOM code: https://hycom.org/
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live-verified 2026-08-10 against the 2026-08-10T12:00:00Z cycle (steps 000H, 003H, 021H–024H, 045H–048H, 051H decoded with ecCodes 2.48.0, including empirical determination of the statistical windows across all four HYCOM2D configurations), with cycle enumeration, retention-boundary observation and publication timestamps sampled 2026-07-26 to 2026-08-10.*
