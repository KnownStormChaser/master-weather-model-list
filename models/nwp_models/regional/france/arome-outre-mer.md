# AROME Outre-Mer

## What this model is
AROME Outre-Mer (AROME-OM) is the operational convection-permitting regional deterministic numerical weather prediction system run by Météo-France over France's overseas territories. It is **a single model configuration run on five separate regional domains** — the Antilles, French Guiana, New Caledonia, French Polynesia, and Réunion–Mayotte — rather than five distinct models. Météo-France documents it as one system ("LE MODELE AROME OUTRE-MER"), and this entry follows that structure: shared characteristics are described once, with per-domain specifics collected in the tables below.

Internally designated **PAROTRO** (AROME-Tropical), AROME-OM is designed for **short-range forecasting and nowcasting** of high-impact tropical and equatorial weather: deep convection, intense rainfall, thunderstorms, and local wind effects (sea breezes, terrain channelling). It shares the same dynamical core, physics packages, and vertical grid as [AROME France](./arome-france.md), but differs in two important respects described below: it has no data assimilation of its own, and its forecast range and output level set are smaller.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (overseas territories — Antilles, French Guiana, New Caledonia, French Polynesia, Réunion & Mayotte)

---

## How it is initialized
Unlike AROME France, **AROME-OM has no data assimilation of its own.** It is coupled for its initial conditions from two larger-scale models:

- **Upper-air initial conditions:** provided by **ECMWF's IFS** (~16 km), via dynamical downscaling with a prior "warmup" spin-up integration
- **Surface initial conditions:** provided by Météo-France's **[ARPEGE](../../global/france/arpege-global.md)**

This is a meaningful difference from AROME France, which runs its own 3DEnVar assimilation cycle. AROME-OM is effectively a high-resolution dynamical downscaling driven by IFS and ARPEGE, without an independent analysis.

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core); internal designation PAROTRO
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude, per domain
- **Vertical levels:** 90
- **Forecast length (live-verified):** **48 hours**, hourly — steps `000H` through `048H` (49 steps), identical on all five domains
- **Update frequency / cycles (live-verified):** **4× daily (00, 06, 12, 18 UTC) on all five domains**, including French Polynesia
- **Temporal output resolution:** Hourly throughout
- **Numerical precision:** Mixed precision
- **Ocean coupling:** 1D ocean model
- **Archive start:** 8 December 2015

> **Two corrections to earlier versions of this entry.**
> **Forecast length is 48 h, not 42 h.** The 42 h figure came from Météo-France's model-characteristics sheet (30/10/2025) and the AROME-OM brochure. The live distribution publishes 49 hourly steps through +48 h on every domain and cycle checked, and the package description (02/01/2024) and data.gouv.fr both say 48 h. The 42 h figure appears simply to be wrong.
> **French Polynesia runs 4× daily, not 2×.** All five domains published a complete 537-file set at each of 00, 06, 12 and 18 UTC on 2026-08-08; none published at 03, 09, 15 or 21 UTC.

---

## Domains

All five grids are **rectangular** `regular_ll` at 0.025°, north-to-south row order. Bounds below are live-verified from GRIB headers.

| Domain | Token | Grid | Points | Latitude | Longitude |
|---|---|---|---:|---|---|
| **Antilles** | `ANTIL` | 945 × 529 | 499,905 | 22.9°N – 9.7°N | 75.3°W – 51.7°W |
| **French Guiana** | `GUYANE` | 419 × 317 | 132,823 | 8.95°N – 1.05°N | 56.75°W – 46.3°W |
| **New Caledonia** | `NCALED` | 521 × 491 | 255,811 | 13.75°S – 26.0°S | 158.5°E – 171.5°E |
| **French Polynesia** | `POLYN` | 521 × 507 | 264,147 | 12.6°S – 25.25°S | 157.5°W – 144.5°W |
| **Réunion–Mayotte** | `INDIEN` | 1395 × 899 | 1,254,105 | **3.45°S** – 25.9°S | 32.75°E – 67.6°E |

Longitudes are encoded on a 0–360° axis (e.g. Antilles runs 284.7°E → 308.3°E); the values above are converted for readability. None of the five domains crosses the antimeridian or the prime meridian, so the first longitude is always numerically smaller than the last — unlike the metropolitan AROME and ARPEGE Europe grids.

> **No missing values.** Unlike metropolitan [AROME France](./arome-france.md), where the trapezoidal native grid leaves 17.2% of every field masked, the AROME-OM domains carry **`bitmapPresent = 0`** and no fill values. Verified across three domains (Antilles IP3, Réunion–Mayotte HP1, Guiana SP1) — zero points above the 9999 sentinel in fields of 499,905, 1,254,105 and 132,823 points respectively. The masking guidance that applies to metropolitan AROME does **not** apply here.

### Domain-bounds conflicts in the documentation
Two of the five domains are documented incorrectly, and the conflicts are not the ones previously flagged in this entry:

- **New Caledonia** — the package description (02/01/2024) gives 10°S–30°S, 156°E–174°E. Live headers give **13.75°S–26.0°S, 158.5°E–171.5°E**, matching the newer model-characteristics sheet (30/10/2025). *This resolves the conflict previously flagged here: the characteristics sheet is right and the package description is stale.*
- **Réunion–Mayotte** — both the package description **and** the data.gouv.fr dataset page give the northern edge as 7.25°S. The live grid starts at **3.45°S** and the arithmetic confirms it (899 rows × 0.025° = 22.45° span, ending at 25.9°S). The operational domain extends roughly 3.8° further north than any published source states. **TBD** — worth raising with Météo-France.
- **Antilles** — the package description gives 22.45°N–10.4°N, 67.8°W–52.2°W; the data.gouv.fr page gives 22.9°N–9.7°N, 75.3°W–51.7°W. **The dataset page is correct**; the package description is stale.
- Guiana and Polynesia agree across all sources and match the live headers.

---

## What it provides

AROME-OM uses **one file per package per forecast hour**, not range groups — the same organisation as [AROME France HD](./arome-france-hd.md) and unlike metropolitan [AROME France](./arome-france.md). Per domain per cycle: **11 packages × 49 steps = 537 files**, less two because HP3 and IP4 have no step-0 file (see below), giving **537 files** exactly.

### Package inventory (live-verified, Antilles, 2026-08-09 12 UTC)

| Package | Level type | Parameters |
|---|---|---|
| **SP1** | surface, 2 m, 10 m | `prmsl`, `2t`, `2r`, `10u`, `10v`, `10si`, `10wdir`, `max_i10fg`, `max_10efg`, `max_10nfg`, total cloud cover, `tp`, `tsnowp`, `tgrp`, `ssrd` |
| **SP2** | surface, 2 m | `sp`, surface `t`, `2d`, `2sh`, `lcc`, `mcc`, `hcc`, `CAPE_INS`, `blh`, `tirf`, `min_2t`, `max_2t` |
| **SP3** | surface | total column water vapour, evaporation, `slhf`, `sshf`, `strd`, `ssr`, `str`, `ssrc`, `strc`, `iews`, `inss`, synthetic satellite brightness temperature (2 channels) |
| **HP1** | height above ground (12) | `t`, `r`, `u`, `v`, `ws`, `wdir`, `pres`, `z` |
| **HP2** | height above ground (12) | `dpt`, `q`, `clwc`, `crwc`, `cswc`, `ciwc`, `cc`, + 1 unresolved |
| **HP3** | height above ground (7) | radar reflectivity (two distinct fields) |
| **IP1** | isobaric (19) | `t`, `r`, `u`, `v`, `z` |
| **IP2** | isobaric (19) | `clwc`, `crwc`, `cswc`, `ciwc`, `cc` |
| **IP3** | isobaric (19) | `dpt`, `q`, `ws`, `wdir`, `w`, `wz`, `pv` |
| **IP4** | isobaric (10 / 5) | `tke` (10 levels); radar reflectivity (5 levels) |
| **IP5** | isobaric (16 / 5) + potential vorticity | `papt` (16 levels); `absv`, `vo`, `d` (5 levels each); `u`, `v`, `z` on PV surfaces |

### Level sets (live-verified)
- **Isobaric — 19 levels** (IP1, IP2, IP3): 100, 150, 175, 200, 225, 250, 275, 300, 350, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — `tke` (IP4), 10 levels:** 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — reflectivity (IP4), 5 levels:** 700, 800, 850, 900, 925 hPa
- **Isobaric — `papt` (IP5), 16 levels:** 200 through 1000 hPa
- **Isobaric — `absv`/`vo` (IP5), 5 levels:** 300, 500, 600, 700, 850 hPa
- **Isobaric — `d` divergence (IP5), 5 levels:** 200, 300, 700, 925, 950 hPa
- **Height above ground — 12 levels** (HP1, HP2): 20, 50, 100, 250, 500, 750, 1000, 1250, 1500, 2000, 2500, 3000 m
- **Height above ground — reflectivity (HP3), 7 levels:** 500, 750, 1000, 1500, 2000, 2500, 3000 m
- **Potential vorticity (IP5): two surfaces at 0.7 and 1.5 PVU** (encoded 700, 1500)

> **The height level set is 12, not 14.** Both the package description and earlier versions of this entry state 14 levels spanning 20–3000 m. The live files carry **12**, and there are no 2 m or 10 m entries in the HP packages — those live in SP1/SP2. Similarly the isobaric count is 19, which is correct in all sources.

### Step 0 is a reduced set
As on the metropolitan grids, several fields are absent at step 0 because they are hour-interval quantities or start at step 1:

| Package | Step 0 | Steps 1–48 |
|---|---|---|
| **SP1** | 7 fields | 15 — adds gusts, precipitation, total cloud cover, `ssrd` |
| **SP2** | 6 fields | 12 — adds cloud layers, `tirf`, 2 m extremes |
| **SP3** | 2 fields — column water vapour + brightness temperature | 12 — adds all fluxes |
| **HP3, IP4** | **no file at all** | present |

**HP3 and IP4 have no `000H` file.** Both are the radar-reflectivity packages, and reflectivity is not published at step 0. Code that assumes 49 files per package will fail on these two; the correct count is 48. This is also why a `000H` search on the data.gouv.fr dataset pages returns nine packages rather than eleven.

### Per-domain synthetic satellite channels
SP3 carries **synthetic (simulated) satellite brightness temperatures** under `productDefinitionTemplateNumber = 32`, two channels per domain — and **the channels differ by domain**, each approximating the geostationary satellite that actually views that region:

| Domain | Infrared window | Water vapour |
|---|---|---|
| Antilles, Guiana, New Caledonia | 10.40 µm | 6.20 µm |
| French Polynesia | 10.70 µm | 6.70 µm |
| Réunion–Mayotte | 10.80 µm | 6.20 µm |

As with every other Météo-France synthetic satellite product, `satelliteSeries`, `satelliteNumber` and `instrumentType` are **all zero**, so the emulated platform cannot be read from the file — only the central wavenumber distinguishes the channels. The package description labels these by internal channel number (62/104, 67/107, 62/108 respectively).

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** All five dataset pages carry the **HVD badge** ("Données de forte valeur") under the EU Open Data Directive
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing)

### Primary access — data.gouv.fr object storage (no authentication)

Served from OVH-hosted S3-compatible object storage. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/arome-om/{DOMAIN}/0025/{package}/arome-om-{DOMAIN}__0025__{package}__{step}H__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T12:00:00Z`), `{DOMAIN}` is one of `ANTIL GUYANE NCALED POLYN INDIEN`, `{package}` is one of `SP1 SP2 SP3 HP1 HP2 HP3 IP1 IP2 IP3 IP4 IP5`, and `{step}` is a **zero-padded three-digit forecast hour** from `000` to `048`.

  Note that the domain token appears **twice** — once as a path segment and once inside the filename — and that the step is three digits here, against two on the metropolitan HD grid.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T12:00:00Z/arome-om/ANTIL/0025/HP1/arome-om-ANTIL__0025__HP1__000H__2026-08-09T12:00:00Z.grib2
  ```

- **Dataset landing pages:**
  - Antilles: https://www.data.gouv.fr/datasets/paquets-arome-outre-mer-antilles-resolution-0-025deg
  - French Guiana: https://www.data.gouv.fr/datasets/paquets-arome-outre-mer-guyane-resolution-0-025deg-1
  - New Caledonia: https://www.data.gouv.fr/datasets/paquets-arome-outre-mer-nouvelle-caledonie-resolution-0-025deg
  - French Polynesia: https://www.data.gouv.fr/datasets/paquets-arome-outre-mer-polynesie-resolution-0-025deg
  - Réunion–Mayotte: https://www.data.gouv.fr/datasets/paquets-arome-outre-mer-reunion-mayotte-resolution-0-025deg

- **The bucket is anonymously listable:**
  ```bash
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T12:00:00Z/arome-om/ANTIL/&max-keys=1000"
  ```
  Paginate via `NextContinuationToken` (URL-encode it). Objects serve `Content-Length` and `Last-Modified` on `HEAD` and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling**, shared with every other model in the bucket.

- **Publication latency (live-verified):** measured on the 2026-08-08 00 and 12 UTC cycles. **AROME-OM lands far later than the metropolitan products, and the tail is long and erratic:**

  | Domain | 00 UTC first / last | 12 UTC first / last |
  |---|---|---|
  | Antilles | T+6 h 38 m / **T+10 h 24 m** | T+6 h 33 m / T+7 h 23 m |
  | French Guiana | T+6 h 35 m / T+7 h 20 m | T+6 h 29 m / T+7 h 14 m |
  | New Caledonia | T+6 h 35 m / T+7 h 23 m | T+6 h 29 m / **T+10 h 09 m** |
  | French Polynesia | T+6 h 35 m / **T+9 h 54 m** | T+6 h 29 m / **T+9 h 59 m** |
  | Réunion–Mayotte | T+6 h 44 m / **T+10 h 18 m** | T+6 h 38 m / T+7 h 59 m |

  First files consistently appear around **T+6 h 30 m**, but completion ranges from **T+7 h 14 m to T+10 h 24 m** and the slow cycles are not the same domains from one run to the next. Météo-France's published delivery times (00→07:30, 06→13:30, 12→19:30, 18→01:30, i.e. ~T+7 h 30 m) describe the typical case but not the tail. Poll for specific keys rather than assuming completion by a fixed offset.

- **Volume (live-verified, 2026-08-09 12 UTC):** **~44.8 GB per cycle across all five domains**, ~179 GB/day, roughly **2.7 TB** resident under the 15-day window.

  | Domain | Per cycle | Largest file |
  |---|---:|---:|
  | Réunion–Mayotte | 23.25 GB | 146.5 MB |
  | Antilles | 9.29 GB | 59.6 MB |
  | French Polynesia | 5.17 GB | 33.6 MB |
  | New Caledonia | 4.51 GB | 31.3 MB |
  | French Guiana | 2.59 GB | 16.9 MB |

  Réunion–Mayotte alone is over half the total, being by far the largest grid. Individual files are small throughout (0.12–146 MB), a direct consequence of the per-hour layout — convenient for step-targeted fetching.

### Companion documentation
- **`description-paquets-modele-aromeom.pdf`** — official package description covering all five domains, **version 02/01/2024**. See *Documentation defects* below.
- **`description-parametres-modeles-arpege-arome-v2-185.pdf`** — the ARPEGE/AROME parameter glossary, linked from the metropolitan datasets, applies to AROME-OM parameter names as well.

No constant-fields file is published for the AROME-OM domains, unlike the metropolitan and ARPEGE grids. Orography is not carried in any package either (metropolitan SP2 publishes `h`; AROME-OM SP2 does not), so **no terrain or land–sea mask is available through this distribution**. **TBD** — whether Météo-France publishes these elsewhere.

### Secondary access — Météo-France API portal

Météo-France also exposes AROME-OM through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 files with no authentication and no key rotation, and is the recommended access path. *(This supersedes the earlier note in this entry flagging the API zone tokens as unverified — the object-storage domain tokens are `ANTIL`, `GUYANE`, `NCALED`, `POLYN`, `INDIEN`, live-confirmed.)*

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw` (Météo-France Toulouse), `subCentre = 0`, `typeOfGeneratingProcess = 2` (forecast, including step 0).
- **`generatingProcessIdentifier = 121`** — distinct from metropolitan AROME's 204 and ARPEGE's 211. A reliable way to identify AROME-OM messages in a mixed archive.
- **Packing is `grid_ccsds`** (CCSDS/AEC) throughout.
- **`bitmapPresent = 0`** — no missing-value masking on any domain (see *Domains* above).

### Accumulation convention
Time-processed fields accumulate **from run start** (`0-1`, `0-2`, …), as on the metropolitan 0.025° grid. Hourly totals require differencing successive messages.

### Parameters that do not decode
| Encoding | Package | Identification | Basis |
|---|---|---|---|
| 0/6/1 | SP1 | **Total cloud cover** (`NEBUL`) | Package description + glossary; WMO-standard encoding ecCodes leaves unresolved |
| 0/1/64 | SP3 | **Total column water vapour** (`COLONNE_VAPO`) | Package description + glossary |
| 0/1/6 | SP3 | **Evaporation** (`FLEVAP`), accumulated | Package description + glossary |
| 0/16/192 | IP4, HP3 | **Derived radar reflectivity** (`RFLCTVT`) | Package description + glossary |
| 0/5/7, PDT 32 | SP3 | **Synthetic satellite brightness temperature** (`BT`) | Package description |
| 0/1/201 | HP2 | **Undocumented — TBD** | Not in the package description |

On 0/1/201: the same undocumented parameter appears in metropolitan AROME's IP2, where the evidence points to **specific graupel content**. Here it sits in HP2 alongside liquid, rain, snow and ice species plus cloud fraction — again with graupel the conspicuous absence. The inference is the same and remains unconfirmed by Météo-France.

`CAPE_INS` also has no resolvable level type, as elsewhere in the Météo-France distributions.

### HP3 carries two distinct reflectivity fields
The package description lists a single `RFLCTVT` field in HP3. The live files carry **two** on the same seven height levels: the local-table 0/16/192 (undecoded) and **`rare`** (0/16/4, equivalent radar reflectivity factor for rain, in dB) — the latter a WMO-standard encoding that ecCodes resolves normally. Metropolitan AROME's HP3 carries only the first. Users wanting a decodable reflectivity field on the overseas domains should prefer `rare`.

### Divergence is published but undocumented
IP5 carries **`d`** (divergence, 0/2/13) on five isobaric levels (200, 300, 700, 925, 950 hPa). The package description does not list it, and metropolitan AROME's IP5 does not contain it. It is unique to the overseas domains among the AROME distributions.

### Documentation defects
`description-paquets-modele-aromeom.pdf` (version 02/01/2024) has drifted from the live data in several places:

- **"8 réseaux : 0 UTC, 06 UTC, 12 UTC et 18 UTC"** — says eight cycles, lists four. Live is four.
- **New Caledonia and Antilles bounds are stale** (see *Domains* above); the Réunion–Mayotte northern edge is wrong in every source including the live-correct dataset pages.
- **Height levels given as 14; live files carry 12.**
- **IP4 level ranges are wrong**: `tke` is described as 10 levels spanning 100–1000 hPa but actually spans **400–1000 hPa**; reflectivity is described as 5 levels spanning 200–925 hPa but actually spans **700–925 hPa**. The counts are right, the ranges are not.
- **IP5 PV surfaces are given as ISO_TP 2000 and 1500; live files carry 700 and 1500** (0.7 and 1.5 PVU). Metropolitan AROME uses 1500/2000, so the PDF appears to have copied the metropolitan values.
- **HP2 is listed as carrying TKE; it does not.** The live HP2 has no `tke` at all (it is in IP4 only) but does carry the undocumented 0/1/201.
- **HP3 is listed with one reflectivity field; live files have two.**
- **IP5's divergence field is absent from the listing.**
- SP3's parameter list contains `FLSOLAIRE_D` twice where `FLSOLAIRE` is presumably intended.

The PDF is correct on the cycle hours, forecast length, hourly step, packing format, per-hour file organisation, the 19-level isobaric set, the 7-level HP3 set, and the SP1/SP2/SP3/IP1/IP2/IP3 parameter lists.

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host resolves and recognises the bucket name but returns `NoSuchKey` for every object. The five `meteo.data.gouv.fr/datasets/{id}` landing pages previously listed here are superseded by the `www.data.gouv.fr` slugs above.
- **The community AWS mirror is gone.** `mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket` and `mf-models-on-aws.org` no longer resolves; only the stale [AWS Open Data Registry listing](https://registry.opendata.aws/meteo-france-models/) remains.

### How AROME-OM differs from metropolitan AROME
| | [AROME France](./arome-france.md) 0.025° | AROME-OM 0.025° |
|---|---|---|
| Data assimilation | 3DEnVar, 1 h cycle | none (IFS + ARPEGE downscaling) |
| Forecast length | 51 h | 48 h |
| Cycles | 8× daily | 4× daily |
| File organisation | 9 range groups | 49 hourly files |
| Isobaric levels | 24 | 19 |
| Height levels | 25 (10–3000 m) | 12 (20–3000 m) |
| Missing values | 17.2% masked (trapezoidal) | none (rectangular) |
| `generatingProcessIdentifier` | 204 | 121 |
| Divergence in IP5 | no | yes |
| Second HP3 reflectivity field | no | yes |

---

## Companion ensemble system

**PEAROME-OM** (Prévision d'Ensemble AROME Outre-Mer) is the ensemble counterpart, operational since 21 February 2023. It comprises 16 members (1 control + 15 perturbed), perturbing initial, boundary, surface, and model conditions. Key differences from the deterministic AROME-OM:

- **Native resolution:** 2.5 km (vs. 1.3 km deterministic)
- **Vertical levels:** 90 (same as deterministic)
- **Lateral boundary conditions:** supplied by ARPEGE's ensemble **PEARP** (15 selected members couple the perturbed members; the control couples to deterministic ARPEGE), refreshed every 3 hours
- **Forecast length:** 48 hours, extended to **78 hours when a tropical cyclone is present** in the domain
- **Cycles (differ from deterministic):** Antilles and Guiana at 00/12 UTC; Réunion at 00/18 UTC; New Caledonia and Polynesia at 06/18 UTC

PEAROME-OM covers all five domains and is documented separately under ensemble models. **It is not present in the `arome-om/` tree of the open-data bucket** — only the deterministic `0025` grids appear per domain — so its open-data availability is **TBD**.

---

## Related systems
- **[AROME France](./arome-france.md)** — the metropolitan configuration, with its own 3DEnVar assimilation, longer range, faster cadence, more output levels, and additional diagnostics. See the comparison table above.
- **[AROME France HD](./arome-france-hd.md)** — the 0.01° metropolitan distribution, which shares AROME-OM's per-hour file organisation.
- **[ARPEGE](../../global/france/arpege-global.md)** — provides AROME-OM's surface initial conditions (and, via PEARP, the ensemble's lateral boundary conditions).

---

## Official documentation
- data.gouv.fr dataset pages — five domains, listed under *Data availability* above
- Météo-France open data portal: https://meteo.data.gouv.fr
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- AROME-OM package description (PDF, v. 02/01/2024): `description-paquets-modele-aromeom.pdf`, linked from the dataset pages above
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`
- Météo-France local GRIB definitions (for local parameter numbers): linked from the Confluence page above
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-09 against the 2026-08-09 12:00 UTC cycle (all five domains enumerated; all eleven Antilles packages decoded at steps 000H and 001H; grid headers and synthetic-satellite channels checked on all five domains; ecCodes 2.48.0), with supporting checks on the 2026-08-08 00/03/06/09/12/15/18/21 UTC cycles for the cycle schedule, on the 00 and 12 UTC cycles for publication timing and volume, and on the 2026-07-25/26 boundary for retention.*
