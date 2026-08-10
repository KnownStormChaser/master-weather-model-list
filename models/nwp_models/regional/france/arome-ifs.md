# AROME-IFS

## What this model is
AROME-IFS is a parallel operational run of Météo-France's convection-permitting AROME model over metropolitan France, **initialized from ECMWF's IFS global analysis instead of from ARPEGE and AROME's own data assimilation cycle**.

It is not a different model and not a different distribution of an existing forecast: it is the **same AROME model, run a second time from different initial and boundary conditions**. Everything about the output packaging is identical to [AROME France](./arome-france.md) — same EURW1S40 grid, same eleven packages, same parameter set, same 51-hour range — but the forecast itself is distinct, produced by a separate production chain running on a different schedule.

Météo-France describes it as combining AROME's fine resolution (1.3 km) with initialization from ECMWF's global IFS model, giving a better representation of local phenomena such as thunderstorms and sea breezes.

> **What this is useful for.** Running the same convection-permitting model from two independent global analyses gives a cheap read on **initial-condition sensitivity** over France. Where AROME France and AROME-IFS agree, confidence in the convective-scale detail is higher; where they diverge, the large-scale forcing is the uncertain part. This is a poor-man's two-member multi-analysis ensemble, and a genuinely different use case from either AROME France or the AROME ensembles — which is why it is documented separately here rather than folded into the AROME France entry.

---

## Who runs it
- **Organization:** Météo-France (initial and boundary conditions from ECMWF)
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and surrounding regions of Western Europe
- **Operational domain:** EURW1S40
- **Grid (live-verified):** `regular_ll`, **1121 × 717**, 0.025° × 0.025°, **803,757 points per field**. First grid point 55.4°N / 348.0°E, last 37.5°N / 16.0°E, north-to-south row order. Bounds **55.4°N–37.5°N, 12°W–16°E** — **identical to [AROME France](./arome-france.md)**.
- **The native grid is trapezoidal** — see *Missing values* under Notes.

> **Longitude uses a 0–360° axis and the domain crosses the prime meridian.** The first grid longitude is **348.0°E** (= 12°W), numerically larger than the last (16.0°E).

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core); SURFEX surface modelling
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 1.3 km native resolution; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude. **There is no 0.01° distribution** — only `aromeifs/0025/` exists in the bucket.
- **Vertical levels:** 90
- **Forecast length (live-verified):** **51 hours**, all cycles
- **Update frequency / cycles (live-verified):** **4× daily (00, 06, 12, 18 UTC)** — half the cadence of AROME France, which runs 8× daily. No files appear at 03, 09, 15 or 21 UTC.
- **Temporal output resolution (live-verified):** Hourly throughout, steps 0–51
- **Time step:** 50 s

---

## Initialization — the defining difference
- **Data assimilation:** **None of its own.** Where [AROME France](./arome-france.md) runs a 1-hour 3DEnVar cycle in the OOPS framework with Incremental Analysis Update, AROME-IFS is initialized directly from ECMWF's IFS global analysis.
- **Initial and boundary conditions:** ECMWF **IFS** (~9 km globally), rather than [ARPEGE](../../global/france/arpege-global.md).
- **Practical consequence:** AROME-IFS necessarily waits on IFS dissemination, which is why it publishes around **T+6 h 40 m** against AROME France's T+1 h 53 m to T+5 h 12 m. It is not a fast product; its value is as an independent second opinion, not as timely guidance.

> **This is the cleanest structural distinction from AROME France**, and it is what makes the two forecasts genuinely different rather than two views of one run. It is also reflected in the GRIB encoding — see `generatingProcessIdentifier` under Notes.

---

## What it provides

The distribution is **package-for-package identical to [AROME France](./arome-france.md) 0.025°**: eleven packages, each in nine forecast-range files (`00H06H`, `07H12H`, `13H18H`, `19H24H`, `25H30H`, `31H36H`, `37H42H`, `43H48H`, `49H51H`) — **99 files per cycle**.

Live verification decoded all eleven packages of the `00H06H` file and found **6,576 messages**, matching AROME France's count for the same file exactly, with the same parameters on the same levels in the same packages.

### Package inventory (live-verified, 2026-08-09 18 UTC, `00H06H` file)

| Package | Messages | Level type | Parameters |
|---|---:|---|---|
| **SP1** | 97 | surface, 2 m, 10 m | `prmsl`, `2t`, `2r`, `10u`, `10v`, `10si`, `10wdir`, `max_i10fg`, `max_10efg`, `max_10nfg`, total cloud cover, `tp`, `tsnowp`, `tgrp`, `ssrd` |
| **SP2** | 79 | surface, 2 m | `h`, `sp`, surface `t`, `lcc`, `mcc`, `hcc`, `CAPE_INS`, `blh`, `tirf`, `min_2t`, `max_2t`, `2d`, `2sh` |
| **SP3** | 67 | surface | total column water vapour, evaporation, `slhf`, `sshf`, `strd`, `ssr`, `str`, `ssrc`, `strc`, `iews`, `inss` |
| **HP1** | 1225 | height above ground | `t`, `r`, `pres` (25 levels); `u`, `v`, `ws`, `wdir` (24 levels + separate 10 m fields) |
| **HP2** | 1550 | height above ground (25) | `z`, `q`, `dpt`, `tke`, `clwc`, `crwc`, `cswc`, `ciwc`, `cc` |
| **HP3** | 42 | height above ground (7) | radar reflectivity |
| **IP1** | 840 | isobaric (24) | `t`, `r`, `u`, `v`, `z` |
| **IP2** | 1008 | isobaric (24) | `clwc`, `crwc`, `cswc`, `ciwc`, `cc`, + 1 unresolved |
| **IP3** | 1176 | isobaric (24) | `dpt`, `q`, `ws`, `wdir`, `w`, `wz`, `pv` |
| **IP4** | 240 | isobaric (24 / 16) | `tke` (24 levels); radar reflectivity (16 levels) |
| **IP5** | 252 | isobaric (5 / 20) + potential vorticity | `absv`, `vo` (5 levels); `papt` (20 levels); `u`, `v`, `z` on PV surfaces |

### Level sets (live-verified — identical to AROME France)
- **Isobaric — 24 levels:** 100, 125, 150, 175, 200, 225, 250, 275, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — radar reflectivity (IP4), 16 levels:** 200 through 925 hPa
- **Isobaric — `absv`/`vo` (IP5), 5 levels:** 300, 500, 600, 700, 850 hPa
- **Isobaric — `papt` (IP5), 20 levels:** 200 through 1000 hPa
- **Height above ground — 25 levels:** 10, 20, 35, 50, 75, 100, 150, 200, 250, 375, 500, 625, 750, 875, 1000, 1125, 1250, 1375, 1500, 1750, 2000, 2250, 2500, 2750, 3000 m
- **Height above ground — radar reflectivity (HP3), 7 levels:** 500, 750, 1000, 1500, 2000, 2500, 3000 m
- **Potential vorticity: two surfaces at 1.5 and 2.0 PVU** (encoded 1500, 2000)

### Step 0 is a reduced set
As in AROME France, instantaneous fields carry 7 steps in a 6-hour range file (0–6) but several start at step 1: **total cloud cover, `lcc`, `mcc`, `hcc`, `tke`** (HP2), and **both radar-reflectivity fields** (HP3 and IP4). `h` (orography, SP2) appears once per file as a static field. Time-processed fields carry 6, as expected for six one-hour intervals.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** **No.** Unlike the ARPEGE and AROME deterministic datasets, this one carries **no HVD badge**.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/paquets-arome-ifs-resolution-0-025deg

> **Licensing provenance — worth flagging.** AROME-IFS is initialized from ECMWF IFS data, and Météo-France redistributes the resulting forecasts under Etalab 2.0. ECMWF's open data is CC-BY-4.0, so the chain appears clean, but this is a derived product whose upstream inputs carry their own attribution terms. Météo-France publishes no statement on the point. **TBD** — users with strict compliance requirements should confirm with Météo-France rather than assume Etalab 2.0 discharges all obligations.

### Primary access — data.gouv.fr object storage (no authentication)

Served from the same OVH-hosted object storage as the other deterministic products. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/aromeifs/0025/{package}/aromeifs__0025__{package}__{range}__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T18:00:00Z`), `{package}` is one of `SP1 SP2 SP3 HP1 HP2 HP3 IP1 IP2 IP3 IP4 IP5`, and `{range}` is one of the nine groups above. Note the **double underscores**, the model token `aromeifs`, and the grid token `0025`.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T18:00:00Z/aromeifs/0025/HP3/aromeifs__0025__HP3__00H06H__2026-08-09T18:00:00Z.grib2
  ```

- **The bucket is anonymously listable:**
  ```bash
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T18:00:00Z/aromeifs/0025/&max-keys=1000"
  ```
  Paginate via `NextContinuationToken` (URL-encode it). Objects serve `Content-Length` and `Last-Modified` on `HEAD` and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling**, the same as the other `meteofrance-pnt` products.

- **Publication latency (live-verified)**, measured across all four cycles of 2026-08-08:

  | Cycle | First file | Last file |
  |---|---|---|
  | 00 UTC | T+6 h 52 m | T+7 h 34 m |
  | 06 UTC | T+6 h 37 m | T+7 h 18 m |
  | 12 UTC | T+6 h 43 m | T+7 h 28 m |
  | 18 UTC | T+6 h 37 m | T+7 h 21 m |

  Much more consistent than AROME France, whose cycles range from T+1 h 53 m to T+5 h 12 m, but **considerably later** — a direct consequence of waiting on IFS dissemination. The publication window is tight (~45 minutes) and does not show the 00/12-versus-06/18 split seen in the ARPEGE-driven products.

- **Volume (live-verified, 2026-08-09 18 UTC):** **~23.2 GB per cycle**, ~93 GB/day, roughly **1.4 TB** resident under the 15-day window. Per package per cycle: HP1 5.91 GB, IP3 5.68 GB, HP2 4.07 GB, IP1 3.56 GB, IP5 1.13 GB, IP2 0.98 GB, IP4 0.52 GB, SP3 0.51 GB, SP1 0.45 GB, SP2 0.32 GB, HP3 0.05 GB. Individual files range from 2.8 MB (HP3 `49H51H`) to 792 MB (HP1 `00H06H`).

  Per cycle this is within 1% of AROME France; at half the cadence, the daily footprint is half.

### Companion static files
**None.** No constant-fields file is published for AROME-IFS. The grid is identical to AROME France's EURW1S40, so **`constant-eurw1s40.grib2` from the [AROME France](./arome-france.md) dataset applies unchanged** — same orography, same land–sea mask, same 803,757 points.

### Secondary access — Météo-France API portal
Météo-France also exposes AROME-IFS through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 packages with no authentication.

---

## Notes

### `generatingProcessIdentifier = 254` — how to tell AROME-IFS from AROME France
Because the two products share a grid, a package layout, a parameter set and a valid-time axis, **the GRIB headers are the only reliable discriminator**. Météo-France assigns a distinct process identifier per production chain:

| System | `generatingProcessIdentifier` |
|---|---:|
| [ARPEGE](../../global/france/arpege-global.md) / PE-ARPEGE | 211 |
| [AROME France](./arome-france.md), [AROME France HD](./arome-france-hd.md) | 204 |
| [AROME Outre-Mer](./arome-outre-mer.md) | 121 |
| PE-AROME New Caledonia | 248 |
| **AROME-IFS** | **254** |

If both feeds land in the same archive, filenames and paths will distinguish them, but any process that strips or normalizes filenames must key on this field. All other header values checked — `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw`, `subCentre = 0`, `typeOfGeneratingProcess = 2`, `grid_ccsds` packing — are identical to AROME France.

### Missing values: 17.2% of every field
The trapezoidal native grid produces the same masking as AROME France, at the same proportion: **`bitmapPresent = 1`** and **138,076 of 803,757 points (17.2%)** masked — bit-for-bit the same count, as expected on an identical grid.

ecCodes' default `missingValue` is **9999**, not NaN, so masked points return as the literal 9999.0 and `numpy.isnan()` finds nothing. Set `missingValue` to NaN before reading, mask on `value > 9998`, or crop to a rectangle inside the trapezoid. Météo-France warns that the masked set can differ between parameters and recommends splitting multi-parameter packages into single-parameter GRIB files first, since some tools (CDO is named explicitly) do not handle multi-parameter GRIB cleanly.

### Accumulation convention
All time-processed fields accumulate **from run start** (`0-1`, `0-2`, … `0-6`), including `tp`, `tsnowp`, `tgrp`, `tirf`, `ssrd` and the SP3 fluxes, plus the 2 m and gust extremes. Hourly totals require differencing successive messages. Same as AROME France.

### 10 m winds carry different shortNames from the rest of the HP1 profile
In HP1, temperature, relative humidity and pressure appear on all **25** height levels including 10 m as plain `t`, `r`, `pres`. The wind fields appear on **24** levels (20–3000 m) plus separate 10 m entries that ecCodes resolves to `10u`, `10v`, `10si`, `10wdir`. The 10 m wind data is present, but selecting `ws` across levels silently misses it. Match on discipline/category/number and level rather than `shortName`.

### Parameters that do not decode
Identical to AROME France:

| Encoding | Package | Identification |
|---|---|---|
| 0/6/1 | SP1 | **Total cloud cover** (`NEBUL`) — WMO-standard encoding ecCodes leaves unresolved, because its `tcc` is bound to the ECMWF-local 0/6/192 |
| 0/1/64 | SP3 | **Total column water vapour** (`COLONNE_VAPO`) |
| 0/1/6 | SP3 | **Evaporation** (`FLEVAP`), accumulated |
| 0/16/192 | IP4, HP3 | **Derived radar reflectivity** (`RFLCTVT`) |
| 0/1/201 | IP2 | **Undocumented — TBD**; evidence points to specific graupel content (see [AROME France](./arome-france.md)) |

`CAPE_INS` again has no resolvable level type, and `iews`/`inss` are named "Instantaneous … turbulent surface stress" by ecCodes while encoded as `accum` — they are time-integrated.

### Documentation
**There is no package-description PDF for AROME-IFS.** The data.gouv.fr dataset page carries no documentation resources at all, and its own metadata-quality panel flags it for "Documentation des fichiers manquante" and "Couverture temporelle non renseignée". The dataset description links to the Météo-France Confluence page, which has an AROME-IFS section.

In practice the [AROME package description](./arome-france.md) (v. 01/04/2025) describes this distribution accurately, since the package structure is identical — with the same two caveats noted in that entry (HP1 listed as carrying `Z`, which it does not; HP2's `Z` described with isobaric wording).

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host resolves and recognises the bucket name but returns `NoSuchKey` for every object.
- **The community AWS mirror is gone** (`mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket`). It predates AROME-IFS and never carried it.

### How AROME-IFS differs from AROME France
| | [AROME France](./arome-france.md) | AROME-IFS (this entry) |
|---|---|---|
| Initialization | 3DEnVar, 1 h cycle, ARPEGE coupling | ECMWF IFS analysis, no own assimilation |
| Cycles | 8× daily | 4× daily (00/06/12/18) |
| First file published | T+1 h 53 m to T+4 h 20 m | T+6 h 37 m to T+6 h 52 m |
| Volume per day | ~187 GB | ~93 GB |
| `generatingProcessIdentifier` | 204 | 254 |
| HVD badge | yes | no |
| 0.01° distribution | yes ([AROME France HD](./arome-france-hd.md)) | no |
| Grid, packages, parameters, levels, range | — | **identical** |

### Model family and relationships
- **Twin run:** [AROME France](./arome-france.md) — same model, same grid, same output structure, different initialization. Comparing the two at matching valid times is the intended use.
- **No HD counterpart:** there is no 0.01° AROME-IFS distribution; [AROME France HD](./arome-france-hd.md) exists only for the ARPEGE-initialized run.
- **Global drivers:** ECMWF [IFS](../../../nwp_models/global/eu/ifs.md) here, against [ARPEGE](../../global/france/arpege-global.md) for AROME France.
- **Overseas relative:** [AROME Outre-Mer](./arome-outre-mer.md) is also initialized from IFS (upper air) and ARPEGE (surface) without its own assimilation — the same design choice as AROME-IFS, applied to the overseas domains.
- **Ensemble relatives:** the metropolitan PEAROME is API-only; the only AROME ensemble on open data is [PE-AROME New Caledonia](../../../ensemble_models/regional/fr/pe-arome-ncaled.md).

---

## Recent version history
AROME-IFS is a parallel run of the AROME model and shares its upgrade history. For model changes — the October 2024 move to 3DEnVar (which applies to the AROME France chain, not this one), the 2015 resolution and vertical-level increase, and the December 2008 first implementation — see [AROME France](./arome-france.md).

The dataset first appeared on data.gouv.fr around **June 2026** (portal view statistics begin that month). **TBD** — the operational start date of the AROME-IFS chain itself is not stated in the open documentation.

---

## Official documentation
- data.gouv.fr dataset — Paquets Arome IFS, résolution 0,025° : https://www.data.gouv.fr/datasets/paquets-arome-ifs-resolution-0-025deg
- Modèles et données de prévision — AROME-IFS métropole (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- Météo-France open data portal: https://meteo.data.gouv.fr
- AROME package description (PDF, v. 01/04/2025): `descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf`, linked from the AROME dataset pages — describes the identical package structure, though it does not mention AROME-IFS
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

### Key references
- Seity et al. (2011), *The AROME-France Convective-Scale Operational Model*, Monthly Weather Review 139(3): 976–991.
- Brousseau et al. (2016), *Improvement of the forecast of convective activity from the AROME-France system*, Quarterly Journal of the Royal Meteorological Society 142(699): 2231–2243.

---

*Live verification performed 2026-08-10 against the 2026-08-09 18:00 UTC cycle (all eleven packages, `00H06H` files, 6,576 GRIB2 messages decoded with ecCodes 2.48.0), with supporting checks on all eight three-hourly slots of 2026-08-08 for the cycle schedule, on all four cycles for publication timing and volume, and on the 2026-07-25/26 boundary for retention.*
