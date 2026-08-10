# MFWAM GLOB01 (Météo-France Global Wave Model, 0.1°)

## What this model is
MFWAM is Météo-France's third-generation spectral ocean wave forecast model, derived from the WAM code (WAMDI Group, 1988). It forecasts sea state — wind sea and swell — from the wave action balance equation, forced by 10 m winds from Météo-France's atmospheric NWP chain.

This entry documents the **GLOB01** configuration: the global 0.1° grid, forced by [ARPEGE](../../../nwp_models/global/france/arpege.md) and assimilating satellite wave observations, distributed as GRIB2 through France's national open data portal and the underlying OVH object store.

Météo-France runs MFWAM in several configurations distributed through different channels. The regional 0.025° European grid (FRANGP0025) is a separate product with its own package and cadence — see [MFWAM FRANGP0025](../../regional/france/mfwam-hr-france.md). A distinct global MFWAM at 1/12° with multi-satellite assimilation is distributed through Copernicus Marine — see [MFWAM Global (Copernicus)](./mfwam-copernicus.md). These are different operational systems, not different renderings of one run.

This package has only carried global data since **25 March 2025**; before that it published a European domain, and a separate 0.5° global package existed until April 2025. Both are covered under *Recent version history* — the distinction matters for anyone working with archived files, since `01` files predating that date are Euro-Atlantic, not global.

> **Naming note.** The distribution names this package `vague-surcote` ("wave–surge"), a tree it shares with Météo-France's HYCOM2D storm surge systems. The MFWAM files themselves contain no surge fields.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France
- **GRIB2 originating centre (verified):** `lfpw` — French Weather Service, Toulouse (centre 85), subCentre 10, `generatingProcessIdentifier = 24`

---

## What area it covers
- **Coverage:** Global oceans
- **Grid (verified):** regular latitude–longitude, **3600 × 1801**, 0.1° × 0.1°, 6,483,600 total grid points
- **Grid origin:** first grid point 90°N / 0.0°, last −90° / **359.9°E**, scanning mode 0 (west→east, north→south)

> **Documentation defect — the published eastern bound is wrong.** `documentation-serveur-dp2024-mfwam` gives the GLOB01 domain as `90N 90S 0 359,5E`. The grid actually ends at **359.9°E**: 3600 columns at 0.1° starting from 0.0° cannot terminate at 359.5°. Prior versions of this entry reproduced the error.

### Masking
Fields carry a GRIB bitmap, and **the wave fields and the wind fields use different masks**.

- **Wind fields (`10si`, `10wdir`):** 2,533,079 masked points, **static** — byte-identical mask at +1h, +48h and +102h. 3,950,521 valid points (60.93%).
- **Wave fields (all 16):** mask **varies with lead time** — 2,850,038 masked at +1h, 2,863,873 at +48h, 2,844,044 at +102h. Valid points range from about 3,619,700 to 3,639,600 (≈55.8–56.1%).

The 316,959 points masked in the wave fields but not the wind fields at +1h are **92% south of 60°S** (291,774 points) and only 6,504 north of 60°N. Sampled on 2026-08-10 — austral winter, Antarctic ice near seasonal maximum; Arctic ice near minimum. The wave mask is therefore land plus a **time-varying sea ice mask**; the wind mask is the static land–sea mask alone.

Consumers building a persistent valid-point index must rebuild it per step for wave fields. A mask taken from +1h will discard live wave data at later steps.

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** single regular latitude–longitude grid
- **Core wave model:** MFWAM (third-generation WAM derivative)
- **Horizontal resolution:** 0.1° (~11 km at the equator)
- **Spectral resolution:** 24 directions × 30 frequencies
- **Forecast length (verified):** **102 h at every cycle**
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** hourly +1h to +48h, then 3-hourly +51h to +102h — **66 steps, no analysis step**
- **Vertical levels:** not applicable — wave fields are on `meanSea` (level 0); winds on `heightAboveGround` 10 m

> **Documentation conflict on forecast length — the model documentation carries figures from a superseded configuration.** `20250318-doc-mfwam.pdf` states a 114 h maximum and per-cycle lengths of 102 h (00Z), 72 h (06Z), 114 h (12Z) and 60 h (18Z). `documentation-serveur-dp2024-mfwam` states a uniform 102 h at all four cycles. Live enumeration of four consecutive cycles on 2026-08-09 found **66 files reaching 102 h at every one**; the server descriptif is correct.
>
> The likely explanation is visible in the document itself: **the same four lengths appear twice** — once attributed to GLOB01, and once in an availability section headed "Modèles sur grille GLOB05 et EURAT01," the pre-consolidation grid pair retired in 2025 (see *Recent version history*). They read as carried over from the old configuration rather than re-derived for the global GLOB01. It remains possible that the full operational chain still runs to 114 h at 12Z and the public package is truncated to 102 h, but nothing in the open distribution supports that.

> **No analysis step is published.** Files begin at 001H. The data.gouv description advertises "champs d'analyse et de prévision" and `20250318-doc-mfwam.pdf` states that analyses are available 3-hourly on grids other than FRANGP0025. Neither is true of this distribution as verified.

### File and message structure
One GRIB2 file per step, **18 messages per file**, all on the same grid:

```
vague-surcote-MFWAM__01__SP1__<STEP>H__<YYYY-MM-DD>T<HH>:00:00Z.grib2
```

`01` is the grid token (0.1°; `0025` is the FRANGP0025 grid), `SP1` the single published package. `<STEP>` is zero-padded to three digits.

- **File size:** 87–89 MB per step
- **Volume per run (verified):** **5.66 GiB** — ~22.6 GiB/day across four cycles
- **Packing:** `grid_ccsds` (CCSDS/AEC compression), 16 bits per value
- **GRIB2 tables version:** 32; `localTablesVersion = 0`; PDT 4.0 throughout
- **`typeOfProcessedData`:** unset. ecCodes reports `missing` rather than 1 (forecast) — a minor header defect, harmless for decoding but it prevents filtering on that key.

---

## Forcing and nesting
- **Wind forcing:** ARPEGE 10 m winds. The 10 m wind speed and direction are republished in the wave files themselves (messages 17–18) at the wave model's grid and mask.
- **Ice forcing:** present but undocumented. Its effect is visible in the time-varying wave bitmap (see *Masking*); no ice concentration field or masking threshold is published or stated in the documentation. **TBD.**
- **Current forcing:** not documented for GLOB01. **TBD.**
- **Parent for:** [FRANGP0025](../../regional/france/mfwam-hr-france.md) is nested inside the global configuration.

---

## Data assimilation
- **Assimilates wave observations:** Yes
- **Observation sources:** satellite altimetric and spectral wave data from spaceborne radar. The documentation states this for the global and large-domain regional configurations without naming missions or instruments — **specific missions TBD**. Contrast [MFWAM Copernicus](./mfwam-copernicus.md), where the assimilated mission list is published in full.
- **Method / cadence:** not published. **TBD.**

---

## What it provides
All 16 wave parameters are on `meanSea`; the two wind parameters on `heightAboveGround` 10 m. Verified message inventory, in file order:

| # | Doc name | Description | Units | D,C,N | ecCodes `shortName` |
|---|---|---|---|---|---|
| 1 | MWD | Mean wave direction (total sea) | degree true | 10,0,14 | `mwd` |
| 2 | MWP | Mean wave period (total sea) | s | 10,0,15 | `mwp` |
| 3 | SWH | Significant height, combined wind waves and swell | m | 10,0,3 | `swh` |
| 4 | MDWW | Direction of wind waves | degree true | 10,0,4 | `wvdir` |
| 5 | MPWW | Mean period of wind waves | s | 10,0,6 | `mpww` |
| 6 | SHWW | Significant height of wind waves | m | 10,0,5 | `shww` |
| 7 | MDPS | Direction of primary swell | degree true | 10,0,**194** | *unknown* |
| 8 | MPPS | Period of primary swell | s | 10,0,**195** | *unknown* |
| 9 | SHPS | Height of primary swell | m | 10,0,**192** | *unknown* |
| 10 | MDSS | Direction of secondary swell | degree true | 10,0,**196** | *unknown* |
| 11 | MPSS | Period of secondary swell | s | 10,0,**197** | *unknown* |
| 12 | SHSS | Height of secondary swell | m | 10,0,**193** | *unknown* |
| 13 | MDS | Direction of total swell | degree true | 10,0,7 | `swdir` |
| 14 | MPS | Period of total swell | s | 10,0,9 | `mpts` |
| 15 | SHS | Height of total swell | m | 10,0,8 | `shts` |
| 16 | PP1D | Peak period (1D) | s | 10,0,34 | `pp1d` |
| 17 | WIND | 10 m wind speed | m s⁻¹ | 0,2,1 | `10si` |
| 18 | DWI | 10 m wind direction | degree true | 0,2,0 | `10wdir` |

No wave spectra, no Stokes drift, no maximum-crest parameters, and no third swell partition are published. Only one package (`SP1`) exists; there is no second package to fetch.

> **Encoding defect — the six swell-partition parameters are unresolvable from the headers alone.** Parameters 7–12 use discipline 10, category 0, numbers **192–197**, which is the WMO local-use range, while the same messages declare `localTablesVersion = 0` — i.e. no local table is asserted. ecCodes 2.48.0 returns `shortName = unknown`, `name = unknown` and `units = unknown` for all six. Decoding requires the mapping table on page 4 of `20250318-doc-mfwam.pdf`, reproduced above. Note that primary and secondary swell are the only fields in the file that cannot be identified without external documentation, and that message **order** is the only other discriminator, so tooling should key on the numeric triplet rather than on `shortName`.

Sanity ranges from the 2026-08-10 00Z +1h file: `swh` 0.004–11.881 m (mean 2.647), `pp1d` 1.801–23.552 s, `10si` 1.000–29.976 m s⁻¹. Primary swell height (192) tops out at 9.707 m, exactly matching total swell height (8) — consistent with primary swell dominating the total in the peak cell.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Data formats:** GRIB2 (`grid_ccsds` packing)
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-mfwam-resolution-0-1deg
  (also surfaced at https://meteo.data.gouv.fr/datasets/65bd1a2957e1cc7c9625e7b5)

### Two access paths, with different retention
**The portal exposes only the most recent cycle.** The dataset carries 68 resources — 66 GRIB2 files plus 2 documentation PDFs — refreshed in place each run. There is no archive on data.gouv.fr, and resource UUIDs are stable across runs, so a URL saved yesterday returns today's data.

**The object store carries a 15-day rolling archive**, all four cycles, and is anonymously listable:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/MFWAM/01/SP1/<FILE>
```

with `<CYCLE>` as `YYYY-MM-DDTHH:00:00Z`. Standard S3 `list-type=2` queries work against the bucket root; the `pnt/` prefix contains 3-hourly cycle directories (120 of them, confirming the 15-day window), of which MFWAM populates only 00, 06, 12 and 18. Retention boundary verified on 2026-08-10: `2026-07-26T00:00:00Z` absent, `2026-07-26T06:00:00Z` complete with 66 files.

Anyone needing more than the current run should script against the object store, not the portal.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | First file | Last file | Latency |
|---|---|---|---|
| 00Z | 04:22 | 04:27 | T+4h22m |
| 06Z | 11:13 | 11:26 | T+5h13m |
| 12Z | 16:10 | 16:23 | T+4h10m |
| 18Z | 23:16 | 23:26 | T+5h16m |

Runs complete in 3–13 minutes once publication starts. Sampling the 00Z cycle across eight days (2026-07-27 to 2026-08-10) gave a first-file time between 04:18 and 04:28 — stable to within about ten minutes.

---

## Notes
- **Portal metadata is stale.** `temporal_coverage` on the data.gouv record still reads 2024-02-28 to 2024-02-29, from the dataset's creation in February 2024. It does not track the rolling window and should be ignored.

- **The `vague-surcote` label spans two other model families.** The prefix also carries the **HYCOM2D** storm surge systems (`-MARO`, `-MARP`, `-WARO`, `-WARP`, 0.04°), which belong under `storm_surge_models/` and are not yet catalogued, and the **WW3** coastal wave systems ([WW3-MARO](../../regional/france/ww3-maro-france.md), [WW3-MARP](../../regional/france/ww3-marp-france.md), [WW3-WARP](../../regional/france/ww3-warp-france.md)) — WAVEWATCH III on unstructured meshes, distributed as NetCDF. Note the asymmetry: HYCOM2D has four configurations, WW3 only three, with **no WW3-WARO**. Nothing from either family appears inside the MFWAM files.

- **Enumerate a completed cycle, not the current one.** The WW3 NetCDF files publish roughly 20 minutes after the MFWAM GRIB2 files. Since the object store has no real directories — a prefix exists only once a key beneath it does — a listing of the in-progress cycle will show `MFWAM` and `HYCOM2D-*` but omit `WW3-*` entirely, which looks like absence rather than lateness.

- **FRANGP0025 also overruns its documentation.** The 0025 grid under `MFWAM/0025/SP1/` published 49 files reaching **51 h** at the 2026-08-10 00Z cycle, against a documented 48 h. Recorded in the [FRANGP0025 entry](../../regional/france/mfwam-hr-france.md).

- **Relationship to ECWAM.** MFWAM and ECMWF's [ECWAM](../eu/ecwam.md) are cross-pollinated WAM descendants rather than a fork in either direction: the Ardhuin/Météo-France wind-input and deep-water dissipation parametrizations were adopted into ECWAM at IFS Cycle 46r1. GLOB01's 0.1° grid is finer than the 0.25° ECWAM open subset, but ECWAM's open data runs to 360 h against GLOB01's 102 h, and GLOB01 publishes no spectra.

- **Choosing among the three MFWAM distributions.** GLOB01 is the only one with hourly output and no registration, and the right choice for short-range global wave work. [MFWAM Copernicus](./mfwam-copernicus.md) offers 10-day range, a published assimilation mission list, Stokes drift and maximum-crest parameters, at 1/12° and 3-hourly, in exchange for registration. [FRANGP0025](../../regional/france/mfwam-hr-france.md) is the European high-resolution nest.

- **Wind fields are wave-model output, not raw ARPEGE.** Messages 17–18 carry the 10 m wind interpolated to the wave grid and subject to the wave model's land–sea mask. Users wanting ARPEGE winds should take them from the [ARPEGE](../../../nwp_models/global/france/arpege.md) packages directly.

---

## Recent version history

### 2025-03/04 — two-tier consolidation onto the 0.1° global grid
Météo-France distributed **three** MFWAM packages until spring 2025. Over two weeks the coarse global tier and the European 0.1° tier were merged into a single global 0.1° product:

| Package | Grid token | Until 2025-03-25 | After |
|---|---|---|---|
| MFWAM 0.5° | `05` | Global | **Discontinued 2025-04-08** |
| MFWAM 0.1° | `01` | Europe, 72°N–20°N / 32°W–42°E | **Global** (this entry) |
| MFWAM 0.025° | `0025` | France élargie | unchanged |

- **2025-03-25, 06Z run** — the 0.1° package switched from the European extraction to the full global grid. Recorded in the dataset description; both documentation PDFs were re-uploaded on 25 and 26 March 2025.
- **2025-04-08** — the 0.5° global package was permanently withdrawn. Its dataset description states it was replaced by the 0.1° global grid. No `05` token appears anywhere in the object store's 15-day window.

This makes the March change a **replacement of the 0.5° global tier**, not merely a widening of the European one. Users who were taking global waves at 0.5° before April 2025 are the population this entry's product was built to absorb.

The grid names `GLOB05` and `EURAT01` in `20250318-doc-mfwam.pdf` are best read as this superseded pair — the 0.5° global grid and the 0.1° Euro-Atlantic grid respectively. The document's front section was updated for the global switch while its availability section was not, which also accounts for the forecast-length discrepancy noted under *Basic details*. Neither name appears in the current distribution.

> **The retired 0.5° dataset is still live-looking on the portal.** `paquets-de-modele-de-vagues-mfwam-resolution-0-5deg` (id `65bd19fe0d61026813636c33`) is **not flagged as archived**, still declares `frequency: continuous`, and still serves 66 GRIB2 resources — frozen at the 2025-04-08 00Z and 06Z cycles. Automated harvesters keying on the portal's archive flag will treat it as an active product. Out of scope for this catalog as a discontinued system, but worth a *Systems Not in the Catalog* wiki line.

### 2024-02-02 — dataset created on data.gouv.fr
Initial publication of the MFWAM packages on the national portal.

### March 2011 — stabilised global MFWAM operational
Per `20250318-doc-mfwam.pdf`, a stabilised global configuration entered operations in March 2011, at that time forced by ECMWF (IFS) winds and assimilating available satellite data. Subsequent configurations and physics improvements, notably to dissipation, are described but not dated. The current GLOB01 is ARPEGE-forced.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-mfwam-resolution-0-1deg
- Model presentation (FR, includes the GRIB2 parameter table on p.4): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-mfwam-resolution-0-1deg/20250325-124758/20250318-doc-mfwam.pdf
- Server/package technical descriptif (FR): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-mfwam-resolution-0-1deg/20250326-104726/documentation-serveur-dp2024-mfwam-20250326.pdf
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live-verified 2026-08-10 against the 2026-08-10T00:00:00Z cycle (steps 001H, 048H, 102H decoded with ecCodes 2.48.0), with cycle enumeration and publication timestamps sampled 2026-07-26 to 2026-08-10, and the sibling MFWAM packages surveyed via the data.gouv.fr catalog API.*
