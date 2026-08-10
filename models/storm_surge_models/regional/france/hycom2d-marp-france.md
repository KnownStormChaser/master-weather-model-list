# HYCOM2D-MARP (Météo-France Storm Surge Model — Mediterranean, ARPEGE-forced)

## What this model is
HYCOM2D-MARP is the ARPEGE-forced Mediterranean configuration of Météo-France's operational **storm surge** forecast system. It publishes the **surge residual only** — the meteorological departure of sea level from the astronomical tide — not total water level.

> **This is not the American HYCOM ocean model.** The name is genuinely shared: the system is built on a **barotropic (2D) version of the HYCOM code**, the same codebase as the US Navy / NOAA HYCOM ocean prediction system. But it is configured here as a depth-averaged tide-and-surge model with no baroclinic structure, no temperature or salinity, and no 3D currents. It belongs under `storm_surge_models/`, not `ocean_models/`. Anyone arriving from the US HYCOM context should expect a different product class entirely.

The model was developed by **SHOM** with Météo-France under the **HOMONIM** project (*Historique, Observation, MOdélisation des NIveaux Marins*), commissioned by the Direction Générale pour la Prévention des Risques under the interministerial *Submersions Rapides* plan. It has been operational at Météo-France since **14 January 2014** — making it the earliest-operational component of the HOMONIM family, predating the [coastal WW3 wave system](../../../wave_models/regional/france/ww3-marp-france.md) by fourteen months.

`MARP` decomposes as **M**editerranean + **ARP**EGE, the same convention used for the WW3 wave packages.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **GRIB2 originating centre (verified):** `lfpw` — French Weather Service, Toulouse (centre 85), subCentre 0, `generatingProcessIdentifier = 134`

---

## What area it covers
- **Coverage:** Western and central Mediterranean — the French Mediterranean seaboard, the Gulf of Lion, Corsica, Sardinia, the Balearics, the Tyrrhenian Sea and the Strait of Sicily approaches
- **Domain (verified):** 45°N–35°N, 6°W–17°E — the `MEDIT0042` grid
- **Inundation coverage:** **None.** The domain stops at the coastline. 64,553 of 133,273 grid points are valid (48.44%); the remainder are masked by bitmap. No overland cells, no wetting and drying, and therefore no inundation depth product.

---

## Basic details
- **Model type:** Deterministic storm surge model
- **Core hydrodynamic model:** HYCOM, barotropic configuration
- **Dimensionality:** **2D depth-averaged (barotropic)**
- **Forecast length (verified):** **102 h at every cycle**
- **Update frequency / cycles (verified):** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** hourly for the instantaneous field; 3-hourly and 24-hourly for the two statistical fields

> **The two documents disagree on forecast length, and the model descriptif loses.** `descriptif-modele-hycom2d.pdf` states the ARPEGE-forced runs reach **102 h, 72 h, 114 h and 60 h** at 00Z, 06Z, 12Z and 18Z respectively. `description-paquets-modele-hycom2d.pdf` states 0–102 h hourly at all four cycles. Live enumeration on 2026-08-09 found **103 files reaching 102 h at every cycle**; the package descriptif is correct.
>
> Note that the erroneous **102/72/114/60 quartet is the same set of figures that appears in the MFWAM model documentation** for GLOB01, where it is also contradicted by live data. It appears to be recurring boilerplate across Météo-France's `descriptif-modele-*.pdf` series rather than an isolated error, and should be treated with suspicion wherever it appears.

---

## Grid and bathymetry
- **Grid type (distributed):** regular latitude–longitude, **553 × 241**, 133,273 points
- **Grid type (native):** **curvilinear.** The model runs on a curvilinear grid targeting ~1 km resolution along the French coast; the published product is a regridded regular-latlon extraction. Nearshore detail present in the native run is not fully recoverable from the distributed fields.
- **Horizontal resolution (verified):** **0.041667° = exactly 1/24°** (~3.9 km zonal at 40°N, ~4.6 km meridional)
- **Bathymetry source:** SHOM, updated with the latest available survey data under HOMONIM
- **Wetting and drying:** No

> **The advertised resolution is a rounding, twice over.** The dataset is titled "Résolution 0,04°" and the grid is named `MEDIT0042`, but `descriptif-modele-hycom2d.pdf` states the fields are produced at **1/24°**, and the GRIB2 headers confirm `iDirectionIncrementInDegrees = 0.041667`. The true spacing is about 4% coarser than the "0.04°" label. Use 1/24° for any reprojection or regridding arithmetic.

> **Longitude is encoded in the 0–360 convention and wraps the prime meridian**, as in [MFWAM FRANGP0025](../../../wave_models/regional/france/mfwam-hr-france.md): headers give `longitudeOfFirstGridPointInDegrees = 354.0` (−6°) and last `17.0`. Use the decoded coordinate array, not the header pair.

---

## Vertical datum and reference level
- **Vertical datum:** **Not applicable in the usual sense.** The published field is a *residual*, not a water level referenced to a datum. Values are the surge component alone, so no chart datum, LAT or MSL reference is required to interpret them — and none is stated in either document.
- **What the water level field is measured relative to:** the model's own astronomical tide simulation. The surge is the difference between a run with meteorological forcing and the modelled tide.
- **Datum conversion offsets provided?** No, and not needed for the residual. **But this means the product cannot be converted to total water level without an external tidal prediction** — see *Tide handling*.
- **Units:** metres. The parameters carry no decodable units (see *What it provides*), but the sampled range of −0.037 to +0.091 on a calm August day, together with the documentation's discussion of biases in centimetres, is consistent with metres and inconsistent with any other plausible unit.
- **Mean sea level trend / SLR handling:** not documented. **TBD.**

> **Documented bias — the model under-predicts the surges that matter.** Long simulations give a mean bias of **−2 cm**, worsening to **−5 cm** for the strongest surges. Across 22 storm events the peak surge was underestimated by about **10 cm** on average (8 cm on maximum total height), with a **34-minute phase lag** on total height. The bias is therefore largest exactly in the conditions the product exists to forecast, and is not corrected in the distributed fields.

---

## Tide handling
- **Are tides included?** **Modelled but not distributed.** The dataset description states the system integrates a tide simulation (*"intégrant une simulation de la marée"*), and bottom friction was tuned specifically to reproduce the tide across both domains. But the three published parameters are all surge residuals.
- **Tidal forcing source:** not documented. **TBD.**
- **Separation of components:** only the surge residual is published. Neither the tidal water level nor the combined total water level is distributed in this package.
- **Tide–surge interaction:** modelled **nonlinearly within the run** — the surge is obtained from a tide-and-surge simulation rather than superposed afterward. This is a meaningful quality distinction from linear-superposition surge products, and it means the residual already accounts for tide–surge coupling.

> **You cannot get total water level from this product alone.** Users need an independent tidal prediction for the location of interest, and must accept that combining an externally predicted tide with this residual reintroduces the linearity error the model avoided internally. For French waters, SHOM publishes tidal predictions separately.

---

## Forcing and coupling
- **Meteorological forcing — wind:** [ARPEGE](../../../nwp_models/global/france/arpege.md) (~10 km over France). Wind stress was calibrated against one full year plus 22 storm events, separately for each domain.
- **Meteorological forcing — pressure:** ARPEGE MSLP. Not separately documented, but a barotropic surge model necessarily consumes it; the inverse-barometer contribution is co-equal with wind stress.
- **Wave contribution:** **None.** No wave setup or radiation-stress coupling is documented. Note the coupling runs the *other* way in this family: [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md) ingests HYCOM2D water level and current on the Atlantic side. There is no such feedback into the surge model, and none at all on the Mediterranean side.
- **River discharge / freshwater forcing:** not documented. **TBD.**
- **Ocean forcing / boundary conditions:** not documented. **TBD.**
- **Ice forcing:** not applicable.
- **Parent for:** none. On the Atlantic side the equivalent configuration forces the coastal wave model; the Mediterranean configurations do not.

---

## Data assimilation
- **Assimilates water level observations:** **No.** No assimilation is described. Tide gauge observations were used for calibration and validation, not for real-time correction.

---

## What it provides
**Three parameters**, all on `meanSea` (level 0), distributed across the step files by a schedule that varies with lead time:

| Doc name | Description | D,C,N | PDT | Present at |
|---|---|---|---|---|
| `SURC_INS` | Instantaneous surge | 10,3,**192** | 0 | every step, 0–102 h |
| `SURC_MAX3` | Maximum surge over the preceding 3 h | 10,3,**193** | 8 | every step divisible by 3 |
| `SURC_MAX24` | Maximum surge over the preceding 24 h | 10,3,**194** | 8 | steps 24, 48, 72, 96 only |

**Message count per file is therefore not constant** — 1, 2 or 3 depending on the step:

- **1 message** — step 0, and every step not divisible by 3
- **2 messages** — steps divisible by 3 but not 24 (verified at 003H, 102H)
- **3 messages** — steps 24, 48, 72, 96 (verified at 024H)

That gives **141 messages across 103 files** per cycle. File sizes track this directly: ~43–50 KB for single-message files, ~76–100 KB for two, and larger again for three. `SURC_MAX24` appears **only four times** in a 102-hour run — not at step 0 (no preceding 24 h) and not at 102 (not a multiple of 24).

> **Encoding defect 1 — the three parameters are unresolvable from headers alone.** All use discipline 10, category 3, numbers **192–194**, the WMO local-use range, while declaring `localTablesVersion = 0`. ecCodes 2.48.0 returns `unknown` for name, shortName and units on all three. Identify them by the numeric triplet and by the PDT/time-range combination, not by `shortName`.

> **Encoding defect 2 — the statistical fields are labelled as averages but contain maxima.** Both `SURC_MAX3` and `SURC_MAX24` use PDT 4.8 with **`typeOfStatisticalProcessing = 0`**, which is *average*. Maximum is code 2. ecCodes accordingly reports `stepType = avg`.
>
> **The data are maxima.** Verified empirically on the 2026-08-10 12Z cycle by comparing the `SURC_MAX3` field at +24 h against the instantaneous fields at +22, +23 and +24 h across all 64,553 valid points:
>
> | Hypothesis | Mean abs. difference | Max abs. difference |
> |---|---|---|
> | Field is the **maximum** | **0.000157** | **0.000461** |
> | Field is the mean | 0.004964 | 0.056882 |
>
> The residual against the maximum is consistent with 9-bit packing precision, and the field sits a hair *above* the three-hourly maximum at each point (e.g. 0.0356 against 0.0355) because the model's internal maximum is taken over sub-hourly timesteps. Any pipeline that trusts `stepType` will treat peak surge as a 3-hour mean and **under-report the hazard**.

> **Encoding defect 3 — `typeOfProcessedData` is unset** (`missing` rather than 1), the same defect as the MFWAM packages.

**No** total water level, tidal level, depth-averaged current, inundation depth, or wave setup field is distributed.

### Packing precision
Files use `grid_jpeg` (JPEG 2000) at **9 bits per value** — notably coarser than the MFWAM packages' 16-bit `grid_ccsds`. Nine bits gives 512 quantisation levels spanning each message's own value range, so absolute precision is dynamic: fine on calm days (~0.25 mm at the sampled range) and proportionally coarser during a large surge event, when the range widens. Worth accounting for if differencing fields or computing thresholds near a warning level.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Output geometry:** **Gridded fields only.** See the note below on the station product.
- **Data formats:** GRIB2 (`grid_jpeg` packing)
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-marp-resolution-0-04deg
- **Portal dataset id:** `65bd183c9ec6ae3f87a5334a`

> **The 10-minute station time series described in the documentation are not published here.** `descriptif-modele-hycom2d.pdf` states the system produces forecasts in two formats: 2D gridded fields, *and* surge time series at 10-minute resolution for a list of named sites. Only the gridded fields appear on the portal. A survey of all 10 datasets under the `pnt-vague-surcote` tag found no station product and no site-position metadata file. Under the storm surge template's point-output exception this would have been in scope; it simply is not openly distributed through this channel. Where it goes — SHOM, the vigilance chain, or nowhere public — is **TBD**.

### Access paths
**The portal exposes the latest cycle only** — 103 GRIB2 resources plus 2 documentation PDFs, refreshed in place. Unlike [MFWAM FRANGP0025](../../../wave_models/regional/france/mfwam-hr-france.md), the portal file list here is complete: all 103 steps are present, none dropped.

**The object store carries the rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/HYCOM2D-MARP/004/SP1/<FILE>
```

Note the grid token is `004`, not `0042` or `01`.

### Retention — 15 days from cycle time, expiring at cycle granularity
Objects expire **15 days after their nominal cycle time**, not 15 days after they were written — a bucket-wide lifecycle policy shared with every product in the `vague-surcote` tree. Confirmed twice on 2026-08-10 by watching the boundary advance: the `2026-07-26T06Z` cycle vanished between 04:45 and 06:12 UTC, and `2026-07-26T12Z` was gone by 18:45, each exactly 15 days after its own cycle time. The oldest available cycle is a moving target within the day.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Complete | Latency |
|---|---|---|
| 00Z | 03:33 | T+3h33m |
| 06Z | 10:23 | T+4h23m |
| 12Z | 15:15 | T+3h15m |
| 18Z | 22:27 | T+4h27m |

Each cycle publishes in about two minutes. **The HYCOM2D surge products lead the entire `vague-surcote` tree** — verified order at the 2026-08-09 00Z cycle: HYCOM2D-WARO 03:01 → HYCOM2D-MARO 03:04 → HYCOM2D-MARP 03:33 → HYCOM2D-WARP 03:48 → MFWAM GLOB01 04:27 → the WW3 packages 04:43–04:58. Surge output is available roughly an hour before the waves, and about 90 minutes before the coastal WW3 files.

- **Volume:** 6.3–6.4 MiB per cycle, ~25 MiB/day — by a wide margin the smallest product in the tree.

---

## Notes
- **Operational status:** active. In continuous operation since 14 January 2014.

- **The four HYCOM2D configurations.** Two domains × two forcing models, and unlike the WW3 wave family the matrix is complete:

  | Package | Domain | Grid | Forcing | Range |
  |---|---|---|---|---|
  | **HYCOM2D-MARP** (this entry) | MEDIT0042 (45N–35N, 6W–17E) | 1/24° | ARPEGE | 102 h |
  | [HYCOM2D-WARP](./hycom2d-warp-france.md) | COTWEU0042 (62N–43N, 9W–10E) | 1/24° | ARPEGE | 102 h |
  | [HYCOM2D-MARO](./hycom2d-maro-france.md) | MEDIT0042 | 1/24° | AROME | 51 h |
  | [HYCOM2D-WARO](./hycom2d-waro-france.md) | COTWEU0042 | 1/24° | AROME | 51 h |

  All four verified live at those ranges on 2026-08-09. Note that `descriptif-modele-hycom2d.pdf` describes the AROME-forced runs as **5× daily** (00, 03, 06, 12, 18 UTC) at 42/39/36/42/36 h; live enumeration shows **4 cycles at 51 h**, matching the package descriptif instead.

- **Relationship to the WW3 wave family.** Same institutional origin, same HOMONIM programme, same SHOM bathymetry lineage, same `MARO`/`MARP`/`WARO`/`WARP` naming — but a different model core, a different output geometry (regular grid rather than unstructured mesh), a different format profile, and a complete 2×2 matrix where WW3 has only three members. The Atlantic surge output feeds [WW3-WARP](../../../wave_models/regional/france/ww3-warp-france.md) as water-level and current forcing; nothing feeds back.

- **Portal metadata is stale**: `temporal_coverage` reads 2024-02-28 to 2024-02-29, from the dataset's February 2024 creation, and does not track the rolling window.

- **What the operator runs is larger than what is published.** The full system produces tide, total water level and site time series; the open distribution carries only the gridded surge residual on a regridded regular grid. Both the tidal component and the native curvilinear resolution are absent from the public product.

---

## Recent version history

No formal version history is published.

### 14 January 2014 — operational at Météo-France
The SHOM-developed barotropic HYCOM surge system entered operations, configured on the ATL and MED domains with bottom friction tuned for tidal reproduction and wind stress calibrated against one year of simulation plus 22 storm events.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-surcote-oceanique-hycom2d-marp-resolution-0-04deg
- Model descriptif (FR; states the 1/24° field resolution and the validation biases): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-marp-resolution-0-04deg/20240228-171436/descriptif-modele-hycom2d.pdf
- Package descriptif (FR; per-configuration grids, cycles and ranges — the more reliable of the two): https://static.data.gouv.fr/resources/paquets-de-modele-de-surcote-oceanique-hycom2d-marp-resolution-0-04deg/20240228-170316/description-paquets-modele-hycom2d.pdf
- HYCOM code: https://hycom.org/
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live-verified 2026-08-10 against the 2026-08-10T12:00:00Z cycle (steps 000H, 003H, 022H, 023H, 024H, 102H decoded with ecCodes 2.48.0, including empirical testing of the statistical-process encoding), with cycle enumeration, retention-boundary observation and publication timestamps sampled 2026-07-26 to 2026-08-10.*
