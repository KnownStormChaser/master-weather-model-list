# HARMONIE-AROME Ireland (Met Éireann – DINI)

## What this model is
HARMONIE-AROME Ireland is the **convection-permitting regional numerical weather prediction (NWP) model** distributed by Met Éireann for short-range forecasting over Ireland and surrounding waters.

Since 2024, Met Éireann no longer runs a separate Irish-domain HARMONIE production. Instead, Ireland's operational HARMONIE forecasts are produced as part of the **United Weather Centres-West (UWC-West)** partnership of DMI (Denmark), the Icelandic Met Office, KNMI (Netherlands), and Met Éireann, on the shared **DINI** (Denmark–Iceland–Netherlands–Ireland) domain. Met Éireann distributes its slice of the same DINI run via the Met Éireann Open Data Portal at [opendata.met.ie](https://opendata.met.ie/).

**What Met Éireann publishes is the DINI ensemble, not a separate deterministic run.** Met Éireann's own Data Store catalogue names the product **`Harmonie UWCW DINIeps`**, and every GRIB2 message in the public files is encoded on an ensemble product definition template. The files labelled `CONTROL` are the **control member (`perturbationNumber = 0`, `typeOfProcessedData = cf`)** of the 1+30 DINI-EPS; the files without that label are **raw perturbed members (`typeOfProcessedData = pf`)**. This entry covers the control-member product, which is the practical deterministic feed. The perturbed members are described under **What it provides** and **Notes** below; they are not documented in a separate ensemble entry.

---

## Who runs it
- **Operating partnership:** United Weather Centres-West (UWC-West)
  - Met Éireann (Ireland)
  - DMI (Danish Meteorological Institute)
  - Icelandic Met Office (Veðurstofa Íslands)
  - KNMI (Royal Netherlands Meteorological Institute)
- **Public distributor (this dataset):** Met Éireann
- **Country / region:** Ireland (distribution); multi-national (operations)
- **Computing infrastructure:** UWC-West "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík
- **Originating centre in GRIB:** `eidb` (Dublin), `subCentre = 255`, `generatingProcessIdentifier = 43`

---

## What area it covers
- **Coverage:** Ireland and surrounding waters, plus larger cutouts extending to the full DINI domain
- **Domain name:** DINI (Denmark–Iceland–Netherlands–Ireland)
- **Domain details:** Met Éireann publishes **three different geographic cutouts** of the shared DINI integration, all on the same Lambert conformal projection and all at 2 km. This is a meaningful difference from the other UWC-West distributors, who each publish a single cutout per product.

### Grid geometry (live-verified from GRIB2, 18 UTC cycle, 18 September 2026)

All three cutouts share the same projection definition:

- Projection: **Lambert conformal conic**, `LaD = 55.5°N`, `LoV = 8.0°W` (`352.0`), `Latin1 = Latin2 = 55.5°`, southern pole at 90°S / 0°E
- Grid spacing: **2000 m × 2000 m** (`DxInMetres` / `DyInMetres`)
- Earth shape: sphere of radius 6,371,229 m (`shapeOfTheEarth = 6`)
- Scanning: `iScansNegatively = 0`, `jScansPositively = 1`
- `uvRelativeToGrid = 1` — **wind components are relative to the Lambert grid frame, not true geographic east/north.** Software computing wind direction in another CRS must apply a rotation. This matches the DMI distribution.

| Cutout | `Nx × Ny` | Points | Extent (km) | Lat range | Lon range |
|---|---|---|---|---|---|
| `DINI` | **1909 × 1609** | 3,071,581 | 3818 × 3218 | 37.655–69.933°N | 43.260°W–40.151°E |
| `IRL25` | **639 × 730** | 466,470 | 1278 × 1460 | 46.679–60.174°N | 18.171°W–4.607°E |
| `IoI` | **229 × 386** | 88,394 | 458 × 772 | 49.865–56.833°N | 11.523–4.037°W |

Two things to note here:

- **Met Éireann's DINI grid is not the same grid DMI publishes.** Met Éireann distributes **1909 × 1609**; DMI distributes the native **1906 × 1606**. Met Éireann's cutout is three points larger in each dimension, with a first grid point of 39.639°N / 25.447°W against DMI's 39.671°N / 25.422°W. The two are not byte-comparable and cannot be assumed to share an index origin.
- **`IRL25` is a legacy name and does not indicate 2.5 km.** The decoded grid spacing is 2000 m. The label is inherited from Met Éireann's retired 2.5 km Irish domain and no longer describes the resolution.

---

## Basic details
- **Model type:** Regional NWP — control member of a regional EPS, distributed as the deterministic product
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core, AROME physics, SURFEX surface scheme); cycle **43h**
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2 km; shallow convection parameterized)
- **Horizontal resolution:** 2 km, native Lambert conformal grid (no regridding) — live-verified
- **Vertical levels:** **90 hybrid levels** — live-verified (`NV = 182` = 2 × (90 + 1); the `mllow` product carries hybrid levels 68–90)
- **Model top:** 10 hPa (TBD — standard HARMONIE-AROME configuration; not encoded in the distributed GRIB)
- **Forecast length:** **60 hours** — live-verified (steps 000–060 present for every completed cycle)
- **Update frequency / cycles:** **Hourly, all 24 cycles** — live-verified. Met Éireann publishes every hourly cycle to the Open Data Portal, unlike DMI, which collects its deterministic DINI product only every third hour.
- **Temporal output resolution:** 1 hour
- **Ensemble encoding:** every message carries `numberOfForecastsInEnsemble = 31` on product definition template **1** (instantaneous) or **11** (statistically processed)

---

## Data assimilation
- **Data assimilation:** Yes — the UWC-West DINI production runs hourly cycling assimilation, with observation processing via ECMWF's SAPP (Scalable Acquisition and Pre-Processing) across the four partner services
- **Method / cadence:** 3D-Var with a three-hour assimilation window and a one-hour cut-off (as documented for the same production in the [DMI DINI-EPS entry](../../../ensemble_models/regional/denmark/harmonie-dini-eps-dmi.md)); the distributed GRIB reports `hoursAfterDataCutoff = 0`, `minutesAfterDataCutoff = 0`, which is not informative about the real cut-off

---

## Initial and boundary conditions
- **Initial conditions:** UWC-West HARMONIE-AROME analysis on the DINI domain
- **Boundary conditions:** ECMWF IFS — the control is coupled to IFS-HRES; the perturbed members are coupled to IFS-ENS members in a 1+30 LBC configuration

---

## What it provides

Met Éireann publishes **seven control-member product streams** and **two perturbed-member streams** per hourly cycle, each as one file per forecast step. The parameter set runs *inversely* to domain size: the smallest cutout carries the richest variable set.

| Stream | Grid | Typical size / step | Messages | Content |
|---|---|---|---|---|
| `CONTROL_grib2_ie` | TBD | **529–591 MB** | TBD | Not inspected — largest stream; likely the full-domain, full-parameter set |
| `CONTROL_grib2_ieDINI` | 1909 × 1609 | 98–109 MB | 39 | Surface + pressure-level core set, full DINI cutout |
| `CONTROL_grib2_ieIRL25` | 639 × 730 | 43–46 MB | 88 | Surface + pressure levels, Ireland/UK cutout |
| `CONTROL_grib2_ieIoI` | 229 × 386 | 12–14 MB | 116 | Richest surface set, Island-of-Ireland cutout |
| `CONTROL_grib2_mlIoI` | 229 × 386 | 52–60 MB | 543 | Full 90 hybrid levels, Island-of-Ireland cutout |
| `CONTROL_grib2_mllow` | 229 × 386 | 8–9 MB | 69 | Lowest 23 hybrid levels (68–90): T, U, V only |
| `CONTROL_grib2_pp` | 1909 × 1609 | 68–79 MB | 22 | Post-processed / aviation and satellite-simulation fields, full DINI cutout |
| `grib2_enIRL25` | 639 × 730 | 10–13 MB | — | Perturbed members (6 files per step) |
| `grib2_enIoI` | 229 × 386 | 6–11 MB | 64 | Perturbed members (6 files per step) |

### `ieIoI` — 116 messages, 31 parameter/level-type combinations
- Temperature: 2 m; heights 0/50/100/305/610/914/1524 m; 10 pressure levels; time-max and time-min 2 m temperature
- Dewpoint: surface and 2 m
- Wind U/V: 10 m; heights 50/100/150/200/305/610/914/1524 m; 10 pressure levels
- Time-maximum 10 m eastward and northward wind gusts
- Geometric vertical velocity on 10 pressure levels
- Pseudo-adiabatic potential temperature at 850/925/1000 hPa
- Relative humidity (as a 0–1 fraction) at the same height and pressure levels
- Pressure at surface and mean sea level; geopotential at surface
- Precipitation: total, snowfall, graupel, rain rate, and one local-table accumulation — all accumulated over the preceding hour
- Precipitation type; snow depth water equivalent
- Cloud: total, low, medium, high cover (0–1 fractions); cloud base
- CAPE; visibility; freezing-level height; global radiation flux (hourly accumulation); a lightning field

**Pressure levels (all streams that carry them):** 100, 200, 250, 300, 400, 500, 700, 850, 925, 1000 hPa.

### `ieIRL25` — 88 messages, 18 combinations
Drops CAPE, visibility, cloud base, freezing level, radiation, snow depth, dewpoint, min/max 2 m temperature, and the 150/200 m wind levels. Adds geopotential on all 10 pressure levels. Retains the full pressure-level T/U/V/w set and the hourly precipitation accumulations.

### `ieDINI` — 39 messages, 14 combinations
The thinnest of the three. Geopotential on 10 pressure levels; temperature at 300/500/850/925 hPa only; U/V at 250/300/500/850/925/1000 hPa; 2 m temperature; 10 m wind and gusts; surface and mean sea level pressure; precipitation type and the hourly accumulations. **No relative humidity, no cloud fields, no vertical velocity.** Anyone needing the full parameter set over the wider domain should take the DMI or KNMI distributions instead.

### `pp` — 22 messages, 13 combinations, full DINI cutout
Total column cloud liquid water, cloud ice water, rain water, and graupel; cloud top and cloud base; CAPE; mixed layer depth; visibility; 2 m dewpoint; precipitation type; net short-wave radiation flux at cloud top; a lightning field (non-zero, up to ~1288 in the sampled step); an icing-type index on aviation heights 305/610/914/1524 m; three simulated-satellite fields (two brightness temperatures in K, one 0–1 reflectance-like field); and a cloud-fraction field at 2 m (fog).

### `mlIoI` — 543 messages, the full model-level product
Six variables on **all 90 hybrid levels** (540 messages), on the Island-of-Ireland grid: specific humidity, temperature, U and V wind, geometric vertical velocity, and a per-level cloud fraction (local code `0.6.192`, 0–1). Plus three single-level fields needed to reconstruct the vertical coordinate and geometry: surface pressure, mean sea level pressure, and surface geopotential.

This is the only Met Éireann stream carrying the complete model-level state, and at 52–60 MB per step it is far cheaper than the 529–591 MB `ie` stream. For anyone needing native model levels over Ireland — trajectory work, boundary-layer analysis, downstream nesting — this is the product to take. Note that it is Island-of-Ireland only; there is no model-level equivalent on the wider `IRL25` or `DINI` cutouts.

### `mllow` — 69 messages, a lowest-levels subset
Temperature, U and V on **hybrid levels 68–90** — the lowest 23 of the model's 90 levels — on the Island-of-Ireland grid. No other variables, and no surface pressure, so the hybrid coefficients must be taken from `mlIoI` to convert levels to pressure. A lightweight near-surface subset of `mlIoI` rather than an independent product.

---

## GRIB2 encoding notes

- **Edition 2 throughout.** Packing is `grid_ccsds` (CCSDS/AEC), at **24 bits** for `ieIoI`, `pp` and the `en*` streams and **16 bits** for `ieDINI`, `ieIRL25` and `mllow`.
- **Accumulations are per-step, not run-total.** Statistically processed fields (PDT 11) carry `stepRange = 0-1` at step 1, `lengthOfTimeRange = 1`. Do not difference successive steps.
- **Many parameters do not decode with stock ecCodes.** Met Éireann's centre-local tables are not shipped with ecCodes, so a large share of messages return `shortName = unknown` and `paramId = 0`. **Address parameters by `discipline`/`parameterCategory`/`parameterNumber`, not by `shortName`.** Observed local codes, identified here by value range and level set rather than from a published table (TBD — Met Éireann has not published a parameter table for these):
  - `0.1.192` — relative humidity, expressed as a **fraction 0–1**, not a percentage
  - `0.6.192` — a **generic cloud fraction** (0–1) whose meaning is set by the level: total cloud cover at the surface in `ieIoI`, a fog fraction at 2 m in `pp`, and per-level cloud fraction on all 90 hybrid levels in `mlIoI`
  - `0.6.194 / 0.6.195 / 0.6.196` — low / medium / high cloud cover fractions at the surface
  - `0.1.194` — icing-type index (integer 0–4) on aviation heights
  - `0.1.200` — a fifth precipitation accumulation (hail or freezing rain; zero in the sampled step)
  - `0.4.198 / 0.4.199` — simulated brightness temperatures (K); `0.4.200` — a 0–1 reflectance-like field
  - `0.17.192` — lightning
  - `0.3.6` with `typeOfFirstFixedSurface = 192` — a local height field distinct from the freezing-level height
- Several fields that *are* WMO-standard also fail to decode by name, including `0.1.8` (total precipitation), `0.1.53` (snowfall, water equivalent) and `0.1.75` (graupel).

---

## Data availability
- **Is the data free?** Yes. **No account is required for the near real time tier.** A free account is required only for the NWP Data Store (archive).
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0); attribution to Met Éireann required
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://opendata.met.ie/

### How to access — Met Éireann Open Data Portal

The portal provides two tiers, with materially different access terms:

| Tier | Window | Account required | Selection granularity |
|---|---|---|---|
| **Near real time** | most recent **3 hours** of files | **No** | Whole files only |
| **NWP Data Store** | rolling archive (see below) | **Yes** — free registration | Area, parameter, level, ensemble member |

**The near real time tier is openly scriptable.** The portal front end is a React single-page app that appears to gate everything behind a login, but the underlying endpoints answer unauthenticated — no cookie, token, or API key. Live-verified 18 September 2026:

- **List available files:**
  `GET https://opendata.met.ie/data-portal/near-realtime/nwp?from=<ISO8601>&to=<ISO8601>`
  Returns JSON: `[{"name": ..., "size": ..., "timestamp": ...}, ...]`. Both bounds must lie within the past three hours; the server rejects anything wider with an explicit error.
- **Download files:**
  `GET https://opendata.met.ie/data-portal/near-realtime/download/nwp?files=<comma-separated,URL-encoded>`
  Returns a ZIP archive containing the requested GRIB2 files.

**Files are only ever delivered zipped.** There is no per-file direct URL — even a single-file request comes back as a ZIP. Multi-file requests are packed into one archive. The three-hour window means a polling client must run at least every three hours or lose data; there is no gap recovery on this tier.

**Access route caveat.** Met Éireann's access model differs from the other UWC-West distributors and will not suit every user. There is no S3 bucket, no STAC or EDR API, no OPeNDAP or THREDDS endpoint, and no subsetting on the open tier — you take whole files or nothing, and the largest control stream is 529–591 MB **per forecast step**. A full 60-step cycle of that stream is roughly 33 GB. Users who need subsetting, server-side filtering, or a stable archive interface are better served by [DMI](../denmark/harmonie-dmi.md) or [KNMI](../netherlands/harmonie-knmi.md).

### NWP Data Store (archive) — extent live-verified

The Data Store catalogue endpoint (`POST /data-portal/nwp-forecasts`, open without authentication; the actual *retrieval* endpoints require a token) reports the following:

- **Earliest records: 1 September 2023** — not October 2023 as previously documented here.
- **Product title lineage:** `Harmonie IREPS` (to ~late October 2023) → `Harmonie UWCW ECDS` (~November–December 2023) → `Harmonie UWCW DINIeps` (January 2024 onward).
- **There is a roughly four-month archive gap in 2025.** Queries return records for cycles up to **24 June 2025** and then nothing until **29 October 2025 04 UTC**. Interpolating the query semantics, cycles between approximately **25 June and 28 October 2025** are absent. (TBD — cause not documented by Met Éireann; treat the archive as discontinuous rather than complete back to 2023.)
- **Ensemble member selection exists only here.** The catalogue exposes an `ensembles` field per cycle, and the Data Store request payload accepts member selection. The near real time tier does not.

### Alternative access via UWC-West partners

Because the DINI domain is a single shared UWC-West production, the same underlying model run is also available through:
- **DMI** (Danish Meteorological Institute) — DINI distribution via S3, EDR and STAC APIs at https://opendatadocs.dmi.govcloud.dk/ (see [HARMONIE (DMI)](../denmark/harmonie-dmi.md))
- **KNMI** — European cutout (P3) and Dutch cutout (P1) on the KNMI Data Platform (see [HARMONIE-AROME Europe / DINI (KNMI)](../netherlands/harmonie-knmi.md) and [HARMONIE-AROME Netherlands (KNMI)](../netherlands/harmonie-arome-netherlands.md))

**The Icelandic Met Office does not distribute model files.** Contacted directly, IMO confirmed it publishes no UWC-West model output and recommended DMI instead. Earlier versions of this entry listed IMO as a distribution route; that was incorrect. IMO hosts the Aurora supercomputer but is not a data distributor for this production.

These are not duplicate forecasts; they are repackagings of the same UWC-West model run, differing in distribution grid, retained variable set, GRIB encoding, and access mechanism.

---

## Notes
- **The public "deterministic" product is the DINI-EPS control member.** Live-verified: every message in the `CONTROL` files carries `typeOfProcessedData = cf`, `typeOfEnsembleForecast = 0`, `perturbationNumber = 0`, `numberOfForecastsInEnsemble = 31`, on PDT 1 or 11. This is the same relationship DMI documents for its own [DINI product](../denmark/harmonie-dmi.md) — with the difference that Met Éireann publishes every hourly cycle where DMI collects every third hour.
- **Met Éireann publishes raw perturbed ensemble members.** The `en*` streams are `typeOfProcessedData = pf`, `typeOfEnsembleForecast = 3`, with non-zero `perturbationNumber`. This is a materially different offering from DMI, which publishes only derived ensemble statistics, and from KNMI P4a, which publishes members on a coarser regridded grid. Met Éireann's members are on the native 2 km grid. They are not documented in a separate ensemble entry.
- **Member composition rotates hourly, and Met Éireann's API exposes it directly.** The Data Store catalogue reports the member set per cycle: 18 UTC → `{0,1,2,3,4,5}`, 19 UTC → `{0,6,7,8,9,10}`, 20 UTC → `{0,11,…,15}`, 21 UTC → `{0,16,…,20}`, and so on through member 30 across six consecutive hourly cycles. Each cycle integrates the control plus five perturbed members; the full 1+30 ensemble is a **time-lagged composite** across six cycles, so member lead times are not uniform. This is live confirmation of the composition described in the [KNMI P4a](../../../ensemble_models/regional/netherlands/harmonie-eps-knmi-eu.md) and [DMI DINI-EPS](../../../ensemble_models/regional/denmark/harmonie-dini-eps-dmi.md) entries.
- **Ensemble member files share an identical filename, and only one member is retrievable from the near real time tier.** Six files per step appear in the listing under the same `name`, distinguished only by `size` and `timestamp`. The download endpoint keys on name and deduplicates, returning a single member (`perturbationNumber = 1` in the sampled step) regardless of how many times the name is repeated in the request. The other five members of each hourly cycle are **unreachable through the near real time interface**; member selection requires a Data Store account.
- **Met Éireann no longer runs a separate Irish-only HARMONIE domain.** Prior to UWC-West, Met Éireann operated its own HARMONIE-AROME configuration and the **IREPS** ensemble (1000 × 900 grid, cy40h1.1, 65 vertical levels, 2.5 km, 4× daily). Both have been superseded by the shared UWC-West DINI production. Documentation that still references "IREPS" or the 1000 × 900 / 65-level configuration refers to the legacy system. The `IRL25` product name is a surviving artefact of that era.
- **DINI is the same model run distributed by other UWC-West partners.** The Met Éireann distribution, DMI's [DINI distribution](../denmark/harmonie-dmi.md), KNMI's [P3 European dataset](../netherlands/harmonie-knmi.md), and KNMI's [P1 Dutch dataset](../netherlands/harmonie-arome-netherlands.md) are all packaged independently from the same UWC-West cy43 model run on the Aurora supercomputer in Iceland. Underlying model state is identical; **grid extents are not** — see the grid geometry section above.
- **DINI vs MEPS:** UWC-West (Ireland, Denmark, Iceland, Netherlands) and MetCoOp ([MEPS](../norway/meps.md): MET Norway, SMHI, FMI, ESTEA, LEGMC) are the two regional UWC clusters running parallel HARMONIE-AROME productions. The two clusters target a common UWC NWP production by ~2027.
- **Relationship to siblings in the HARMONIE-AROME family:** The UWC-West HARMONIE production shares the same dynamical core and physics as other ACCORD-consortium HARMONIE-AROME and AROME deployments. See [MEPS](../norway/meps.md), [AROME-Arctic](../norway/arome-arctic.md), [AROME France](../france/arome-france.md), [AROME Hungary](../hungary/arome-hungary.md), and other family members.
- **Cycle 46 upgrade pending.** The UWC-West cy43 production is scheduled for replacement by HARMONIE-AROME cycle 46h1.1.1 in **October 2026**, which changes output encoding from FA to FullPOS (WMO) GRIB2 among other changes. Re-verify this entry's GRIB structure, parameter codes, and grid definition after the changeover.
- **GRIB edition:** All Met Éireann HARMONIE-AROME output is GRIB2.

---

## Recent version history

### Pending — Cycle 46 (operational October 2026)
See Notes above. The encoding change from FA to FullPOS GRIB2 is likely to resolve several of the local-table decoding problems documented here.

### September 2024 — UWC-West Aurora supercomputer becomes operational
The UWC-West collaboration entered its operational phase with the cy43 HARMONIE-AROME production running on the new HPE Cray "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík. Common UWC-West NWP models had been running operationally since March 2024. This consolidated Met Éireann's HARMONIE production with DMI, IMO, and KNMI onto the same hardware and the same model run, replacing Met Éireann's legacy Irish-only HARMONIE/IREPS chain.

### Late 2023 — transitional `UWCW ECDS` production
Between the end of IREPS (~late October 2023) and the start of the `Harmonie UWCW DINIeps` archive (January 2024), the Data Store carries a distinct product titled `Harmonie UWCW ECDS`. TBD — this transitional configuration is not documented in Met Éireann's public NWP pages.

### Pre-2024 — IREPS (legacy)
The Irish Regional Ensemble Prediction System (IREPS) became operational on 15 October 2018 as Met Éireann's first ensemble system, using HARMONIE-AROME cy40h1.1 with 1 control + 10 perturbed members on a 1000 × 900 / 2.5 km / 65-level grid. It was upgraded on 15 April 2020 to a 54-hour, 11-member ensemble running 4× daily (00/06/12/18 UTC) using combined ECMWF and KNMI HPC resources. IREPS was retired with the transition to the UWC-West DINI production in 2024. Data Store records under the `Harmonie IREPS` title run from 1 September 2023 to approximately late October 2023.

---

## Official documentation
- Met Éireann Open Data Portal: https://opendata.met.ie/
- Met Éireann Open Data documentation: https://opendata.met.ie/documentation
- Met Éireann Open Data about: https://opendata.met.ie/about
- Met Éireann Open Data registration (Data Store only): https://opendata.met.ie/register
- Met Éireann operational NWP overview: https://www.met.ie/science/numerical-weather-prediction/operational-nwp-in-met-eireann
- UWC-West operational announcement (Met Éireann): https://www.met.ie/new-met-eireann-weather-and-climate-supercomputer-becomes-operational-in-unique-collaboration-with-three-other-national-meteorological-services
- Bengtsson et al. (2017), *The HARMONIE–AROME Model Configuration in the ALADIN–HIRLAM NWP System*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
