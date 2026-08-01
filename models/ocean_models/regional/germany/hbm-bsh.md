# HBM (BSH operational circulation model — North Sea, Baltic Sea, Tidal Elbe)

## What this model is
**HBM** (HIROMB-BOOS Model) is the three-dimensional baroclinic ocean circulation model that forms the core of the operational forecast system run by the **Bundesamt für Seeschifffahrt und Hydrographie (BSH)**, Germany's federal maritime and hydrographic agency. It simulates tidal, wind-driven and density-driven motion across the North Sea and Baltic Sea on several interactively coupled grids, with a separate very-high-resolution nest over the Tidal Elbe. Its operational role is to support BSH's water level and storm surge forecasting service, oil drift and pollutant dispersion calculations, ice forecasting, and search-and-rescue drift prediction.

HBM is the direct descendant of **BSHcmod**, the circulation model developed at BSH in the 1990s (Dick et al. 2001; Kleine 1994, 2003). BSH provided the code to DMI in 1999; after extensive Danish redevelopment it was renamed HBM in 2009 and adopted by the BOOS and HIROMB communities. The same code lineage therefore underlies DMI's [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) and the former Copernicus Marine Baltic MFC system. BSH separately retains **BSHsmod/BSHcmod** as a fast 2D barotropic water level and surge model — that is a different system and is not what this entry covers.

> **Scope caution — this entry documents a model whose public data is a narrow slice of it.** HBM produces water level, 3D currents, temperature, salinity and sea ice. The **only** openly distributed gridded output located is the *"Surface currents for sailors"* GRIB package: **two velocity components at a single near-surface level**, and nothing else. No water level, temperature, salinity or ice field is published through this channel. Users needing 3D Baltic or North Sea physics from an open channel should look to Copernicus Marine rather than to this feed.

---

## Who runs it
- **Production Unit:** Bundesamt für Seeschifffahrt und Hydrographie (BSH), Operational Modelling section (*Operationelle Modellierung*)
- **Country:** Germany
- **Programme or coordinating body:** National operational service. HBM development is shared with European partners (DMI, SMHI) through the BOOS/HIROMB community.
- **Role in any larger system:** Published descriptions of DWD's coastal wave model [CWAM](../../../wave_models/regional/germany/cwam-dwd.md) state that it is driven by water level and currents from BSH's HBM, making BSH an upstream forcing provider for the innermost tier of the DWD wave chain. Those descriptions date from the COSMO-EU era and the persistence of the coupling in the current operational setup is **not verified** — see the matching flag in the CWAM entry.
- **Contact:** `opmod@bsh.de`

---

## What area it covers

### Model domains (from BSH documentation)
| Domain | Horizontal resolution | Vertical layers |
|---|---|---|
| North Sea and Baltic Sea (parent) | ~5 km | 35 |
| German Bight and western Baltic Sea (nest) | ~0.9 km | 25 |
| Tidal Elbe (nest) | 90 m | 7 |

All grids are **fully dynamically (two-way) coupled**. Published BSHcmod-era descriptions give the parent domain as 4°W–30.5°E, 48.5°N–60.5°N in the North Sea and to 66°N in the Baltic.

### Distributed windows — all live-verified from GRIB2, 2026-07-31 cycle
Sixteen packages are published. Each is a rectangular window on one of the three model grids; **the distributed grids are the native model grids, not a re-interpolation.** `dlon`/`dlat` are the encoded increments; `water` is the fraction of grid points carrying data (the remainder is land-masked via a GRIB bitmap).

**3 nautical mile grid (5′ × 3′ = 0.083333° × 0.050000°) — the ~5 km parent**

| Area | Name | Ni × Nj | Longitude | Latitude | Points | Water |
|---|---|---|---|---|---|---|
| `no` | North Sea incl. English Channel to Start Point | 154 × 241 | 3.875°W – 8.875°E | 48.625 – 60.625°N | 37 114 | 55.0% |
| `ba` | Baltic Sea incl. Kattegat and part of Skagerrak | 256 × 245 | 8.958 – 30.208°E | 53.625 – 65.825°N | 62 720 | 26.4% |

**0.5 nautical mile grid (50″ × 30″ = 0.013889° × 0.008333°) — the ~0.9 km nest**

| Area | Name | Ni × Nj | Longitude | Latitude | Points | Water |
|---|---|---|---|---|---|---|
| `db` | German Bight | 240 × 387 | 6.174 – 9.493°E | 53.229 – 56.446°N | 92 880 | 64.2% |
| `idb` | Inner German Bight | 90 × 123 | 7.757 – 8.993°E | 53.229 – 54.246°N | 11 070 | 51.7% |
| `nfi` | North Frisian Islands | 90 × 121 | 7.757 – 8.993°E | 54.246 – 55.246°N | 10 890 | 75.1% |
| `ofi` | East Frisian Islands | 114 × 123 | 6.174 – 7.743°E | 53.229 – 54.246°N | 14 022 | 71.2% |
| `wb` | Western Baltic incl. Belts and Sound to Bornholm | 412 × 387 | 9.201 – 14.910°E | 53.229 – 56.446°N | 159 444 | 40.5% |
| `kbu` | Kiel Bight | 108 × 64 | 9.840 – 11.326°E | 54.304 – 54.829°N | 6 912 | 69.9% |
| `mbu` | Lübeck / Mecklenburg Bight | 128 × 102 | 10.757 – 12.521°E | 53.904 – 54.746°N | 13 056 | 64.0% |
| `rgn` | Area around Rügen | 144 × 213 | 12.507 – 14.493°E | 53.229 – 54.996°N | 30 672 | 45.4% |
| `snd` | The Sound | 67 × 24 | 12.174 – 13.090°E | 56.104 – 56.296°N | 1 608 | 47.4% |
| `blt` | The Belts | 130 × 156 | 9.507 – 11.299°E | 54.704 – 55.996°N | 20 280 | 54.9% |

**90 m grid (5″ × 3″ = 0.001389° × 0.000833°) — the Tidal Elbe nest**

| Area | Name | Ni × Nj | Longitude | Latitude | Points | Water |
|---|---|---|---|---|---|---|
| `AusAlt` | Outer Elbe to Altenbruch | 320 × 228 | 8.306 – 8.749°E | 53.850 – 54.039°N | 72 960 | 93.4% |
| `CuxBru` | Cuxhaven to Brunsbüttel | 410 × 156 | 8.681 – 9.249°E | 53.820 – 53.949°N | 63 960 | 58.9% |
| `BruPag` | Brunsbüttel to Pagensand | 276 × 300 | 9.201 – 9.583°E | 53.650 – 53.899°N | 82 800 | 18.8% |
| `PagHam` | Pagensand to Hamburg | 432 × 240 | 9.301 – 9.899°E | 53.500 – 53.699°N | 103 680 | 10.7% |

- **Sub-areas are bit-exact windows, not separate products.** Live-verified: `idb` sits at integer offset (i = 114, j = 0) in the `db` grid, and over the 5 719 common water points the values are **identical to the bit** with 100% mask agreement. The same integer-offset relationship holds for `nfi`, `ofi`, `kbu`, `mbu`, `rgn`, `blt` and `snd` against `db`/`wb`. Downloading a sub-area *and* its parent retrieves the same numbers twice.
- **`db` and `wb` are two windows on one 0.5 nm master grid**, sharing identical latitude bounds and offset by exactly 218 columns in longitude; they overlap between 9.201°E and 9.493°E. Likewise `no` and `ba` lie on a single 3 nm lattice (exactly 154 columns and 100 rows apart) and are adjacent rather than overlapping, with a one-cell gap between 8.875°E and 8.958°E.
- **Land–sea mask is static.** The bitmap is identical across all 96 time steps of a file, so wetting and drying — which HBM performs internally, per BSH documentation — is **not** represented in the distributed mask.

> **Live-verified — several published domain bounds are materially wrong.** BSH's product page and the bundled README give "approx." bounds that in four cases miss by more than a rounding: `db` is stated as reaching **58°N** but ends at **56.446°N**; `ba` is stated as **52°N–68.8°N, 10°–30°E** but is actually **53.625–65.825°N, 8.958–30.208°E**; `no` is stated as reaching **10°E** but ends at **8.875°E**; `kbu` is stated as **53.9–54.75°N** but actually spans **54.304–54.829°N**. Use the table above, not the published bounds.

---

## Basic details
- **Model type:** Regional 3D baroclinic ocean physics, deterministic, with an online sea ice component
- **Core ocean model:** **HBM** (HIROMB-BOOS Model) — hydrostatic primitive-equation, free surface, Boussinesq; flux-corrected transport for advection and diffusion; Smagorinsky horizontal viscosity. Descendant of BSHcmod (Dick et al. 2001). Operational HBM version at BSH: **TBD** — not published.
- **Sea ice model:** Online thermodynamic/dynamic sea ice, integral to HBM. Version **TBD**.
- **Horizontal resolution:** ~5 km parent / ~0.9 km German Bight and western Baltic / 90 m Tidal Elbe. Delivered unchanged.
- **Vertical levels:** 35 / 25 / 7 respectively, of differing thicknesses
- **Vertical coordinate:** Generalised ("dynamical") vertical coordinates are available in HBM alongside z-coordinates with free surface (Kleine 2003). **Which is used in the current BSH operational configuration is TBD.**
- **Forecast length:** **120 h** from the 00 and 12 UTC cycles; **78 h** from the 06 and 18 UTC cycles. **Tidal Elbe: 60 h**, main cycles only.
- **Update frequency:** North Sea / Baltic **4× daily**; Tidal Elbe **2× daily**
- **Production cycles:** 00, 06, 12, 18 UTC (Elbe: 00 and 12 UTC)
- **Public product frequency:** **2× daily only.** The GRIB packages are built from the 00 and 12 UTC cycles; the 06 and 18 UTC runs are not published through this channel.
- **Target delivery time:** documented as approximately **10:00 and 22:00 local time**. Live-verified mtimes on 2026-07-31 were **17:50–17:54 UTC** for the North Sea and Baltic packages and **19:22–19:28 UTC** for the Elbe packages — i.e. the evening publication landed earlier than documented, and Elbe lags the marine areas by roughly 90 minutes.
- **Temporal output resolution:** **15 minutes**, 96 steps per file, from **00:15 to 24:00 UTC** of the covered day. Live-verified: uniform 900 s spacing with no gaps; the final step is encoded as 00:00 of the following day.
- **Archive availability:** **None for the North Sea and Baltic** — only the current forecast set is retained. **Three analysis days for the Elbe** (live-verified: 29, 30 and 31 July present on 2026-07-31, 12 files per area). Continuous local capture is required for any time-series use.
- **Bathymetry source:** TBD — not stated in BSH's public documentation.

---

## Forcing
- **Atmospheric forcing:** Operational atmospheric model of **Deutscher Wetterdienst (DWD)**, delivered 4× daily. Fields used: 10 m wind, air pressure, air temperature, cloud cover and specific humidity (the latter three for air–sea heat flux). **Which DWD model — ICON-EU, ICON-D2, or a combination — is not stated in BSH's documentation (TBD).** BSH additionally receives an ECMWF forecast set in parallel, primarily for redundancy and as groundwork for a future uncertainty product.
- **River runoff:** Published descriptions of BSH's operational HBM setups cite the **E-HYPE** runoff model operated by SMHI, with German river input from BfG. Not restated in current BSH documentation (**TBD** for the present configuration).
- **Lateral boundary conditions:** Tidal predictions plus external surge from BSH's own **BSHsmod.na** North Atlantic surge model, per a 2012 BSH presentation. Current configuration **TBD**.
- **Tidal forcing:** Yes — tides are computed explicitly and propagate from the open boundary through the coupled nests.
- **Initial conditions:** Warm start from the preceding cycle, with SST assimilation increments applied. Details **TBD**.

---

## Coupling
- **Internal grid coupling:** The ~5 km parent, the ~0.9 km German Bight / western Baltic nest and the 90 m Tidal Elbe nest are **fully dynamically two-way coupled**, not one-way nested.
- **Sea ice:** Online within HBM. Seabed temperature is also carried, which matters for the wintertime Baltic.
- **Waves:** A coupled circulation-plus-wave configuration (`BSHcmod.w` → HBM+WAM) is documented historically. Whether wave coupling is active in the current operational HBM at BSH is **TBD**. Separately, DWD's [CWAM](../../../wave_models/regional/germany/cwam-dwd.md) is described in the literature as consuming HBM currents and water levels — a one-way link outward, unverified in the current setup.
- **Atmosphere:** Not coupled; atmospheric forcing is one-way.

---

## Data assimilation
- **DA scheme:** Satellite **sea surface temperature** has been assimilated operationally **since the beginning of 2021**. The scheme documented in the peer-reviewed literature for the BSH system is a **local SEIK filter** (Losa et al. 2012, 2014); whether that is still the operational scheme is **TBD**.
- **Update cycle:** TBD
- **Increment application:** TBD

### Assimilated observations
- **Sea surface temperature:** Satellite SST, operational since early 2021. Historical work used NOAA/AVHRR products; the current source list is **TBD**.
- **In-situ temperature and salinity profiles:** In development, not yet operational (per BSH)
- **Sea ice concentration:** In development, not yet operational (per BSH)
- **Currents:** BSH states that current measurements have been combined with the model through data assimilation "for some years"; no further detail is published (**TBD**).
- **Sea surface height (altimetry):** Not documented

---

## What it provides

### Publicly distributed fields — the complete list
| Field | GRIB2 discipline/category/number | GRIB1 table 2 / parameter | Level | Unit |
|---|---|---|---|---|
| Eastward current component (u) | 10 / 1 / **2** | 3 / **49** | `depthBelowSea`, level 1 | m s⁻¹ |
| Northward current component (v) | 10 / 1 / **3** | 3 / **50** | `depthBelowSea`, level 1 | m s⁻¹ |

That is the whole product: 2 parameters × 96 time steps = **192 GRIB messages per file**, live-verified across North Sea, Baltic and Elbe files alike.

- **Vector convention:** `uvRelativeToGrid = 0` — components are resolved along true east and true north, not grid-relative.
- **Observed value range:** −2.30 to +2.66 m s⁻¹ over a full North Sea day; −1.74 to +1.83 m s⁻¹ over a full Tidal Elbe day. Plausible for these waters.

### Not distributed through this channel
Water level, sea surface height, temperature, salinity, mixed layer depth, sea ice concentration or thickness, surface stress, bathymetry, land–sea mask as a standalone field, or grid cell dimensions. HBM computes all of these; none are published here.

---

## Data availability
- **Is the data free?** **Yes** — no registration, no account, no API key, no approval gate
- **License:** **CC BY 4.0.** A `LICENSE.txt` in the download root names BSH as rights holder, permits sharing and adaptation including commercial use, and gives the recommended attribution *"Data provided by BSH, CC BY 4.0."* Attribution, a link to the licence, and indication of changes are required.
  > **The licence declaration is very recent and lives only in the download directory.** `LICENSE.txt` and the bilingual `README_en_de.txt` carry mtimes of **2026-07-27**. BSH's own product web page states only a liability disclaimer and **does not mention the licence at all**. Cite the files, not the page.
- **Is the data downloadable?** Yes, via three channels (below)
- **Data formats:** **GRIB2** (uncompressed `.grb2` and bzip2 `.grb2.bz2`) and **GRIB1** (bzip2 only, `.grb.bz2`). Live-verified: no uncompressed GRIB1 is offered, consistent with the README.
- **Product identifier:** None. No DOI, no formal dataset ID.
- **Dataset identifiers:** 16 area codes — `no`, `db`, `idb`, `nfi`, `ofi` (North Sea); `ba`, `wb`, `kbu`, `mbu`, `rgn`, `snd`, `blt` (Baltic); `AusAlt`, `CuxBru`, `BruPag`, `PagHam` (Elbe)
- **File naming:** `Current_XX_YYYYMMDDHH_VV.grb2` (GRIB2), `Current_XX_YYYYMMDDHH_VV.grb.bz2` (GRIB1), where `XX` is the area code, `YYYYMMDDHH` is the date and hour of the **underlying DWD meteorological analysis**, and `VV` is a forecast-day index. **See the warning about `VV` under Notes — the convention is not uniform.**
- **File size (live-verified, uncompressed GRIB2, 2026-07-31):** `snd` 0.29 MB · `kbu` 1.24 MB · `idb` 1.59 MB · `nfi` 2.00 MB · `mbu` 2.06 MB · `ofi` 2.15 MB · `blt` 2.96 MB · `rgn` 3.33 MB · `no` 4.79 MB · `ba` 4.99 MB · `PagHam` 6.77 MB · `BruPag` 8.00 MB · `db` 10.98 MB · `wb` 14.64 MB · `CuxBru` 16.04 MB · `AusAlt` 27.97 MB. Whole tree ≈ **1.4 GB**.
- **Directory layout:**
  ```
  /                     LICENSE.txt, README_en_de.txt
  /grib1/{Nordsee,Ostsee,Elbe}/       .grb.bz2 only
  /grib2/{Nordsee,Ostsee,Elbe}/       .grb2 and .grb2.bz2
  /grib2/fixname/                     fixed-name copies (GRIB2 only, 4 areas only)
  ```
- **DOI:** None
- **Delivery mechanism:** FTP and an ownCloud-based public file share ("BSH FileBox", ownCloud 10.16.3, share owner `fbopmod`).

### Access channels

**1. Anonymous FTP** — `ftp://ftp.bsh.de/Stroemungsvorhersagen/`
Host resolves to `linftp60.bsh.de` (141.17.83.103). **Not verified here:** port 21 was unreachable from the verification environment, which permits HTTP/HTTPS egress only. Treat FTP availability as documented-but-unconfirmed.

**2. FileBox web share (GUI)** — `https://filebox.bsh.de/index.php/s/Z8k9NMB3dKVpOXA`
Browser folder view with per-file and per-folder download. Live-verified: HTTP 200, no password, public upload disabled.

> **The URL printed on BSH's product page is dead.** The page displays the link text `https://filebox.bsh.de/Stroemungsvorhersagen`, which returns **HTTP 404**. Only the underlying `href` — the tokenised share above — works. Anyone reading, printing or transcribing the displayed URL will get nothing.

**3. FileBox scripted access (no FTP, no browser)** — both routes live-verified:
- Plain HTTPS, single file:
  `https://filebox.bsh.de/index.php/s/Z8k9NMB3dKVpOXA/download?path=%2Fgrib2%2FOstsee&files=Current_snd_2026073112_01.grb2`
- Plain HTTPS, whole folder as ZIP: same URL with `path=` and no `files=`
- WebDAV PROPFIND for directory enumeration:
  `curl -u "Z8k9NMB3dKVpOXA:null" -X PROPFIND -H "Depth: 1" https://filebox.bsh.de/remote.php/dav/public-files/Z8k9NMB3dKVpOXA/`
  Note the **share token is the WebDAV username** and the password must be non-empty (`null` works); the legacy `public.php/webdav/` endpoint returns `NotAuthenticated` and should not be used.

> **The share token is the only handle on this data over HTTPS, and it is not a stable identifier.** If BSH re-creates the share, every scripted HTTPS route breaks with no redirect and no announcement. Re-read the `href` on the product page before assuming a failure is an outage.

---

## Version history

BSH publishes no version history for this product. The following is what could be established.

### 2026-07-27 — Explicit open licence added
`LICENSE.txt` and a rewritten bilingual `README_en_de.txt` appear in the download root, declaring **CC BY 4.0**. Prior licence status not established.

### 2021 (early) — Operational SST assimilation
Satellite sea surface temperature assimilation enters operations.

### Undated — Move to 15-minute output
Inferred, not documented: the file-size table in `README_en_de.txt` is smaller than live files by close to a factor of four for several areas (`db` 3 MB stated vs 10.98 MB actual; `rgn` 0.8 vs 3.33; `kbu` 0.4 vs 1.24; `snd` 0.1 vs 0.29), which is what a switch from hourly to 15-minute output would produce. **Conjecture** — the ratio is not uniform across all 16 areas, and no BSH source confirms it. What is certain is that the size table is stale.

### 2009 — BSHcmod lineage renamed HBM
DMI's revised BSHcmod is accepted by BOOS and HIROMB partners as HBM.

---

## Notes

> **Independently-overwritten files in one directory belong to two different meteorological cycles.** Live-verified on 2026-07-31: the North Sea and Baltic directories held `Current_db_2026073100_00` (from the **00 UTC** DWD analysis) alongside `Current_db_2026073112_01/_02/_03` (from the **12 UTC** analysis). Day 0 therefore comes from an analysis 12 hours older than days 1–3. Whether the day-0 file is regenerated at the evening publication or simply retained is **TBD** — it would take observation across two cycles to settle.

> **The `VV` forecast-day index does not mean the same thing in the Elbe directory as in the marine directories.** The README states that `VV` is the forecast day relative to the day of the analysis. Live-verified valid dates:
>
> | File | First valid step |
> |---|---|
> | `Current_kbu_2026073100_00` | 2026-07-31 00:15 |
> | `Current_kbu_2026073112_01` | 2026-08-01 00:15 |
> | `Current_kbu_2026073112_02` | 2026-08-02 00:15 |
> | `Current_kbu_2026073112_03` | 2026-08-03 00:15 |
> | `Current_PagHam_2026073100_00` | 2026-07-31 00:15 |
> | `Current_PagHam_2026073100_01` | 2026-08-01 00:15 |
> | **`Current_PagHam_2026073112_01`** | **2026-07-31 00:15** |
> | **`Current_PagHam_2026073112_02`** | **2026-08-01 00:15** |
>
> For the North Sea and Baltic, valid day = analysis day + `VV` in both cycles. For the **Elbe 12 UTC** files, valid day = analysis day + `VV` − **1**. Applying the documented rule uniformly puts every Elbe evening file a day out. Read `dataDate` from the GRIB rather than parsing the filename.

> **Two files can carry the same valid times and different numbers.** `Current_PagHam_2026073100_00` and `Current_PagHam_2026073112_01` both cover all 96 steps of 2026-07-31. Live-verified across all 192 matching (parameter, time) fields: **no field is identical**, maximum absolute difference **1.456 m s⁻¹**, median per-field RMS difference **0.109 m s⁻¹**. The difference is not confined to the second half of the day (mean RMS 0.147 before 12 UTC, 0.140 after). Anything that stitches Elbe files by valid time must pick a cycle and stay on it.

- **The GRIB messages carry no analysis time and no forecast step.** Every message encodes `forecastTime = 0`, `step = 0`, `typeOfGeneratingProcess = 0` (*analysis*) and `significanceOfReferenceTime = 1`, with `dataDate`/`dataTime` set to the **valid** time. `dataTime` is HHMM, so `15` is 00:15 UTC, not 15:00. The DWD analysis that produced the file survives **only in the filename** — decode-and-discard workflows lose provenance entirely.

- **Originating centre is not set.** `centre = 0` (*WMO Secretariat*), `subCentre = 0`, `generatingProcessIdentifier = 0`. Nothing in the files identifies BSH. Files from this feed cannot be distinguished from other GRIB by header metadata alone.

- **ecCodes does not resolve the parameters.** In ecCodes 2.48 both messages report `shortName = unknown`, `name = unknown`, `units = unknown` in GRIB2 and GRIB1 alike. Fall back to the code tables: GRIB2 discipline 10 / category 1 / number **2** and **3** are U- and V-component of current in m s⁻¹; GRIB1 table 2 parameters **49** and **50** are the same. This is the same failure mode noted for FMI's Baltic feed — see [NEMO Baltic (FMI)](../finland/nemo-baltic-fmi.md).

- **The encoded level contradicts the documented one.** Messages encode `typeOfLevel = depthBelowSea`, `level = 1`, `typeOfFirstFixedSurface = 160` with **`typeOfSecondFixedSurface = 255` (missing)** — i.e. a single surface at 1 m depth. BSH documents the values as the **mean over the surface-to-5 m layer** (or to the bottom where shallower). The layer nature is not encoded at all, and the encoded depth is not the layer midpoint. Anyone reading the header alone will interpret these as 1 m point values.

- **Currents are total and Eulerian.** Tidal, wind-driven and density-driven components are combined and **cannot be separated** — no tide-only or residual-only product exists. The values are Eulerian model velocities; **Stokes drift is not included**, which matters for anyone using these for drift or leeway estimation.

- **Two different packings, two different precisions.** North Sea and Baltic files use **JPEG 2000 (`grid_jpeg`) at 12 bits**; Elbe files use **`grid_simple` at 16 bits**, which is why Elbe file sizes are byte-constant across days. Effective granularity is roughly 1 × 10⁻³ m s⁻¹ in both cases — ample for the application. Missing-value sentinel is **9999** with a bitmap present throughout.

- **`fixname/` is a partial mirror, not a parallel product.** It holds fixed-name copies (`Current_ba_today.grb2`, `..._today_plusone`, `_plustwo`, `_plusthree`) for the Expedition navigation software. Live-verified: `Current_no_today.grb2` is **md5-identical** to `Current_no_2026073100_00.grb2`. But it exists **only under `grib2/`**, not `grib1/`, and covers **only 4 of the 16 areas** (`no`, `db`, `ba`, `wb`) — the README's "the directory fixname … remains unchanged" understates how narrow it is.

- **`mbu` is named inconsistently by BSH itself.** The English README and product page call it *Lübeck Bight*; the German README calls it *Mecklenburger Bucht* — which is what the `mbu` code abbreviates. They are adjacent but not identical bights. The live domain (10.757–12.521°E) spans both.

- **Not registered on GovData.** Live-verified: a GovData CKAN search returns no record for this dataset. The only nearby hit is *"Projekt ImoNav: Strömungsvorhersagen für die Deutsche Bucht und westliche Ostsee"*, a separate project dataset. Given the CC BY 4.0 declaration and Germany's obligations under the EU High-Value Datasets regulation, absence from the national portal is worth raising with BSH.

- **Relationship to sibling systems.** Same code lineage as DMI's [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) — HBM descends from BSH's BSHcmod, which BSH gave DMI in 1999. FMI's Baltic feed retains `producer=hbm` as a compatibility alias pointing at NEMO output, a fossil of the same lineage; see [NEMO Baltic (FMI)](../finland/nemo-baltic-fmi.md). On the German wave side, [CWAM](../../../wave_models/regional/germany/cwam-dwd.md) covers effectively the same coastal domain on a **near-identical 50″ × 30″ lattice** — but BSH encodes truncated increments (0.013889° / 0.008333°) where DWD encodes exact 50″/30″, so encoded corner coordinates diverge by up to ~1 × 10⁻⁴° (≈10 m) across the domain. **Do not assume the two grids are co-registered without checking.**

- **Operational use cases cited by BSH:** water level and storm surge forecasting, oil drift and pollutant dispersion, search and rescue, water quality studies, and — for this specific product — recreational sailing and regatta navigation software.

---

## Open questions for BSH (`opmod@bsh.de`)
1. Which DWD atmospheric model provides the forcing, and at what resolution?
2. Is the `VV` index difference between the Elbe and marine directories intentional, and can the README be corrected?
3. Is the day-0 file regenerated at the evening publication, or retained from the morning cycle?
4. Is the encoded `depthBelowSea = 1 m` intended to represent the 0–5 m layer mean? Could `typeOfSecondFixedSurface` be set to encode the layer?
5. Could `centre` be set to a BSH identifier rather than 0?
6. Are the current operational configuration's river runoff and open-boundary forcing sources still E-HYPE / BSHsmod.na?
7. Is the FileBox share token stable, or should users expect it to change?
8. Is registration of this dataset on GovData planned?

---

## Official documentation
- Product page (English): https://www.bsh.de/EN/DATA/Predictions/Currents/Surface_currents_for_sailors/surface_currents_for_sailors_node.html
- Product page (German): https://www.bsh.de/DE/DATEN/Vorhersagen/Stroemungen/Oberflaechenstroemung_fuer_Segler/oberflaechenstroemung_fuer_segler_node.html
- Model description — Hydrodynamic (English): https://www.bsh.de/EN/TOPICS/Operational_modelling/Hydrodynamic/hydrodynamic_node.html
- Model description — Hydrodynamik (German): https://www.bsh.de/DE/THEMEN/Modelle/Hydrodynamik/hydrodynamik_node.html
- Operational modelling overview: https://www.bsh.de/EN/TOPICS/Operational_modelling/operational_modelling_node.html
- Geoinformation and Open Data: https://www.bsh.de/EN/TOPICS/Geoinformation_and_Open_Data/geoinformation_and_open_data_node.html
- Bundled `README_en_de.txt` and `LICENSE.txt` in the download root (the only licence statement located)
- Operating institute: https://www.bsh.de/
- DOI: none

### Key references
- Dick, S., Kleine, E., Müller-Navarra, S. H., Klein, H., and Komo, H. (2001). *The Operational Circulation Model of BSH (BSHcmod) — Model description and validation.* Berichte des BSH, Nr. 29.
- Kleine, E. (2003). *Die konzeptionelle Formulierung des BSH-Zirkulationsmodells.* BSH internal report — generalised vertical coordinate formulation.
- Berg, P., and Poulsen, J. W. (2012). *Implementation details for HBM.* DMI Technical Report 12-11.
- Losa, S. N., Danilov, S., Schröter, J., Nerger, L., Massmann, S., and Janssen, F. (2012). *Assimilating NOAA SST data into the BSH operational circulation model for the North and Baltic Seas: Inference about the data.* Journal of Marine Systems, 105–108, 152–162. https://doi.org/10.1016/j.jmarsys.2012.07.008
- Zhuang, S. Y., et al. / Berg, P. (2020). *Improvements in turbulence model realizability for enhanced stability of ocean forecast and its importance for downstream components.* Ocean Dynamics, 70, 1017–1030. https://doi.org/10.1007/s10236-020-01353-9
- Stanev, E. V., et al. (2021). *Ocean Circulation Model Applications for the Estuary-Coastal-Open Sea Continuum.* Frontiers in Marine Science, 8, 657720. https://doi.org/10.3389/fmars.2021.657720
- Staneva, J., Wahle, K., Koch, W., Behrens, A., Fenoglio-Marc, L., and Stanev, E. V. (2016). *Coupling of wave and circulation models in coastal–ocean predicting systems: a case study for the German Bight.* Ocean Science, 12, 797–806. https://doi.org/10.5194/os-12-797-2016

---

*Live verification performed 2026-07-31 against the 2026-07-31 00/12 UTC cycles: WebDAV enumeration of all four directory trees, GRIB2 header decode for all 16 areas, full-file decode for `no`, `snd`, `PagHam` (three files) and `db`/`idb`, GRIB1 decode for `snd`, md5 comparison of the `fixname` mirror, and HTTP checks of all published access URLs. FTP was not reachable from the verification environment and remains unconfirmed.*
