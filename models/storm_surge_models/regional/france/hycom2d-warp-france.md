# HYCOM2D-WARP (Météo-France Storm Surge Model — Atlantic, ARPEGE-forced)

## What this model is
HYCOM2D-WARP is the ARPEGE-forced Atlantic configuration of Météo-France's operational **storm surge** forecast system. It publishes the **surge residual only** — the meteorological departure of sea level from the astronomical tide — not total water level.

> **This is not the American HYCOM ocean model.** The system is built on a **barotropic (2D) version of the HYCOM code**, the same codebase as the US Navy / NOAA HYCOM ocean prediction system, but configured as a depth-averaged tide-and-surge model with no baroclinic structure, no temperature or salinity, and no 3D currents. It belongs under `storm_surge_models/`, not `ocean_models/`.

Developed by **SHOM** with Météo-France under the **HOMONIM** project, commissioned by the Direction Générale pour la Prévention des Risques under the interministerial *Submersions Rapides* plan, and operational at Météo-France since **14 January 2014**.

This is the configuration whose output **forces the coastal wave model** on the Atlantic side: [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md) has ingested HYCOM2D barotropic current and water height since July 2017, and the tidal wetting and drying visible in that model's node mask is this system's water level at work. Note that the water level driving WW3-WARP is *not* what is published here — the public product is the surge residual alone.

`WARP` decomposes as **W**est/Atlantic + **ARP**EGE.

> **Read the encoding caveat under *What it provides* before using the maximum-surge fields.** They are mislabelled in the headers, and on this configuration the time window is also offset by an hour relative to what the headers declare.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **GRIB2 originating centre (verified):** `lfpw` — French Weather Service, Toulouse (centre 85), subCentre 0, **`generatingProcessIdentifier = 131`**

> The process identifier differs from [HYCOM2D-MARP](./hycom2d-marp-france.md)'s (134) and is the reliable discriminator between the two ARPEGE-forced domains in archives where filenames have been lost, since the parameter sets are identical.

---

## What area it covers
- **Coverage:** Northeast Atlantic shelf — Bay of Biscay, western approaches, the English Channel, the Celtic and Irish Seas, and the southern North Sea
- **Domain (verified):** 62°N–43°N, 9°W–10°E — the `COTWEU0042` grid, a square 19° × 19° box
- **Inundation coverage:** **None.** The domain stops at the coastline. 100,163 of 208,849 grid points are valid (47.96%); the rest are bitmap-masked. No overland cells, no wetting and drying in the published product, and therefore no inundation depth.

The northern edge at 62°N reaches Shetland and the Norwegian Sea approaches; the domain covers the full UK and Irish coastlines as well as the French Atlantic and Channel seaboards, the Dutch, German Bight and Danish North Sea coasts.

---

## Basic details
- **Model type:** Deterministic storm surge model
- **Core hydrodynamic model:** HYCOM, barotropic configuration
- **Dimensionality:** **2D depth-averaged (barotropic)**
- **Forecast length (verified):** **102 h at every cycle**
- **Update frequency / cycles (verified):** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** hourly instantaneous; 3-hourly and 24-hourly statistical fields

> **The model descriptif's forecast lengths are wrong.** `descriptif-modele-hycom2d.pdf` states the ARPEGE-forced runs reach 102 h, 72 h, 114 h and 60 h at 00Z, 06Z, 12Z and 18Z. Live enumeration on 2026-08-09 found **103 files reaching 102 h at every cycle**, matching `description-paquets-modele-hycom2d.pdf` instead. The erroneous **102/72/114/60 quartet also appears in the MFWAM GLOB01 documentation**, where it is likewise contradicted — recurring boilerplate across Météo-France's `descriptif-modele-*.pdf` series.

---

## Grid and bathymetry
- **Grid type (distributed):** regular latitude–longitude, **457 × 457**, 208,849 points
- **Grid type (native):** **curvilinear**, targeting ~1 km resolution along the French coast. The published product is a regridded regular-latlon extraction; nearshore detail from the native run is not fully recoverable.
- **Horizontal resolution (verified):** **0.041667° = exactly 1/24°** (~2.3 km zonal at 55°N, ~4.6 km meridional)
- **Bathymetry source:** SHOM, updated with the latest available survey data under HOMONIM
- **Wetting and drying:** Not in the published product. The model does supply a water level that drives wetting and drying in [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md), so the capability exists upstream.

> **The advertised resolution is a rounding, twice over.** Titled "Résolution 0,04°" and named `COTWEU0042`, but `descriptif-modele-hycom2d.pdf` states 1/24° and the headers confirm `iDirectionIncrementInDegrees = 0.041667` — about 4% coarser than the "0.04°" label.

> **Longitude is encoded in the 0–360 convention and wraps the prime meridian**: headers give first `351.0` (−9°) and last `10.0`. Use the decoded coordinate array, not the header pair.

---

## Vertical datum and reference level
- **Vertical datum:** **Not applicable in the usual sense.** The published field is a *residual*, not a water level referenced to a datum, so no chart datum, LAT or MSL reference is needed to interpret it — and none is stated.
- **What the field is measured relative to:** the model's own astronomical tide simulation.
- **Datum conversion offsets provided?** No, and not needed for the residual — but the product cannot be converted to total water level without an external tidal prediction. See *Tide handling*.
- **Units:** metres. The parameters carry no decodable units, but the sampled range of −0.29 to +0.31 m, together with the documentation's discussion of biases in centimetres, is consistent with metres and no other plausible unit.
- **Mean sea level trend / SLR handling:** not documented. **TBD.**

> **Documented bias — the model under-predicts the surges that matter.** Long simulations give a mean bias of **−2 cm**, worsening to **−5 cm** for the strongest surges. Across 22 storm events the peak surge was underestimated by about **10 cm** on average (8 cm on maximum total height), with a **34-minute phase lag**. The documentation reports these for the system as a whole rather than per domain. The bias is largest exactly in the conditions the product exists to forecast, and is not corrected in the distributed fields.

---

## Tide handling
- **Are tides included?** **Modelled but not distributed.** The dataset description states the system integrates a tide simulation, and bottom friction was tuned specifically to reproduce the tide on both domains. All three published parameters are surge residuals.
- **Tidal forcing source:** not documented. **TBD.**
- **Separation of components:** only the surge residual is published — neither the tidal level nor the combined total water level.
- **Tide–surge interaction:** modelled **nonlinearly within the run**, so the residual already accounts for tide–surge coupling. This matters far more on this domain than on the Mediterranean one: the macrotidal Channel and Bay of Biscay coasts have among the largest tidal ranges in Europe, and tide–surge interaction there is strong.

> **You cannot get total water level from this product alone.** An independent tidal prediction is required, and combining an externally predicted tide with this residual reintroduces the linearity error the model avoids internally — a larger error source here than in the Mediterranean. SHOM publishes tidal predictions for French ports separately.

---

## Forcing and coupling
- **Meteorological forcing — wind:** [ARPEGE](../../../nwp_models/global/france/arpege.md) (~10 km over France). Wind stress was calibrated against one full year plus 22 storm events, separately per domain.
- **Meteorological forcing — pressure:** ARPEGE MSLP. Not separately documented, but a barotropic surge model necessarily consumes it; the inverse-barometer contribution is co-equal with wind stress.
- **Wave contribution:** **None** into this model. The coupling runs outward: this configuration's water level and barotropic current force [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md). There is no documented feedback from waves into the surge.
- **River discharge / freshwater forcing:** not documented. **TBD.**
- **Ocean forcing / boundary conditions:** not documented. **TBD.**
- **Parent for:** [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md), via water level and current since July 2017.

---

## Data assimilation
- **Assimilates water level observations:** **No.** Tide gauge observations were used for calibration and validation, not real-time correction.

---

## What it provides
**Three parameters**, all on `meanSea` (level 0):

| Doc name | Description | D,C,N | PDT | Present at |
|---|---|---|---|---|
| `SURC_INS` | Instantaneous surge | 10,3,**192** | 0 | every step, 0–102 h |
| `SURC_MAX3` | Maximum surge over a ~3 h window | 10,3,**193** | 8 | every step divisible by 3 |
| `SURC_MAX24` | Maximum surge over a ~24 h window | 10,3,**194** | 8 | steps 24, 48, 72, 96 only |

**Message count per file varies** — 1, 2 or 3 depending on the step, giving **141 messages across 103 files** per cycle:

- **1 message** — step 0, and every step not divisible by 3
- **2 messages** — steps divisible by 3 but not 24 (verified at 003H, 102H)
- **3 messages** — steps 24, 48, 72, 96 (verified at 024H)

File sizes track this: ~59–68 KB for one message, ~119–131 KB for two. `SURC_MAX24` appears only four times per run.

> **Encoding defect 1 — the parameters are unresolvable from headers alone.** All three use discipline 10, category 3, numbers **192–194**, the WMO local-use range, while declaring `localTablesVersion = 0`. ecCodes 2.48.0 returns `unknown` for name, shortName and units. Identify by numeric triplet and PDT/time-range, not by `shortName`.

> **Encoding defect 2 — the statistical fields are labelled as averages but contain maxima.** Both use PDT 4.8 with `typeOfStatisticalProcessing = 0` (*average*; maximum is code 2), so ecCodes reports `stepType = avg`. As on [MARP](./hycom2d-marp-france.md), the data are maxima. A pipeline trusting `stepType` will read peak surge as a mean and under-report the hazard.

> ### Encoding defect 3 — on this configuration the 3-hour maximum window is offset by one hour
>
> **`SURC_MAX3` at step N contains the maximum over the instantaneous fields at N−2 and N−1 only. It excludes step N**, despite headers declaring `startStep = N−3, endStep = N`.
>
> Verified by comparing the field against candidate windows built from the hourly instantaneous fields, at two independent steps of the 2026-08-10 12Z cycle. Differences are expressed in units of the message's own 9-bit quantisation step (`q`), so a fit under ~1 q is exact to the packing precision:
>
> | Window | WARP @ +24h | WARP @ +48h | MARP @ +24h | MARP @ +48h |
> |---|---|---|---|---|
> | max{N−2, N−1} | **0.5 q** | **0.9 q** | 11.2 q | 3.5 q |
> | max{N−2, N−1, N} | 1.5 q | 2.3 q | **0.6 q** | **0.3 q** |
> | max{N−3, N−2, N−1} | 6.4 q | 2.9 q | 11.9 q | 9.6 q |
> | max{N−3 … N} | 7.3 q | 4.1 q | 1.9 q | 7.3 q |
>
> Each configuration fits its own window to sub-quantisation precision and fits the other's badly. **[MARP](./hycom2d-marp-france.md) is correct — its window matches the header. WARP is not.** On WARP, 28% of points have the "maximum" field falling *below* the instantaneous value at the labelled end step, by up to 3.6 cm — impossible for a genuine maximum over a window containing that step.
>
> **The practical consequence is a coverage gap.** Since the window at step N covers only {N−2, N−1}, and `SURC_MAX3` is written only at steps divisible by 3, **every step that is a multiple of 3 falls outside every 3-hour maximum window**. One hour in three is never represented in the maximum-surge product. A peak occurring exactly at hour 21, 24, 27 and so on will not appear in any `SURC_MAX3` field, and users tracking peak surge from these fields alone will miss it. Take maxima from the hourly `SURC_INS` series instead.
>
> `SURC_MAX24` shows the same endpoint exclusion, though less cleanly. Tested exhaustively against all 25 hourly fields from +0 to +24: the window 0..23 fits best (0.5 q mean, 96.4% of points at or above the hourly max) and adding step 24 degrades it (0.8 q, 88.4%). A ~3.6% tail of points falls up to 7 cm short of the hourly maximum and is not explained by either window — the 24-hour field should be treated as approximate.

**No** total water level, tidal level, depth-averaged current, inundation depth, or wave setup field is distributed.

### Packing precision
`grid_jpeg` (JPEG 2000) at **9 bits per value** — coarser than the MFWAM packages' 16-bit `grid_ccsds`. Nine bits gives 512 quantisation levels spanning each message's own value range, so absolute precision is dynamic: about 1.0 mm at the sampled range here, and proportionally coarser during a large surge event when the range widens. Because this domain has a much larger surge range than the Mediterranean, its effective precision is roughly four times coarser than MARP's at comparable conditions.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Output geometry:** **Gridded fields only.**
- **Data formats:** GRIB2 (`grid_jpeg` packing)
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-warp-resolution-0-04deg
- **Portal dataset id:** `65bd17fe9ec6ae3f87a53349`

> **The 10-minute station time series described in the documentation are not published here.** `descriptif-modele-hycom2d.pdf` states the system produces both 2D gridded fields and surge time series at 10-minute resolution for a list of named sites. Only the gridded fields appear on the portal; a survey of all 10 datasets under the `pnt-vague-surcote` tag found no station product and no site-position metadata. Under the storm surge template's point-output exception this would have been in scope; it is simply not distributed through this channel. **TBD** where it goes.

### Access paths
**The portal exposes the latest cycle only** — 103 GRIB2 resources plus 2 documentation PDFs, refreshed in place. The portal file list is complete; all 103 steps are present.

**The object store carries the rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/HYCOM2D-WARP/004/SP1/<FILE>
```

The grid token is `004`, not `0042`.

### Retention — 15 days from cycle time, expiring at cycle granularity
Objects expire **15 days after their nominal cycle time**, not 15 days after they were written — a bucket-wide lifecycle policy shared with every product in the `vague-surcote` tree. Confirmed twice on 2026-08-10 by watching the boundary advance: the `2026-07-26T06Z` cycle vanished between 04:45 and 06:12 UTC, and `2026-07-26T12Z` was gone by 18:45, each exactly 15 days after its own cycle time.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Complete | Latency |
|---|---|---|
| 00Z | 03:48 | T+3h48m |
| 06Z | 10:36 | T+4h36m |
| 12Z | 15:29 | T+3h29m |
| 18Z | 22:39 | T+4h39m |

Each cycle publishes in about two minutes. WARP is the **last of the four HYCOM2D configurations** to land but still ahead of every wave product — verified order at the 2026-08-09 00Z cycle: HYCOM2D-WARO 03:01 → HYCOM2D-MARO 03:04 → HYCOM2D-MARP 03:33 → **HYCOM2D-WARP 03:48** → MFWAM GLOB01 04:27 → WW3 packages 04:43–04:58.

- **Volume:** 8.6–8.7 MiB per cycle, ~35 MiB/day — larger than MARP's 25 MiB/day in proportion to the bigger grid, and still the second-smallest product in the tree.

---

## Notes
- **Operational status:** active, in continuous operation since 14 January 2014.

- **Prefer the hourly instantaneous field for peak surge on this domain.** Between the mislabelled statistical process, the one-hour window offset, the resulting one-in-three coverage gap, and the coarser effective packing precision, the `SURC_MAX3` and `SURC_MAX24` fields carry more caveats than value here. Computing maxima from the 103 hourly `SURC_INS` fields avoids all four problems at the cost of downloading the full step series — about 8.7 MiB per cycle, which is negligible.

- **The four HYCOM2D configurations.** Two domains × two forcing models — unlike the WW3 wave family, the matrix is complete:

  | Package | Domain | Grid | Forcing | Range |
  |---|---|---|---|---|
  | [HYCOM2D-MARP](./hycom2d-marp-france.md) | MEDIT0042 (45N–35N, 6W–17E) | 1/24° | ARPEGE | 102 h |
  | **HYCOM2D-WARP** (this entry) | COTWEU0042 (62N–43N, 9W–10E) | 1/24° | ARPEGE | 102 h |
  | [HYCOM2D-MARO](./hycom2d-maro-france.md) | MEDIT0042 | 1/24° | AROME | 51 h |
  | [HYCOM2D-WARO](./hycom2d-waro-france.md) | COTWEU0042 | 1/24° | AROME | 51 h |

  All four verified live at those ranges on 2026-08-09. `descriptif-modele-hycom2d.pdf` describes the AROME-forced runs as 5× daily (00, 03, 06, 12, 18 UTC) at 42/39/36/42/36 h; live enumeration shows 4 cycles at 51 h. **Whether the WARO configuration shares this domain's window offset is untested** — worth checking when that entry is written, since the offset may be domain-specific rather than forcing-specific.

- **Relationship to the WW3 wave family.** Same institutional origin, same HOMONIM programme, same SHOM bathymetry, same naming convention — different model core, different output geometry (regular grid rather than unstructured mesh), and a complete 2×2 matrix where WW3 has only three members. This configuration's water level feeds [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md); nothing feeds back.

- **Portal metadata is stale**: `temporal_coverage` reads 2024-02-28 to 2024-02-29, from the dataset's February 2024 creation, and does not track the rolling window.

- **What the operator runs is larger than what is published.** The full system produces tide, total water level and site time series; the open distribution carries only the gridded surge residual on a regridded regular grid.

---

## Recent version history

No formal version history is published.

### 14 January 2014 — operational at Météo-France
The SHOM-developed barotropic HYCOM surge system entered operations, configured on the ATL and MED domains with bottom friction tuned for tidal reproduction and wind stress calibrated against one year of simulation plus 22 storm events.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-warp-resolution-0-04deg
- Model descriptif (FR; states the 1/24° field resolution and the validation biases): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-warp-resolution-0-04deg/20240228-171436/descriptif-modele-hycom2d.pdf
- Package descriptif (FR; per-configuration grids, cycles and ranges — the more reliable of the two): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-warp-resolution-0-04deg/20240228-170316/description-paquets-modele-hycom2d.pdf
- Both documents are shared across all four HYCOM2D datasets; working copies are reachable from the [MARP entry](./hycom2d-marp-france.md#official-documentation) if these URLs fail.
- HYCOM code: https://hycom.org/
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live-verified 2026-08-10 against the 2026-08-10T12:00:00Z cycle (steps 000H, 003H, 021H–024H, 045H–048H, 102H plus the full 000H–024H series decoded with ecCodes 2.48.0, including empirical determination of the statistical windows), with cycle enumeration, retention-boundary observation and publication timestamps sampled 2026-07-26 to 2026-08-10.*
