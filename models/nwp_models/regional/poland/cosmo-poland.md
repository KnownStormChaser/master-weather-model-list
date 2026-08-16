# COSMO (IMGW-PIB, Poland)

## What this model is
COSMO is the convection-permitting regional deterministic limited-area model run operationally by IMGW-PIB over Poland and neighbouring Central Europe at 2.8 km. Since June 2020 the operational 2.8 km configuration has used the **EULAG** dynamical core (COSMO-EULAG, referred to by IMGW as **COSMO-CE PL**) in place of the earlier Runge–Kutta core, built on COSMO model version 5.05. It is nested in IMGW's coarser 7 km COSMO PL 7 run, which supplies its lateral boundary conditions and is itself driven by DWD's global ICON.

The full model output is published as GRIB1 through IMGW-PIB's public data portal under the product family `COSMO_HVD_*`. Unlike the [ALARO](./alaro-poland.md) and [AROME](./arome-poland.md) public products, **this feed is not regridded, not cropped and not downsampled in time**: it is delivered on the native rotated grid, at full domain size, on all 50 model levels, hourly to the full operational range.

IMGW-PIB has stated (June 2026) that it is moving its public model data from COSMO to ICON-LAM. **No transition date has been announced**, and the COSMO feed remains live and complete as of the verification date below. This entry documents the feed as it currently exists; it is expected to be superseded by an ICON-LAM entry when that product appears.

> **Live-verified 2026-08-16** against the `COSMO_HVD_*` datastore using ecCodes 2.48.0 on the 2026-08-15 06 UTC cycle (`+000` and `+012` from both output streams, plus the constant file), together with HTTP probing of the full step and cycle space and the portal's own `getFilesList` / `getProductList` endpoints. Fields marked **(verified)** were read from the GRIB headers or from live server responses rather than taken from documentation. The `readme.txt` shipped with the data contradicts the files on several points — see *Decoding notes*.

---

## Who runs it
- **Organization:** Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy (IMGW-PIB), Department of Numerical Weather Prediction – COSMO, Warsaw
- **Country / region:** Poland

---

## What area it covers
- **Coverage:** Poland and surrounding Central Europe — eastern Germany, Czechia, Slovakia, western Ukraine and Belarus, the Baltic coast, and the northern Alps at the southern edge

### Grid **(verified)**
The distributed files carry the model's **native rotated latitude/longitude grid**. There is no interpolation step and no crop:

- `gridType = rotated_ll` (GRIB1 data representation type 10)
- **380 × 405 = 153,900 points**, identical on every message of every file
- Rotated coordinates: first point **−2.400° / 0.663°**, last point **7.700° / 10.138°**
- **Southern pole at 40.000° S / 10.000° E**, `angleOfRotationInDegrees = 0` — i.e. north pole at **40.0° N / −170.0° E**, matching the "rotated: NP −170.0, 40.0" given in the IMGW poster series
- Grid spacing **0.025° × 0.025° ≈ 2.78 km**, uniform in the rotated frame
- `scanningMode = 64` (j scans positively, i positive)
- **Flag — increments are encoded as zero.** `iDirectionIncrement` and `jDirectionIncrement` are both 0 in section 2, i.e. "not given". Software that reads them literally will produce a degenerate grid; the spacing must be derived from the first/last corner and Ni/Nj.

### Geographic extent **(verified from the `RLAT` / `RLON` constant fields)**
The rotated quadrilateral's corners in true coordinates:

| Corner | Latitude | Longitude |
|---|---|---|
| First grid point | 47.596 N | 10.963 E |
| End of first row | 46.597 N | 24.810 E |
| Start of last row | 57.695 N | 11.205 E |
| Last grid point | 56.454 N | 28.376 E |

Bounding box ≈ **46.60–57.70 N, 10.96–28.38 E**. Model orography (`HSURF`) spans **−2.02 m to 2001.10 m** — the Tatra maximum, consistent with a domain that clips the Alps rather than containing them.

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area), convection-permitting
- **Model system / core:** COSMO 5.05 with the **EULAG** compressible semi-implicit dynamical core (COSMO-EULAG / "COSMO-CE PL"). **Flag:** the portal never states which dynamical core the public product comes from, and `generatingProcessIdentifier = 132` does not distinguish cores. The COSMO-CE identification rests on the IMGW poster series, which records the June 2020 switch and lists COSMO-CE PL as the only operational deterministic 2.8 km run since.
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (2.8 km; deep convection explicitly resolved). Note that the files nevertheless carry convective diagnostics — convective cloud cover, convective rain and snow, convective cloud base/top, CAPE_CON — so a shallow-convection scheme is active and the mass-flux diagnostics remain populated. See *What it provides*.
- **Horizontal resolution:** 2.8 km (native, and as distributed — no regridding)
- **Grid dimensions:** **380 × 405 (verified)**
- **Vertical levels:** **50 model layers / 51 half levels (verified)**. Terrain-following height-based coordinate; the `pv` array gives reference-atmosphere parameters `p0sl = 100000 Pa`, `t0sl = 288.15 K`, `dt0lp = 42`, `vcflat = 11357 m`, and `pv[0] = 102` which on COSMO's convention (`ivctype + 100 · irefatm`) decodes to `irefatm = 1`, `ivctype = 2` — Gal-Chen-type height coordinate with reference atmosphere 1. *(Decode inferred from the COSMO convention, not from IMGW documentation — flag.)*
- **Model top:** **22 km (verified** — half-level 1 is a constant 22000.0 m; the topmost layer's mean pressure at +12 h was 46.8 hPa**)**
- **Forecast length:** **60 h (verified** — steps `+000` through `+060` all resolve; `+061` does not**)**
- **Update frequency / cycles:** **4× daily (00, 06, 12, 18 UTC) — verified.** Four consecutive cycles were simultaneously resident on 2026-08-16.
- **Temporal output resolution:** **Hourly (verified** — 61 files per cycle per stream, no gaps**)**
- **Time step:** 20 s

---

## Data assimilation
- **Data assimilation:** Yes — **nudging** (Newtonian relaxation), per the IMGW poster series, which lists a nudging assimilation scheme for COSMO-CE PL in every year sampled. The GRIB headers carry no assimilation indicator, so the files can neither confirm nor contradict this.
- **Related but separate:** IMGW also runs a **COSMO-RUC** rapid-update configuration at 2.8 km with latent heat nudging of radar precipitation composites, feeding the SCENE / HAIL / SPT nowcasting systems at 10-minute resolution. That output is not part of this public feed.

---

## Initial and boundary conditions
- **Initial conditions:** Own nudging analysis on the 2.8 km domain
- **Boundary conditions:** LBC from **COSMO PL 7** (7 km, 415 × 445, 40 levels, ~+86 h range), **1 h coupling interval**. COSMO PL 7 is in turn driven by **ICON Global** (DWD) with a 3 h LBC update interval. COSMO PL 7 output is **not** published on the public portal.

---

## What it provides

The product is split into **two output streams per cycle**, exposed on the portal as eight separate entries — four cycle hours × two streams. They are the `00` and `01` GRIBOUT groups of the same model run, carry **disjoint** field sets, and cover the same 61 hourly steps. Both are needed for a complete picture: cloud water is in `/00`, cloud ice in `/01`; stability indices are in `/01` only.

### Stream `/00` — **523 messages per file (verified)**

**Model-level fields (3D)** — eight parameters on 50 hybrid layers plus one on 51 half levels:

| Parameter | Table.code | shortName | Levels | Units |
|---|---|---|---|---|
| U wind | 2.33 | `u` | 50 layers | m s⁻¹ |
| V wind | 2.34 | `v` | 50 layers | m s⁻¹ |
| **Vertical velocity (geometric)** | 2.40 | `W` | **51 half levels** | m s⁻¹ |
| Temperature | 2.11 | `t` | 50 layers | K |
| Specific humidity | 2.51 | `q` | 50 layers | kg kg⁻¹ |
| Relative humidity | 2.52 | `r` | 50 layers | % |
| Cloud water mixing ratio | 201.31 | `QC` | 50 layers | kg kg⁻¹ |
| Pressure | 2.1 | `pres` | 50 layers | Pa |
| Cloud cover (grid-scale + convective) | 201.29 | `CLC` | 50 layers | % |

**Level ordering is top-down**: level 1 is the topmost layer (~47 hPa), level 50 the lowest. `W` is on layer interfaces, so its 51 messages are not co-located with the 50-layer fields.

**Soil (25 fields):** `T_SO` on 9 depths (0, 1, 2, 6, 18, 54, 162, 486, 1458 cm), `W_SO` and `W_SO_ICE` on 8 depths each (1 cm downward), plus `T_S` (0 cm) and surface/subsurface runoff `RUNOFF_S` at 0 and 10 cm.

**Single-level fields (45):** surface pressure, MSLP, 2 m temperature / dew point / relative humidity, 10 m wind components, 1-hour min/max 2 m temperature, 1-hour maximum 10 m wind, skin temperature `T_G`, surface specific humidity, snow temperature, canopy water, snow density, fresh-snow factor, total / high / medium / low cloud cover, albedo, climatological roughness length, turbulent transfer coefficients for momentum and heat, convective cloud base and top heights, dry-convection top height, and two supercell detection indices (`SDI_1`, `SDI_2`).

**Precipitation (accumulated from run start):** `RAIN_GSP` (grid-scale rain), `lssf` (grid-scale snow), `RAIN_CON` (convective rain), `snoc` (convective snow), and `tp` (total precipitation). The mechanism × phase split *and* the total are both present, so no summation is required.

**Radiation and fluxes (averaged from run start):** `ASOB_S`, `ATHB_S`, `ASOB_T`, `ATHB_T`, `APAB_S`, `AUMFL_S`, `AVMFL_S`, `ASHFL_S`, `ALHFL_S`.

### Stream `/01` — **120 messages per file (verified)**

**Model-level fields (3D):** cloud ice mixing ratio `QI` (201.33) and convective cloud cover `ccc` (2.72), each on 50 hybrid layers.

**Single-level diagnostics (20):**

| Field | Table.code | Notes |
|---|---|---|
| `CAPE_MU`, `CIN_MU` | 201.143 / 201.144 | most-unstable parcel |
| `CAPE_ML`, `CIN_ML` | 201.145 / 201.146 | mean-layer parcel |
| `CAPE_CON` | 201.241 | convection-scheme CAPE |
| `HZEROCL` | 201.84 | 0 °C isotherm height above MSL |
| `SNOWLMT` | 201.85 | snowfall limit height above MSL |
| `CEILING` | 203.157 | cloud ceiling above MSL |
| `CLDEPTH` | 203.203 | normalised cloud depth |
| `cwat` | 2.76 | vertically integrated cloud water (TQC) |
| `BAS_CON`, `TOP_CON` | 201.72 / 201.73 | convective cloud base/top **level indices**, not heights |
| `HBAS_SC`, `HTOP_SC` | 201.58 / 201.59 | shallow-convection cloud base/top height |
| `TDIV_HUM` | 201.42 | vertically integrated total-water divergence (accumulated) |
| `sde` | 2.66 | snow depth |
| `snom` | 2.99 | snowmelt (accumulated) |
| `icetk` | 2.92 | ice thickness |
| *(unnamed)* | 201.218 / 201.219 | 1-hour maximum gusts — see *Decoding notes* |

### Constant file — **64 messages (verified)**
Each cycle directory also contains `..._lfff00000000c`: 51 `HHL` half-level geometric heights, `HSURF`, `FIS`, Coriolis parameter, `RLAT`, `RLON`, land–sea mask, soil type, climatological ozone (`VIO3`, `HMO3`), vegetation fraction, leaf area index, root depth, and sub-grid orography standard deviation `SSO_STDH`. **The constant file is byte-identical between the `/00` and `/01` directories** (same MD5), so only one copy need be fetched.

### Not present
No pressure-level output of any kind, and no precipitating hydrometeor mixing ratios on model levels (`QR`, `QS`, `QG`). Users needing isobaric fields must interpolate from the model levels themselves — a real cost, but the trade for getting the full 3D state rather than a six-level pressure subset.

---

## Data availability
- **Is the data free?** Yes
- **License:** No named license — identical arrangement to [ALARO](./alaro-poland.md) and [AROME](./arome-poland.md). The governing document is the portal's **Regulamin udostępniania danych**, grounded in the Polish Act of 11 August 2021 on open data and re-use of public sector information (Dz. U. 2023 poz. 1524) and in **Commission Implementing Regulation (EU) 2023/138** on high-value datasets. §5 permits use of free data for private purposes and, for high-value data, **for any purpose** — matching IMGW-PIB's statement in correspondence WWS.381.295.2026/AKS (June 2026) that the model data are HVD and may be used "in any way." The product path itself (`COSMO_HVD_*`) carries the HVD designation.

  **Attribution is mandatory and the wording is prescribed:** *"Źródłem pochodzenia danych jest Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy"*, plus, where the data have been processed, *"Dane Instytutu Meteorologii i Gospodarki Wodnej – Państwowego Instytutu Badawczego zostały przetworzone"*.
- **Is the data downloadable?** Yes
- **Data formats:** **GRIB edition 1 (verified)** — not GRIB2. Uniform `packingType = grid_simple`, `bitsPerValue = 16` on every message including the constant fields, which is why file sizes are fixed rather than varying with step:

  | File | Size |
  |---|---|
  | `/00` forecast file | 161,171,864 B |
  | `/01` forecast file | 36,980,160 B |
  | constant file | 19,722,752 B |

  **Flag — originating centre is DWD, not IMGW.** Section 1 carries `centre = 78` (Offenbach), `subCentre = 255`, `generatingProcessIdentifier = 132`, and local tables **201, 202 and 203**. Anyone filtering an archive by originating centre will not find these under a Polish code. Note this differs from ALARO/AROME, which carry Météo-France's centre 84 — the two IMGW model families are distinguishable by centre code, but neither is attributed to IMGW.

  **Flag — no file extension.** Files are named `<run>_<valid>_lfff<DDHHMMSS>`, e.g. `202608150600_202608171800_lfff02120000` for the +60 h step of the 2026-08-15 06 UTC cycle. Both timestamps are `YYYYMMDDHHMM`; the `lfff` suffix is COSMO's native output naming, with the forecast lead encoded as days/hours/minutes/seconds. The constant file appends a literal `c`.
- **Official download location:**  
  https://danepubliczne.imgw.pl/pl/datastore?product=COSMO+HVD+00%2F00 (one product page per cycle × stream)
- **Direct file URL pattern (verified):**  
  `https://danepubliczne.imgw.pl/{pl|en}/datastore/getfiledown/Oper/COSMO/COSMO_HVD_<HH>/<NN>/<run>_<valid>_lfff<DDHHMMSS>`  
  where `<HH>` ∈ {00, 06, 12, 18} is the cycle hour and `<NN>` ∈ {00, 01} the output stream.
- **Alternative host (verified):** the same files are served from **`https://dane.imgw.pl/datastore/getfiledown/Oper/COSMO/...`** with no language prefix. `dane.imgw.pl` exposes an identical 100-product catalogue and appears to be the successor to `danepubliczne.imgw.pl`; the legacy portal now shows a banner directing users to it. Both hosts work today — worth watching, since a future retirement of `danepubliczne.imgw.pl` would break URL patterns recorded in this and the ALARO/AROME entries.
- **Directory listing endpoint (verified):** the portal's own AJAX endpoints give an authoritative listing without HTML scraping of the front page:  
  `POST /{pl|en}/datastore/getFilesList` with `productType=oper` and `path=/Oper/COSMO/COSMO_HVD_06/00`, and `POST /{pl|en}/datastore/getProductList` with `productType=oper|arch`. Both return HTML fragments. A COSMO product directory returns exactly **63 entries**: 61 forecast files, the constant file, and `readme.txt`.

---

## Decoding notes

- **The shipped `readme.txt` describes a different model configuration.** The file served in every COSMO product directory (identical MD5 across all of them) is headed "IMGW-PIB, COSMO 2k8 numerical model forecast", but its grid block gives **415 × 460 × 40 points at 0.0625°** with the pole at 32.5 N / −170 E and origin 57.5 N / 10 E. The actual files are **380 × 405 × 50 at 0.025°** with the pole at 40 N / −170 E. A 0.0625° rotated grid of 415 columns is the **7 km COSMO PL 7 domain**, not the 2.8 km one — the readme's geometry section appears to have been carried over from the older product and never updated. Its cycle list (00/06/12/18), forecast length (60 h), step (1 h) and format (GRIB1) are correct.
- **Further readme discrepancies:** the `00` data set list omits `RAIN_CON` and `SNOW_CON`, which the files do carry; the constant-field list has 63 entries where the file has 64 (`SSO_STDH` is present but undocumented); and the record numbers given throughout do not match the actual message ordering. Treat the readme as a rough field guide only.
- **Codes 201.218 and 201.219 are unknown to ecCodes** and decode as `unknown` with `units = unknown`, at 10 m above ground with a 1-hour statistical window. **201.218 is bit-identical to `VMAX_10M` (201.187) in stream `/00`** (same min, max and mean to full precision) and **201.219 is identically zero across the domain**. COSMO splits its gust diagnostic into a dynamical (turbulent) and a convective component, with `VMAX_10M` being the combination of the two; the readme names `VGUST_DYN` in the `01` set. The consistent reading is therefore **218 = `VGUST_DYN`, 219 = `VGUST_CON`**, with the convective component unpopulated in the sampled case. *(Inferred from the data plus COSMO release notes — IMGW publishes no code table. Re-check 219 during a convective episode before treating the zero as structural rather than seasonal.)*
- **Statistical processing follows COSMO's usual conventions, but two of them bite:**
  - Accumulations (`timeRangeIndicator = 4`) and averages (`timeRangeIndicator = 3`) run **from the start of the forecast**, giving ranges `0-1`, `0-12`, `0-60`. Consumers wanting per-hour values must difference consecutive files.
  - Min/max 2 m temperature and the gust fields (`timeRangeIndicator = 2`) use a **1-hour** window (`11-12` at the +12 h step). **ecCodes names these `mn2t6` / `mx2t6`** — "in the last 6 hours" — which is simply wrong for this feed. Read `P1`/`P2`, not the shortName.
  - At step 0 all statistically processed fields carry `timeRangeIndicator = 0` and zero-length ranges.
- **`CEILING` uses a fill value, not a missing-value flag.** Where there is no ceiling the field takes model top + orography (domain maximum 22000.19 m at +12 h). Any statistic computed over the raw field without masking values near 22000 m will be meaningless.
- **`BAS_CON` and `TOP_CON` are vertical level indices**, not heights; `HBAS_CON` / `HTOP_CON` (in stream `/00`) and `HBAS_SC` / `HTOP_SC` (in `/01`) are the corresponding heights above MSL.

---

## Notes

- **This is a far more complete public feed than IMGW's ALADIN-family products.** [ALARO](./alaro-poland.md) is cropped to ~38 % of its operational domain, downsampled to 3-hourly and truncated in range; [AROME](./arome-poland.md) is cropped but otherwise complete. COSMO is subject to **none** of these reductions — native rotated grid, full 380 × 405 domain, all 50 model levels, hourly, full 60 h range. Whether the published field set is the *complete* operational GRIBOUT content is not something the files can settle (**flag**), but the presence of the full 3D state, the soil column and the constant-field file makes it look like the model's own output rather than a curated subset.
- **Volume is the price.** One cycle is **≈ 12.1 GB** (61 × 161.2 MB + 61 × 37.0 MB + 19.7 MB), and a full day is **≈ 48 GB**. Anyone mirroring this feed should plan for that; anyone wanting only surface fields will be discarding roughly 90 % of what they download, since GRIB1 offers no server-side subsetting here.
- **Retention is exactly four cycles (~24 h), with no archive.** On 2026-08-16 at 18:20 UTC the resident cycles were 2026-08-15 06/12/18 UTC and 2026-08-16 00 UTC; 2026-08-15 00 UTC and earlier returned nothing, and 2026-08-16 06 UTC had not yet appeared — a publication lag of **at least 18 h** after nominal cycle time, consistent with the ~16 h lag observed for ALARO/AROME. Critically, **COSMO does not appear in the portal's `Dane archiwalne` (archive) product list at all**, whereas `ALARO_pub` and `AROME_pub` do, with year folders 1970 and 2018–2026. The rolling 24 h window is the entire available history.
- **Missing files return HTTP 200 with an HTML error page, not 404.** This differs from ALARO/AROME, which 404 cleanly. A harvester testing existence by status code will treat every absent step as present and every error page as data. Reliable tests: `Content-Type` (`application` for real files, `text/html` otherwise) on a HEAD request, or the `GRIB` magic bytes. **Range requests are ignored** — the server returns the whole file — so byte-probing is expensive.
- **Concurrent requests produce false negatives.** An initial parallel probe (10 workers) of the 61-step space reported roughly 24 steps missing in each stream; sequential re-probing with retries found **all 61 present in both**. The server evidently throttles or drops concurrent connections in a way that is indistinguishable from a missing file given the point above. Serialise the probes and retry before concluding anything is absent.
- **Ensemble sibling is not published.** IMGW runs **COSMO PL – TLE**, a 20-member time-lagged ensemble on the same 2.8 km / 380 × 405 domain, 4× daily to +60 h, with no data assimilation. It is used internally for EPS-based products (visibility/fog, tornado index, ML post-processing) and is **not** on the public portal — it belongs on the Wiki's "Systems Not in the Catalog" page rather than in an `ensemble_models/` entry, unless a feed appears.
- **Parent model is also unpublished.** COSMO PL 7 (7 km) supplies the LBCs and is not distributed.
- **Successor system:** IMGW's **ICON PL** (ICON-LAM, ~2.5 km equivalent, R2B10, 65 levels, +48 h, 4× daily, no DA, nested in R3B7 ICON Global) has run alongside COSMO since 2019 and, in IMGW's own verification, outperforms COSMO-CE PL for most surface and upper-air parameters. IMGW-PIB stated in June 2026 that the public data would move from COSMO to ICON-LAM, but **has given no date** and the ICON-LAM output is not yet on the portal. This entry should be revisited when it appears — as a new `icon-pl.md` entry rather than an edit here, since the two are different models.
- **Consortium context:** developed within the **COSMO** consortium. IMGW-PIB contributes the EULAG dynamical core work and leads/participates in the AWARE, INSPECT, CITTA, EPOCS, EGALITE and MILEPOST priority projects. Related COSMO deployments elsewhere in the catalogue should be cross-linked as they are added.

---

## Recent configuration history
*(from the IMGW EWGLAM/SRNWP poster series, 2016–2025; the public feed carries no version metadata, so these are documentation-sourced rather than verified.)*

- **2016 and earlier:** COSMO PL 7 km (415 × 445, 40 levels, +78 h) and COSMO PL 2.8 km (380 × 405, dt = 20 s, **+36 h**), plus a 2.8 km EPS.
- **2019:** 2.8 km deterministic at **model version 5.01 with the Runge–Kutta core**, +48 h; COSMO PL 7 extended to +82 h. ICON PL begins semi-operational running in June 2019 at 00 UTC only.
- **June 2020:** the operational 2.8 km run is **switched from Runge–Kutta (v5.01) to the EULAG core (v5.05)** — COSMO PL 2.8 becomes COSMO-CE PL. COSMO PL 7 extended to +86 h.
- **2021:** configuration stable — COSMO-CE PL 2.8 km / +48 h / nudging / 1 h coupling from COSMO PL 7; ICON PL at 65 levels, 2× daily (00/12 UTC).
- **Between Sept 2021 and Oct 2024:** deterministic and ensemble forecast range extended **+48 h → +60 h**; ICON PL moves to 4× daily at +48 h. The 60 h range matches the public feed as verified.
- **2025:** ICON PL 2.6.2.2 verified against COSMO-CE PL 6.01 over JJA2024–MAM2025, with ICON PL better or equal for essentially all surface parameters. **Flag:** the 2025 poster gives the COSMO-CE PL version as **6.01** in the verification text while the operational-suite box on the same poster still reads **5.05**. The discrepancy is unresolved and the files carry no version field.

---

## Official documentation
- IMGW-PIB public data portal (datastore): https://danepubliczne.imgw.pl/pl/datastore
- IMGW-PIB public data portal (successor host): https://dane.imgw.pl/datastore
- IMGW-PIB meteorological models portal: https://modele.imgw.pl
- IMGW-PIB model descriptions: https://aplikacjameteo.imgw.pl/informacje-i-pomoc/prognoza-pogody/modele-prognozy-pogody/
- COSMO consortium: https://www.cosmo-model.org
- COSMO GRIB I/O conventions (local tables, level coding, `pv` array): https://www.cosmo-model.org/content/model/cosmo/coreDocumentation/cosmo_io_guide_6.00.pdf

### Key references
- Mazur, A., Interewicz, W., Jaczewski, A., Jurczyk, A., Ośródka, K., Surowiecki, A., Szaton, M., Szturc, J., Wyszogrodzki, A. (2025). *Numerical Weather Prediction at IMGW-PIB.* 47th EWGLAM & 32nd SRNWP Meeting, 22–25 September 2025.
- Linkowska, J., Jaczewski, A., Jurczyk, A., Ośródka, K., Szturc, J., Wyszogrodzki, A., Ziemiański, M., Interewicz, W. (2024). *Numerical Weather Prediction at IMGW-PIB.* 46th EWGLAM & 31st SRNWP Meeting, 30 September – 3 October 2024.
- Linkowska, J., Mazur, A., Wójcik, D., Ziemiański, M. (2021). *Numerical Weather Prediction at IMGW-PIB.* 43rd EWGLAM & 28th SRNWP Meeting, 27 September – 1 October 2021.
- Linkowska, J., Mazur, A., Wójcik, D., Ziemiański, M. (2020). *Numerical Weather Prediction at IMGW-PIB.* 42nd EWGLAM & 27th SRNWP Meeting, 28 September – 2 October 2020. *(Documents the June 2020 Runge–Kutta → EULAG switch.)*
- Linkowska, J., Interewicz, W., Wójcik, D., Wyszogrodzki, A., Ziemiański, M. (2019). *Numerical Weather Prediction at IMGW-PIB.* 41st EWGLAM & 26th SRNWP Meeting, 30 September – 3 October 2019, Sofia.
- Ziemiański, M. Z., Wójcik, D. K., Rosa, B., Piotrowski, Z. P. (2021). *Compressible EULAG dynamical core in COSMO: convective scale Alpine weather forecasts.* Mon. Wea. Rev., 149, 3563–3583.
- Smolarkiewicz, P. K., Kühnlein, C., Wedi, N. P. (2014). *A consistent framework for discrete integrations of soundproof and compressible PDEs of atmospheric dynamics.* J. Comput. Phys., 263, 185–205.
