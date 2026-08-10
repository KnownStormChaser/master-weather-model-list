# MFWAM FRANGP0025 (Météo-France High-Resolution Wave Model, 0.025°)

## What this model is
FRANGP0025 is the high-resolution European configuration of **MFWAM**, Météo-France's third-generation spectral wave model derived from the WAM code (WAMDI Group, 1988). It runs at 0.025° (~2.5 km) over the waters around France and the western Mediterranean, forced by [AROME](../../../nwp_models/regional/france/arome.md) 10 m winds and nested inside the global MFWAM configuration.

It is the finest-resolution wave product Météo-France distributes openly, and the only one of the three MFWAM packages driven by a convection-permitting atmosphere rather than by ARPEGE.

> **Correction to earlier versions of this entry.** This system was previously described here as WW3-based and distributed in NetCDF, with a ~72 h forecast range. All three were wrong: MFWAM is a **WAM** derivative (not WAVEWATCH III), the distribution is **GRIB2**, and the verified range is **51 h**. Météo-France does operate WW3-based wave models — the `WW3-MARO`/`-MARP`/`-WARO`/`-WARP` packages — but those are separate systems in a different model family.

Sibling configurations: [MFWAM GLOB01](../../global/france/mfwam-global-france.md) (global 0.1°, ARPEGE-forced, this model's parent) and [MFWAM Global (Copernicus)](../../global/france/mfwam-copernicus.md) (global 1/12° via Copernicus Marine).

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France
- **GRIB2 originating centre (verified):** `lfpw` — French Weather Service, Toulouse (centre 85), **subCentre 30**, **`generatingProcessIdentifier = 26`**

> The subCentre and process identifier differ from GLOB01's (10 and 24). Together they are a reliable discriminator between the two grids in mixed archives where filenames have been lost, since both packages otherwise carry an identical parameter set.

---

## What area it covers
- **Coverage:** Waters around metropolitan France — Bay of Biscay, western English Channel, southern North Sea approaches, and the western Mediterranean including the Gulf of Lion, Ligurian Sea and around Corsica
- **Grid (verified):** regular latitude–longitude, **801 × 601**, 0.025° × 0.025°, 481,401 total grid points
- **Bounds:** 53°N–38°N, 8°W–12°E, scanning mode 0 (west→east, north→south)

> **Longitude is encoded in the 0–360 convention and wraps the prime meridian.** The raw headers give `longitudeOfFirstGridPointInDegrees = 352.0` and `longitudeOfLastGridPointInDegrees = 12.0`. ecCodes resolves this correctly, returning a monotonic −8.0 → 12.0 axis with a uniform 0.025° step, but code that reads the two header keys directly and subtracts will compute a negative span. Use the decoded coordinate array, not the header pair.

### Masking
Fields carry a GRIB bitmap: **299,690 masked points, 181,711 valid (37.75%)**. Unlike GLOB01, the mask here is **a single static land–sea mask** — byte-identical across every parameter and every lead time tested (+1h, +48h, +51h), and identical between the wave fields and the wind fields.

This is a structural contrast with the global product, where wave fields carry a time-varying ice mask and the wind fields a separate static one. This domain has no sea ice, and the wind fields are masked to the same coastline as the waves — so **no 10 m wind is published over land**, and this package cannot substitute for AROME winds on the coastal fringe.

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** single regular latitude–longitude grid, nested inside the global configuration
- **Core wave model:** MFWAM (third-generation WAM derivative)
- **Horizontal resolution:** 0.025° (~2.5 km)
- **Spectral resolution:** 24 directions × 30 frequencies
- **Forecast length (verified):** **51 h at every cycle**
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** hourly +1h to +48h, then a single step at **+51h** — 49 steps, no analysis step
- **Vertical levels:** not applicable — wave fields on `meanSea` (level 0), winds on `heightAboveGround` 10 m

> **Every published forecast-length figure for this grid is wrong.** `description-paquets-modele-mfwam.pdf` gives 48 h at 00Z/12Z and 42 h at 06Z/18Z. `descriptif-modele-mfwam.pdf` describes a differently-bounded 0.025° grid (see *Documentation*) at 42 h and 36 h. Live enumeration of all four cycles on 2026-08-09 found **49 files reaching 51 h at every one**, and the same 49/51 h pattern holds at every cycle across the full 15-day archive window. The isolated +51h step is the 3-hourly continuation the global grid runs out to 102 h, truncated here after a single step.

> **No analysis step is published.** Files begin at 001H. Both the dataset description ("champs d'analyse et de prévision") and `descriptif-modele-mfwam.pdf` (hourly analyses on the 0.025° grid) advertise analyses. Neither is true of this distribution.

### File and message structure
One GRIB2 file per step, **18 messages per file**:

```
vague-surcote-MFWAM__0025__SP1__<STEP>H__<YYYY-MM-DD>T<HH>:00:00Z.grib2
```

`0025` is the grid token (`01` is GLOB01), `SP1` the single published package.

- **File size:** 4.83–4.95 MiB per step — **except 048H, see below**
- **Volume per run (verified):** 241–245 MiB — ~0.95 GiB/day across four cycles
- **Packing:** `grid_ccsds`, 16 bits per value
- **GRIB2 tables version:** 32; `localTablesVersion = 0`; PDT 4.0 throughout
- **`typeOfProcessedData`:** unset (`missing` rather than 1) — same defect as GLOB01

> **Defect — the 048H file is its own contents concatenated twice.** `vague-surcote-MFWAM__0025__SP1__048H__*.grib2` is ~9.65 MiB against ~4.85 MiB for every neighbouring step, and decodes to **36 messages instead of 18**. All 36 are at step 48. The second block of 18 repeats the first in the same parameter order, with **byte-identical halves** (MD5 `6c2292c4…` for both halves of the 2026-08-10 00Z file) — identical values, identical headers, no distinguishing key whatsoever.
>
> This is **persistent, not a one-off**: verified at all four cycles on 2026-08-09 and on 2026-08-05 and 2026-07-27, i.e. across the entire retention window. It also propagates to the portal, where 048H is listed at 9.7 Mo.
>
> Practical consequences: message-count assertions fail at exactly one step; naive iteration yields duplicate records for +48h; and tools that key on `(param, step)` will silently overwrite rather than error. Since the halves are identical, **deduplicating on the numeric parameter triplet is safe** — no information is lost by discarding the second block. Anyone computing archive volumes should note that ~4.8 MiB per cycle, roughly 2% of the product, is pure duplication.

---

## Forcing and nesting
- **Wind forcing:** [AROME](../../../nwp_models/regional/france/arome.md) 10 m winds. Speed and direction are republished in the wave files (messages 17–18) on the wave grid and sea mask.
- **Nested inside:** the global MFWAM configuration — [GLOB01](../../global/france/mfwam-global-france.md) since the March 2025 consolidation, and the EURAT01 0.1° European grid before it.
- **Ice forcing:** not applicable to this domain.
- **Current forcing:** not documented. **TBD.**

---

## Data assimilation
- **Assimilates wave observations:** **No.** The documentation states that satellite altimetric and spectral data are assimilated by "les modèles MFWAM globaux et régionaux (de grande emprise)" — the global and *large-domain* regional configurations. The 0.025° grid is neither, and the assimilating configurations are listed explicitly as the global grid and EURAT01. This grid takes its wave state from its parent through the nest boundary.

Users needing assimilated wave analyses at high resolution over this domain should look to [IBIWAM](../spain/ibiwam.md), which covers the Atlantic portion at 1/36° with altimeter and spectral assimilation.

---

## What it provides
Identical 18-parameter set to [GLOB01](../../global/france/mfwam-global-france.md), in the same message order. All 16 wave parameters on `meanSea`; both wind parameters on `heightAboveGround` 10 m.

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

No spectra, no Stokes drift, no maximum-crest parameters, no third swell partition. One package only (`SP1`).

> **The six swell-partition parameters are unresolvable from headers alone**, exactly as in GLOB01: numbers 192–197 sit in the WMO local-use range while `localTablesVersion = 0` declares no local table. ecCodes 2.48.0 returns `unknown` for name, shortName and units on all six. The mapping above is from page 4 of `descriptif-modele-mfwam.pdf`. Key on the numeric triplet, not on `shortName`.

> **Direction fields marginally exceed 360°.** Verified maxima include `wvdir` 360.001°, `swdir` 360.003° and `10wdir` 360.001° — an artifact of 16-bit packing across a 0–360 range. Harmless for display, but code that bins directions or indexes into a 360-element array should apply a modulo or clamp rather than assuming a closed [0, 360] interval.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Data formats:** GRIB2 (`grid_ccsds` packing)
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-mfwam-resolution-0-025deg
  (also surfaced at https://meteo.data.gouv.fr/datasets/65bd1a505a5b412989a84ca7)

### Two access paths — and the portal is missing a step
**The portal exposes only the most recent cycle, and only 48 of its 49 files.** The dataset carries 50 resources: 48 GRIB2 files covering steps 001–048, plus 2 documentation PDFs. **The +51h step is absent from the portal entirely** while being present on the object store at every cycle. Anyone treating the portal as the authoritative file list will silently lose the final three hours of the forecast.

**The object store carries the full 49 files and a 15-day rolling archive**, all four cycles, anonymously listable:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/MFWAM/0025/SP1/<FILE>
```

with `<CYCLE>` as `YYYY-MM-DDTHH:00:00Z`. Retention boundary verified on 2026-08-10: `2026-07-26T00:00:00Z` absent, `2026-07-26T06:00:00Z` complete with 49 files — the same boundary as GLOB01, consistent with a bucket-wide expiry policy rather than a per-product one.

Use the object store, not the portal.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | First file | Last file | Latency |
|---|---|---|---|
| 00Z | 04:28 | 04:29 | T+4h28m |
| 06Z | 11:19 | 11:20 | T+5h19m |
| 12Z | 16:16 | 16:17 | T+4h16m |
| 18Z | 23:22 | 23:23 | T+5h22m |

Publication completes in about one to two minutes — far faster than GLOB01's 3–13 minutes, as expected from the ~20× smaller volume. Timing tracks GLOB01 closely and lands a few minutes later at each cycle, consistent with both products being pushed by the same downstream job.

---

## Notes
- **Portal metadata is stale**, identically to GLOB01: `temporal_coverage` still reads 2024-02-28 to 2024-02-29 from the dataset's creation in February 2024, and does not track the rolling window.

- **The documentation PDFs on this dataset predate the 2025 consolidation and describe a different grid.** See *Documentation* below — this is the most significant caveat for anyone working from the published descriptions.

- **Choosing between the French wave products.** FRANGP0025 is the right choice for coastal and nearshore work around France out to two days, with AROME's convection-permitting winds as the main advantage. [GLOB01](../../global/france/mfwam-global-france.md) covers the same waters at 0.1° but reaches 102 h and assimilates satellite wave data. For the Atlantic seaboard specifically, [IBIWAM](../spain/ibiwam.md) offers comparable resolution (1/36°) *with* assimilation and a 10-day range, at the cost of Copernicus Marine registration.

- **Relationship to the WW3 regional packages.** Météo-France also publishes `WW3-MARO`, `-MARP`, `-WARO` and `-WARP` at 0.1° under the same `pnt-vague-surcote` portal tag. Those are WAVEWATCH III systems distributed as **NetCDF through data.gouv resources only** — they do not appear in the object store tree at all, so bucket enumeration will not find them. Different model family, different distribution mechanism, catalogued separately.

- **Volume figures in the documentation are roughly half the live values.** `description-paquets-modele-mfwam.pdf` gives 2.6 Mo per step; live files are 4.83–4.95 MiB. The likely cause is the packing change from `grib2_jpeg` (as documented) to `grid_ccsds` (as observed) — JPEG2000 achieves higher compression than CCSDS on smooth geophysical fields, at the cost of decode speed and lossiness.

---

## Documentation

Both PDFs attached to this dataset were uploaded on 2024-02-28 and **describe the pre-consolidation MFWAM configuration**. They have not been refreshed since, unlike the GLOB01 dataset's documents, which were re-uploaded in March 2025. This makes them a poor guide to the current product but a useful primary record of what the chain looked like before spring 2025.

**`descriptif-modele-mfwam.pdf`** describes a three-grid chain that no longer exists:

| Grid in the document | Resolution | Bounds | Forcing | Status today |
|---|---|---|---|---|
| FRANXL0025 | 0.025° | 51.5°N–41°N, 6°W–10.5°E | AROME | **Not the current grid** — see below |
| EURAT01 | 0.1° | 72°N–20°N, 32°W–42°E | ARPEGE, assimilating | Retired 2025-03-25 |
| GLOB05 | 0.5° | Global | ARPEGE, assimilating | Retired 2025-04-08 |

Two things follow. First, **this document confirms the identity of EURAT01 and GLOB05**, which appear as unexplained names in the current GLOB01 documentation — EURAT01's bounds match exactly the European domain the 0.1° package carried until March 2025. Second, the 0.025° grid it describes, **FRANXL0025 (51.5°N–41°N, 6°W–10.5°E), is not the grid in the files** — the live product is FRANGP0025 (53°N–38°N, 8°W–12°E), a materially larger domain extending further south into the Mediterranean and further north into the Channel. The two names appear in different documents in the same dataset.

**`description-paquets-modele-mfwam.pdf`** (version dated 02/01/2024) uses the correct FRANGP0025 name and bounds, but is stale in three respects: it gives the packing as `grib2_jpeg` where live files are `grid_ccsds`; it gives 48 h/42 h forecast lengths where live is uniformly 51 h; and its file-naming section refers to "les paquets de données du modèle **AROME**," an unedited copy-paste from the atmospheric documentation.

Neither document mentions the 048H duplication.

---

## Recent version history

No version history is published for this grid specifically. The following is what can be established.

### 2025-03/04 — parent chain consolidated
The 0.1° tier this grid nests inside switched from the EURAT01 European domain to the global GLOB01 grid on the 06Z run of **2025-03-25**, and the 0.5° GLOB05 tier was withdrawn on **2025-04-08**. FRANGP0025 itself was unaffected in domain, resolution or cadence, but its parent changed. Documented in full in the [GLOB01 entry](../../global/france/mfwam-global-france.md#recent-version-history).

### Undated — FRANXL0025 → FRANGP0025
The 0.025° grid was at some point enlarged from 51.5°N–41°N / 6°W–10.5°E to the current 53°N–38°N / 8°W–12°E, and renamed. Both names appear in documentation attached to this dataset. The change is undated and unannounced. **TBD.**

### 2024-02-02 — dataset created on data.gouv.fr
Initial publication of the MFWAM packages on the national portal.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-mfwam-resolution-0-025deg
- Model presentation (FR, pre-consolidation; GRIB2 parameter table on p.4): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-mfwam-resolution-0-025deg/20240228-172615/descriptif-modele-mfwam.pdf
- Package technical descriptif (FR, pre-consolidation): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-mfwam-resolution-0-025deg/20240228-161850/description-paquets-modele-mfwam.pdf
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live-verified 2026-08-10 against the 2026-08-10T00:00:00Z cycle (steps 001H, 048H, 051H decoded with ecCodes 2.48.0), with cycle enumeration, duplication checks and publication timestamps sampled 2026-07-26 to 2026-08-10.*
