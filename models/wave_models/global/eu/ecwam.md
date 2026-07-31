# ECWAM (ECMWF Wave Model)

## What this model is
ECWAM is the ocean wave component of ECMWF's **Integrated Forecasting System (IFS)**. It is a third-generation spectral wave model (a descendant of the original WAM) that forecasts significant wave height, wave period, and wave direction, together with wind-sea and swell partitions in the full operational output.

Within the IFS, ECWAM is two-way coupled to the atmospheric model (and, in the coupled Earth-system configuration, to the NEMO ocean model), so the sea state feeds back on surface roughness and air–sea fluxes rather than being a passive downstream product.

A reduced subset of ECWAM output is published through ECMWF's open data service at 0.25° in GRIB2, alongside the open IFS atmospheric fields.

> **Naming note:** This system was previously catalogued here as "ECMWF Wave Model (MFWAM)." That was a misnomer — ECMWF's wave model is **ECWAM**. **MFWAM** is the closely related *Météo-France* wave model, which is built on the ECWAM-IFS source code. See the two MFWAM entries for that distribution: [MFWAM Global (Copernicus)](../france/mfwam-copernicus.md) and [MFWAM GLOB01](../france/mfwam-global-france.md).

> **Scope of this entry.** This entry covers the **deterministic** wave forecast (`stream=wave`, `type=fc`). The wave ensemble (`stream=waef`, ENS-WAM) is a separate operational stream and is catalogued separately per the repository's convention that ensemble marine systems get their own entry filed alongside the deterministic sibling. See *Notes*.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Country / region:** International (intergovernmental organisation)

---

## What area it covers
- **Coverage:** Global oceans
- **Open Data grid (verified):** regular latitude–longitude, **1440 × 721**, 0.25° × 0.25°, 1,038,240 total grid points — the same grid geometry as the open IFS atmospheric fields
- **Grid origin:** first grid point 90°N / 180°, last 90°S / 179.75°, scanning mode 0 (west→east, north→south). The longitude axis begins at the dateline in the raw GRIB header; see the [IFS entry](../../../nwp_models/global/eu/ifs.md#what-area-it-covers).
- **Sea mask:** fields carry a **GRIB bitmap**. Of the 1,038,240 grid points, **665,628 are valid (64.11%)** and 372,612 are masked. The bitmap is **static** — byte-for-byte identical valid-point counts at steps 0, 24, 144 and 360 — so it represents land plus permanently ice-covered ocean, not a time-varying ice edge.

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** single regular latitude–longitude grid in the open-data distribution (the operational model runs on a quasi-regular reduced grid; the published product is interpolated to regular 0.25°)
- **Core wave model:** ECWAM (IFS wave component; third-generation WAM derivative)
- **IFS cycle:** Cycle 50r1 (operational since 12 May 2026)
- **Native horizontal resolution:** **~9 km (TCo1279) — the same grid as the atmospheric model.** Since Cycle 49r1 (12 November 2024) ECWAM's horizontal grid matches the resolution of whichever atmospheric configuration it is coupled to: ~9 km for the medium-range deterministic and ensemble forecasts, ~36 km (TCo319) for the sub-seasonal and seasonal systems. Full resolution is available via the Product Requirements Catalogue under a service agreement; the open subset is interpolated down to 0.25°.
- **Horizontal resolution (Open Data):** 0.25° (~28 km), verified — a roughly 3× coarsening from native
- **Spectral resolution:** **36 directions × 36 frequencies computed online**; the archived/output spectrum carries **36 directions × 29 frequencies**, at 6-hourly intervals. The output frequency count was cut from 36 to 29 in Cycle 49r1 to make the grid-resolution increase affordable — *online computation was not reduced*. No spectra are distributed in the open-data subset, so this is documented from ECMWF sources rather than live-verified.

> **The "wave model is coarser than the atmosphere" assumption is out of date.** It held for most of ECWAM's operational history — Cycle 41r2 (March 2016) put HRES-WAM at 0.125° (~14 km) against a 9 km atmosphere, and ENS-WAM at 0.25° against 18 km. Cycle 49r1 unified them. Anyone carrying forward a pre-2024 mental model, or a pre-2024 secondary source, will understate the native resolution by roughly a third.
- **Forecast length (verified):**
  - **360 h (15 days)** at 00 and 12 UTC
  - **144 h (6 days)** at 06 and 18 UTC
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC), all now under a single `wave` stream
- **Temporal output resolution (verified):**
  - 00 / 12 UTC: 3-hourly to +144 h, then 6-hourly +150 h to +360 h — **85 step files**
  - 06 / 18 UTC: 3-hourly to +144 h — **49 step files**
- **Observed publication latency:** whole run published in one batch, simultaneously with the deterministic atmospheric files. Measured 2026-07-30: 00z at 07:34 UTC (**T+7h34m**), 06z at 12:27 UTC (**T+6h27m**), 12z at 19:34 UTC (**T+7h34m**) — timestamps identical to the `oper` atmospheric stream of the same cycle.
- **Volume:** ~10.6 MB per step file; **~898 MB per 00/12 UTC cycle**, ~518 MB per 06/18 UTC cycle.

> ⚠️ **Corrects the previous version of this entry**, which stated 240 h at 00/12 UTC and a separate 90 h `scwv` stream at 06/18 UTC. Both figures are pre-Cycle-50r1. `scwv` was deprecated on 12 May 2026 and folded into `wave`; the forecast horizon is 360 h and 144 h respectively. ECMWF's own open-data documentation still carries the old 240 h / 90 h figures — see *Open questions*.

---

## Forcing and nesting
- **Wind forcing:** IFS atmospheric model 10 m winds, **two-way coupled** — the wave model and atmosphere exchange fields each timestep rather than the waves being forced offline. The sea state feeds back on the Charnock parameter and hence on surface roughness and air–sea momentum flux.
- **Ice forcing:** IFS sea-ice fields. Cycle 50r1 introduced **refined wave–sea-ice coupling** as one of its headline wave changes, alongside the move to the NEMO4-SI3 ocean/sea-ice core. Behaviour in the distributed fields is characterised under *Notes*.
- **Current forcing:** **Active as of Cycle 50r1.** ECMWF lists "new wave interaction with sea ice and ocean currents" among the cycle's principal changes, so surface currents from the coupled NEMO component now influence wave propagation in the operational configuration. *This resolves a TBD carried by the previous version of this entry, which asked whether current coupling was active in the deterministic run or ENS only — it is active in the coupled system generally as of 50r1.*
- **Bathymetry:** revised in Cycle 50r1. The distributed `wmb` field ranges **2.0 m to 999.0 m** — it is the model's working depth field clipped at 999 m, not true bathymetry. Anything deeper is treated as deep water by the model and reported at the cap.
- **Nested inside / parent for:** Global parent; provides lateral boundary wave spectra to several regional systems. Note that the previously asserted ECWAM→ARCWAM boundary relationship is flagged as unverified in the [MET Norway WW3 entry](../../regional/norway/met-norway-ww3.md) and should not be restated here as established.

---

## Data assimilation
- **Assimilates wave observations:** Yes
- **Observation sources:** Satellite altimeter significant wave height is the primary source. SAR wave spectra are assimilable by the same machinery. **TBD:** the current operational mission list (CFOSAT SWIM, Sentinel-1, and others) is not stated in the open-data documentation and should be confirmed against the IFS Documentation for Cycle 50r1.
- **Method:** **Optimum Interpolation (OI)** applied to altimeter wave height, following Lionello et al. (1992). The wave analysis runs within the IFS assimilation cycle. *This partially resolves a TBD in the previous version; the cadence relative to the 12-hour atmospheric window remains **TBD**.*

---

## What it provides

The open-data subset contains **13 parameters**, verified by decoding `20260730000000-24h-wave-fc.grib2` (13 GRIB messages per step file). All use ECMWF wave parameter table 140.

**Integral sea-state parameters**

| shortName | paramId | Level type | Description |
|---|---|---|---|
| `swh` | 140229 | surface | Significant height of combined wind waves and swell |
| `mwp` | 140232 | surface | Mean wave period |
| `mp2` | 140221 | surface | Mean zero-crossing wave period |
| `pp1d` | 140231 | surface | Peak wave period |
| `mwd` | 140230 | surface | Mean wave direction |
| `cdww` | 140233 | heightAboveGround | Coefficient of drag with waves |

**Significant wave height by period band** — six bands, each giving the significant height of all waves whose period falls in the stated inclusive range:

| shortName | paramId | Period band |
|---|---|---|
| `h1012` | 140114 | 10–12 s |
| `h1214` | 140115 | 12–14 s |
| `h1417` | 140116 | 14–17 s |
| `h1721` | 140117 | 17–21 s |
| `h2125` | 140118 | 21–25 s |
| `h2530` | 140119 | 25–30 s |

**Static field**

| shortName | paramId | Description |
|---|---|---|
| `wmb` | 140219 | Model bathymetry (m, clipped at 999) |

> ⚠️ **Corrects the previous version of this entry** on two counts. It listed five parameters where thirteen are distributed, omitting `cdww`, `wmb`, and all six period bands. It also stated that "wind-sea / swell partitions ... are **not** part of the free open-data subset." That is only half right: the classic wind-sea / total-swell partition triplets (`shww`/`mpww`/`mdww`, `shts`/`mpts`/`mdts`) and the 2D spectra are indeed absent, but **six period-band swell partitions are present** and are among the more useful fields in the subset for distinguishing long-period swell arrival from local wind sea.

**Not distributed in the open subset:** 2D wave spectra, wind-sea / total-swell partition triplets, Stokes drift components, wave-induced stress, peak direction, and the extreme-wave diagnostics. These require a Real-time Dissemination service agreement.

---

## Data availability
- **Is the data free?** Yes (open-data subset)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0), plus the ECMWF Terms of Use. Redistribution and commercial use permitted with attribution to ECMWF.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, `grid_ccsds` packing (CCSDS compression), `tablesVersion = 32`, bitmap present. Decoding requires ecCodes 2.42.0 or newer built with `libaec`.
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/ifs/0p25/wave/<YYYYMMDD><HH>0000-<step>h-wave-fc.grib2`
  - Example: `20260730/00z/ifs/0p25/wave/20260730000000-24h-wave-fc.grib2`

### Access channels
Identical files are served from four channels; verified 2026-07-30 for the wave stream specifically, with the `24h-wave-fc.index` object returning matching MD5 (`2863fe8bcaec...`) from the ECMWF portal, GCS and the AWS regional endpoint.

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | 2023-01-18 → present |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | 2022-01-21 → present |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | 2023-07-12 → present |

The cloud mirrors retain full archives rather than the portal's rolling window. Full access mechanics — the Azure SAS-token exchange, the GCS bucket identity, historical path-schema changes, `.index` byte-range retrieval, and the AWS `SlowDown` throttling behaviour — are documented once in the [IFS entry](../../../nwp_models/global/eu/ifs.md#data-availability) rather than duplicated here.

**Historical note for archive crawlers:** files from cycles before 12 May 2026 use `scwv` in both the directory path and the filename for the 06/18 UTC runs. Cross-cycle workflows reading the mirrors must branch on date.

---

## Notes

- **Verified 2026-07-30.** Directory structure, step lists, grid geometry, bitmap behaviour, parameter inventory and encoding, static-field duplication, publication latency and mirror parity all confirmed by direct inspection of live GRIB2 files from the 00z, 06z and 12z runs.

- **Sea ice is handled by energy suppression, not masking.** The bitmap is static, so seasonally ice-covered water stays inside the valid mask and simply returns near-zero wave height when ice is present. A meridional transect at 0° E on the 00z +24 h field gives 1.75 m at 70°N, 0.95 m at 78°N, 0.15 m at 80°N, then 0.01 m from 82°N to 86°N, with 88°N masked out entirely. The Southern Hemisphere behaves the same way: 1.50 m at 60°S falling to 0.03 m at 67°S, with 70°S masked. **10.55% of all valid sea points carry SWH below 0.05 m** in this field. Basin statistics, climatologies, and extreme-value fits computed over the raw mask will be biased low unless these ice-suppressed cells are screened out — they are not calm seas, they are absent seas.

- **Coastal cells are frequently masked.** At 0.25° the coastline is coarse and many nearshore points fall on the land side of the mask. Verified example: 43.0°N, 8.0°W off Galicia returns missing, while 43.5°N, 9.5°W returns 0.72 m. The open subset is unsuitable for nearshore work without a finer regional model; see the Copernicus Marine regional wave products catalogued in [`COPERNICUS.md`](../../../../COPERNICUS.md).

- **`wmb` is a static field re-emitted at every step.** Bathymetry values are bit-identical between step 0 and step 360 — only the GRIB step key differs. It is duplicated across all 85 steps of a 00/12 UTC cycle, roughly **42.8 MB of redundancy per cycle**. Fetch it once from any step; skip it in the other 84.

- **`generatingProcessIdentifier = 109` distinguishes the wave stream** from the atmospheric `oper` stream, which uses 161. Useful for sorting mixed archives where filenames have been lost — the same trick documented for DWD's [GWAM/EWAM](../../regional/germany/ewam-dwd.md). **Note the limit:** [ENS-WAM](./ecwam-ens.md) also uses 109, so this key separates the wave family from the atmosphere but not deterministic from ensemble. Use `mars.stream` or `mars.type` for that.

- **The wave stream is already fully GRIB2-native.** All 13 parameters decode with `localTablesVersion = 0`, meaning WMO-standard discipline/category/number encoding throughout. Unlike the atmospheric `oper` stream — which still has six locally-encoded parameters including `tp` — **ECWAM's open-data subset should be unaffected by the Cycle 50r2 parameter-encoding migration.** See the [IFS entry](../../../nwp_models/global/eu/ifs.md#upcoming-changes) for the atmospheric picture.

- **Open subset vs. full operational model.** The free data is a reduced-parameter, 0.25° extract of ECWAM, analogous to the open IFS atmospheric subset. Full-resolution, full-parameter wave products (including spectra and partitions) require a Real-time Dissemination service agreement.

- **Relationship to MFWAM (shared codebase).** Météo-France's MFWAM runs on the ECWAM-IFS source code (documented as ECWAM-IFS-38R2 in the Copernicus Marine global product). The lineage is bidirectional: the Ardhuin/Météo-France wind-input and deep-water dissipation parametrizations were adopted *back into* ECWAM in IFS Cycle 46r1 (June 2019). ECWAM and MFWAM are cross-pollinated relatives rather than a simple fork — see [MFWAM Global (Copernicus)](../france/mfwam-copernicus.md) and [MFWAM GLOB01](../france/mfwam-global-france.md).

- **Companion ensemble stream — separate entry.** ECMWF publishes an open wave ensemble under `stream=waef` (ENS-WAM) at all four cycles, carrying the same 13 parameters. Post-Cycle-50r1 it contains **50 perturbed members only**; the wave control forecast moved into this deterministic `wave` stream at the same time the atmospheric control moved into `oper`. Note that ECMWF's MARS-to-filename mapping table still lists `waef / cf → waef / ef`, implying a control inside the ensemble file — live decoding contradicts this. Catalogued separately as [ENS-WAM](./ecwam-ens.md), using this same wave template with its **Ensemble configuration** section retained.

- **Atmospheric siblings.** Driven by and two-way coupled with [IFS](../../../nwp_models/global/eu/ifs.md); the wave ensemble's spread derives from [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md). Coupled ocean-surface fields (`zos`, `sithick`, `sve`, `svn`) are delivered in the *atmospheric* `oper` and `enfo` streams rather than in the wave stream.

- **Upcoming resolution change.** ECMWF has announced plans to extend the open subset toward native 9 km resolution later in 2026 with a 2-hour latency; a longer-term move to 0.125° (~14 km) to match the ERA6 grid has also been signalled. Neither is live — as of 2026-07-30 the resolution directory contains only `0p25`.

---

## Open questions / pending verification
- **ECMWF's open-data documentation still states 240 h (00/12) and 90 h (06/18) for `stream=wave`.** The live archive serves 360 h and 144 h. Believed to be stale pre-50r1 text — the same discrepancy affects the atmospheric `oper` stream. To be raised with ECMWF support alongside the queries listed in the [IFS](../../../nwp_models/global/eu/ifs.md#open-questions--pending-verification) and [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md#open-questions--pending-verification) entries.
- **Current operational altimeter/SAR mission list** for wave assimilation — **TBD**.
- **Wave analysis cadence** relative to the 12-hour atmospheric assimilation window — **TBD**.
- **Whether the 999 m bathymetry cap** is a distribution artefact or the model's internal deep-water treatment — worth confirming, since it affects anyone using `wmb` as a depth reference rather than as a model diagnostic.

---

## Recent version history

### IFS Cycle 50r1 — 12 May 2026 (current)
- **New wave interaction with sea ice and ocean currents** — wave propagation now responds to surface currents from the coupled NEMO4-SI3 component, and wave–sea-ice coupling was refined.
- **Revised wave model bathymetry.**
- **No change in horizontal resolution, vertical resolution, or forecast steps** for the wave component.
- **Stream consolidation:** `scwv` deprecated; 06/18 UTC wave data moved under `stream=wave`. The wave control forecast moved into the `wave` stream, mirroring the atmospheric control's move into `oper`.
- See repository [`STATUS.md`](../../../../STATUS.md) for the full IFS 50r1 / AIFS v2 changeset.

### March 2024
- Open-data resolution increased to 0.25° (~28 km); parameter subset expanded. **Note:** archive inspection shows 0.25° files present from **1 February 2024**, ahead of the announcement date — see the [IFS entry](../../../nwp_models/global/eu/ifs.md#archive-path-schema-changes-verified-against-the-aws-mirror).

### IFS Cycle 49r1 — 12 November 2024
- **Wave grid revised to match the atmospheric resolution in all forecasts** — medium-range ECWAM grid spacing reduced to ~9 km (TCo1279) from the previous 0.125°, and sub-seasonal ECWAM to ~36 km (TCo319). This is the change that eliminated the long-standing resolution gap between ECWAM and the atmospheric model.
- **Output spectra reduced from 36 to 29 frequencies** to offset the cost of the grid increase. Online spectral computation remained at 36 × 36.
- New wind-input parametrizations including a gravity–capillary model and non-linear growth rates, modulating the drag coefficient with wind speed and addressing a known underestimation of extreme ocean wind speeds.
- New sea-state-dependent heat and moisture fluxes.

### IFS Cycle 46r1 — June 2019
- New wind-input and deep-water dissipation parametrizations (Ardhuin et al., via Météo-France) incorporated into ECWAM.

### IFS Cycle 41r2 — 8 March 2016
- HRES-WAM resolution increased from 0.25° to 0.125°; ENS-WAM (days 0–15) from 0.5° to 0.25°. Both remained coarser than their atmospheric counterparts (9 km and 18 km respectively) until Cycle 49r1.

---

## Verification record
Established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of the `wave` stream across the 00z, 06z and 12z cycles for step lists and forecast horizons
- ecCodes 2.48.0 decode of the full `24h-wave-fc.grib2` file (13 messages) for grid geometry, bitmap, packing, parameter inventory, paramIds, level types and local-table status
- Byte-range extraction of `swh` at steps 0, 24, 144 and 360 to test whether the sea mask varies with lead time
- Value sampling of `swh` along meridional transects at 0° E in both hemispheres, plus coastal and open-ocean probes, to characterise ice suppression and coastal masking
- Byte-range extraction and array comparison of `wmb` at steps 0 and 360 to confirm static-field duplication
- `Last-Modified` header sampling across three cycles for publication latency
- MD5 comparison of the same wave object across the ECMWF portal, GCS and the AWS regional endpoint

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

**Not live-verified:** native horizontal resolution, spectral discretisation, assimilation method, and coupling behaviour cannot be established from the open-data subset, which distributes only interpolated integral parameters. These are sourced from ECMWF's Forecast User Guide, the Cycle 49r1 and 50r1 newsletters, and IFS Documentation Part VII, and are labelled as such above.

---

## Official documentation
- ECMWF open data (real-time forecasts from IFS and AIFS): https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Open data licence and parameter subset: https://www.ecmwf.int/en/forecasts/datasets/open-data
- Forecast User Guide §2.3 — Ocean Wave Model ECWAM (resolution and spectral discretisation): https://confluence.ecmwf.int/display/FUG/Section+2.3+Ocean+Wave+Model+-+ECWAM
- ECMWF Newsletter 181 (Cycle 49r1 wave grid unification): https://www.ecmwf.int/en/newsletter/181/earth-system-science/ifs-upgrade-improves-near-surface-wind-and-temperature
- IFS Documentation Part VII — ECMWF Wave Model: https://www.ecmwf.int/sites/default/files/elibrary/2023/81373-ifs-documentation-cy48r1-part-vii-ecmwf-wave-model.pdf
- IFS Cycle 50r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+50r1
- ECMWF Newsletter 185 (Cycle 50r1 overview): https://www.ecmwf.int/en/newsletter/185/earth-system-science/upgrade-ifs-cycle-50r1
- IFS Cycle 50r1 / AIFS v2 webinar (wave changes summarised): https://events.ecmwf.int/event/531/
- Dissemination file-naming changes 49r1 → 50r1: https://confluence.ecmwf.int/display/FCST/Dissemination+file-naming+changes+from+IFS+Cycle+49r1+to+50r1
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/

### Key references
- WAMDI Group (1988). *The WAM Model — A Third Generation Ocean Wave Prediction Model.* Journal of Physical Oceanography, 18, 1775–1810.
- Lionello, P., Günther, H., and Janssen, P. A. E. M. (1992). *Assimilation of altimeter data in a global third-generation wave model.* Journal of Geophysical Research, 97(C9), 14453–14474.
- Janssen, P. A. E. M. (2004). *The Interaction of Ocean Waves and Wind.* Cambridge University Press.
