# Met Office UK Wave (NWS Wave — `uk_wav_det`, AMM15SL2)

## What this model is
The Met Office UK Wave model is an operational deterministic wave forecast for the
North-West European Shelf, run four times daily within the Met Office operational
NWP suite and published on AWS Open Data as *Met Office NWS Wave model on a 2-year
rolling archive*. It is a WAVEWATCH III configuration on the AMM15 1.5 km shelf
domain, forced by the Met Office Global Unified Model, by NEMO AMM15 surface
currents, and by boundary spectra from the Met Office global wave model.

**This is a distinct system from the coupled shelf-seas wave product documented in
[AMM15-WW3](./amm15-ww3-uk.md).** They share the AMM15 domain and delivery grid but
differ in configuration, cycling, forecast length, forcing arrangement, and field
set — see *Relationship to other wave products* below. Getting these two confused is
easy, because both are Met Office, both are "NWS wave", and both are on AWS.

---

## Who runs it
- **Organization:** UK Met Office
- **Country / region:** United Kingdom
- **Production context:** Met Office operational NWP suite (`mosg__model_suite_version = 47`
  as of July 2026), not the Operational Marine Post-Processing Shelf-Seas suite (MaPP-SS)
  that produces the coupled FOAM-NWSW product.

---

## What area it covers
- **Coverage:** North-West European Shelf and adjacent North-East Atlantic
- **Domain bounds (live-verified):** −16.0000° to 12.99997° longitude, 46.0000° to
  62.7432° latitude
- **Grid dimensions:** 958 × 1240 (longitude × latitude) — identical delivery grid to the
  [AMM15-WW3 wave](./amm15-ww3-uk.md) and [NWS Ocean](../../../ocean_models/regional/uk/amm15-nws.md) products
- **Regions covered:** North Sea, Irish Sea, English Channel, Celtic Sea, Bay of Biscay,
  adjacent deep-water North-East Atlantic
- **Sea-point fraction:** ~62% of grid cells carry valid data; the remainder is land-masked

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** Delivered on a single regular latitude–longitude grid (WGS84;
  `grid_mapping_name = latitude_longitude`, semi-major axis 6378137.0,
  inverse flattening 298.257223563). The underlying model uses the Met Office
  Spherical Multiple-Cell (SMC) variable-resolution wave grid — TBD whether this
  configuration uses the same 3–1.5 km SMC arrangement as AMM15-WW3.
- **Core wave model:** WAVEWATCH III v7.13, Met Office local branch
  (`mowv__model_version`, live-verified)
- **Base configuration:** `AMM15SL2` (`mowv__base_configuration`)
- **Model configuration ID:** `uk_wav_det`; grid domain `uk_wave`, grid type `standard`,
  grid version 1.0.0
- **Horizontal resolution:** ~1.5 km — 0.030303° longitude × 0.0135135° latitude
  (exactly ¹⁄₃₃° × ¹⁄₇₄°)
- **Forecast length:** **60 hours** (`mosg__forecast_run_duration = PT60H`;
  live-verified `forecast_period` spans T+0 h to T+60 h). Note this contradicts the AWS
  registry description, which states T-48 h to T+143 h — see *Notes*.
- **Update frequency / cycles:** 4× daily — 00, 06, 12, 18 UTC
- **Temporal output resolution:** Hourly (61 timesteps per cycle)
- **Analysis / hindcast component:** None in this feed. Every cycle begins at T+0;
  the T-48 h analysis window belongs to the coupled MaPP-SS product, not this one.
- **Typical availability:** ~2 h 45 min after cycle time (00Z cycle files carry
  `date_created = 02:42 UTC`, live-verified)

---

## Forcing and nesting
- **Wind forcing:** Met Office Global Unified Model 10 m winds
  (`mowv__parent_model_source = Met Office Operational Suite; Global UM`)
- **Current forcing:** NEMO AMM15 surface currents (one-way forcing — this configuration
  is forced, not two-way coupled, distinguishing it from AMM15-WW3)
- **Boundary conditions:** Global WW3 — the Met Office
  [global wave model (GloWave)](../../global/uk/ukmo-wave.md)
- **Full forcing declaration (live-verified):** `mowv__forcing_data_source =
  Met Office Global UM, NEMO AMM15, Global WW3`
- **Parent model lag:** cycle-dependent. The 12 UTC cycle used the same-cycle 12 UTC
  Global UM run; the 00 UTC cycle used the previous day's 18 UTC run (6 h lag).
  Live-verified on 2026-07-26; **TBD** whether this pattern is systematic.
- **Ice forcing:** Not applicable (shelf-seas domain)

---

## Data assimilation
- **Assimilates wave observations:** No documented wave data assimilation. **TBD** —
  neither the AWS registry nor the file metadata mentions an assimilation scheme.

---

## What it provides
27 files per validity block, carrying 28 distinct variables. All fields are surface,
hourly, instantaneous (`cell_methods = time: point`), on the full 958 × 1240 grid.
Live-verified inventory (2026-07-26 00 UTC cycle):

**Combined sea state**
- `VHM0` — spectral significant wave height (Hm0) [m]
- `VMDR` — mean wave direction [degree]
- `VTPK` — peak period [s]
- `VTM01` — mean period from first frequency moment [s]
- `VTM02` — mean period from second frequency moment [s]

**Wind-wave partition**
- `VHM0_WW` [m], `VMDR_WW` [degree], `VTPK_WW` [s], `VSPR_WW` — directional spread
  [degree], `VPEP_WW` — peak energy [m² s rad⁻¹]

**Swell partitions 1–3** (primary, secondary, tertiary — same five fields each)
- `VHM0_SW{1,2,3}` [m], `VMDR_SW{1,2,3}` [degree], `VTPK_SW{1,2,3}` [s],
  `VSPR_SW{1,2,3}` [degree], `VPEP_SW{1,2,3}` [m² s rad⁻¹]

**Surface wind on the wave grid** (both in `wave_grid_10m_wind`)
- `WSPD` — 10 m wind speed [m s⁻¹]
- `WDIR` — 10 m wind from-direction [degrees]

**Static**
- `deptho` — sea floor depth below geoid [m], in `wave_grid_depth`; single timestep,
  republished once per cycle rather than as a separate static dataset

**Not distributed in this feed:** Stokes drift (`VSDX`/`VSDY`), `VTM10`, peak-direction
`VPED`, and per-partition mean periods (`VTM01_WW`, `VTM01_SW1`, `VTM01_SW2`) — all of
which the coupled MaPP-SS feed does carry. Conversely, this feed uniquely adds tertiary
swell, directional spread, peak energy, and the 10 m wind field.

---

## Data availability
- **Is the data free?** Yes — anonymous S3 access, no registration or account required
- **License:** **CC BY-SA 4.0** (British Crown copyright 2025, the Met Office).
  Share-alike obligation applies to derivatives in addition to attribution — the same
  licence as the Met Office atmospheric and NWS Ocean AWS datasets, and notably
  stricter than plain CC BY 4.0.
- **Is the data downloadable?** Yes — direct HTTPS or S3
- **Data formats:** NetCDF-4 / HDF5; **CF-1.7 and ACDD-1.3 conventions** (live-verified)
- **Variable packing:** int16 with `scale_factor` / `add_offset` and
  `_FillValue = -32767` (e.g. `VHM0` uses `scale_factor = 0.002`). Readers that ignore
  packing attributes will return raw integers.
- **Time coordinate:** `seconds since 1970-01-01T00:00:00`. Files also carry CF
  `forecast_period` (seconds) and `forecast_reference_time`.
- **Bucket:** `s3://met-office-nws-wave-model-data/` (region `eu-west-2`)
- **Path template:** `nws-wave/{YYYY}/{MM}/{DD}/T{HH}00Z/`
- **File naming:** `b{YYYYMMDD}T{HHMM}Z_hi{YYYYMMDD}T{HHMM}Z-wave_uk_standard_v1-level_1-{variable}.nc`
  — first timestamp is the bulletin (cycle) time, second is the start of the validity
  block. **The validity blocks are cycle-relative, not calendar days**: a 12 UTC cycle
  produces `hi...T1200Z` blocks, so the three blocks per cycle cover T+0–23 h (24 steps),
  T+24–47 h (24 steps), and T+48–60 h (13 steps).
- **Volume:** 79 files and ~0.70 GB per cycle; ~2.8 GB/day across four cycles.
  Individual files run ~7–14 MB.
- **Archive retention:** advertised as a 2-year rolling archive. In practice the archive
  **begins 13 March 2025** (earliest object: `nws-wave/2025/03/13/T1200Z/`), so as of
  July 2026 it holds ~16 months and is still filling toward the full window.
- **New-object notifications:** SNS topic
  `arn:aws:sns:eu-west-2:633885181284:met-office-nws-wave-model-data-object_created`
- **Official access:**
  - AWS registry: https://registry.opendata.aws/met-office-nws-wave/
  - Browse: https://met-office-nws-wave-model-data.s3.eu-west-2.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://met-office-nws-wave-model-data/`
- **DOI:** TBD (none on the registry)

---

## Notes
- **Registry description contradicts the data.** The AWS registry states the model runs
  "from T-48h to T+143h". Live inspection of every cycle on 2026-07-20 and 2026-07-26
  shows 79 files spanning exactly T+0 to T+60 h, with `mosg__forecast_run_duration = PT60H`
  stamped in every file. There is no hindcast segment in this bucket. The registry text
  appears to have been adapted from the coupled shelf-seas product's specification.
  **Trust the file metadata.**
- **Product-sheet mismatch.** The Met Office *NWS-Wave* product sheet (FOAM-NWSW /
  AMM15, Crown Copyright 2023) describes a different feed: daily delivery ~08 UTC,
  T-48 → T+167, Stokes drift, and maximum wave/crest height. None of that applies here.
  That sheet documents the MaPP-SS coupled product — see the cross-references below.
- **Two Met Office AWS wave feeds exist**, and they are easy to conflate:
  | | This entry | Coupled MaPP-SS feed |
  |---|---|---|
  | Bucket | `met-office-nws-wave-model-data` | `met-office-nws-ocean-model-data` |
  | Filename prefix | `b{...}Z_hi{...}Z-wave_uk_standard_v1-` | `level1_wave_amm15_NWS_WAV_` |
  | Cycles | 4× daily | 1× daily (00 UTC) |
  | Span | T+0 → T+60 h | T-48 → T+167 h |
  | Packaging | one variable per file | all variables in one file per validity day |
  | WW3 version | v7.13 | v7.12 |
  | Suite version | 47 | 44 |
  | Ocean linkage | forced by NEMO AMM15 | two-way coupled to NEMO AMM15 |
- **Sheet fields absent from the coupled feed too.** The product sheet lists
  `wave_maximum_height` and `wave_maximum_crest_height` (VMXL/VCMX). Neither appears in
  live coupled-feed files as of July 2026 — a documentation-vs-reality gap on that
  product, noted here because the sheet is commonly cited for both.
- **Static depth is republished per cycle** rather than offered as a one-off static
  dataset; pipelines should fetch it once and skip it thereafter.

---

## Relationship to other wave products
- **[AMM15-WW3 (NWS wave, Copernicus + MaPP-SS)](./amm15-ww3-uk.md)** — the coupled
  shelf-seas sibling on the same domain and delivery grid, two-way coupled to NEMO
  AMM15 via OASIS3-MCT, distributed through Copernicus Marine
  (`NWSHELF_ANALYSISFORECAST_WAV_004_014`) and in the NWS Ocean AWS bucket. The closest
  relative to this entry and the one most likely to be mistaken for it.
- **[UK Met Office Global Wave Model (GloWave)](../../global/uk/ukmo-wave.md)** — global
  parent; supplies this configuration's boundary spectra.
- **[Met Office NWS Ocean / FOAM-NWSO (AMM15)](../../../ocean_models/regional/uk/amm15-nws.md)** —
  the shelf physics whose surface currents force this wave run.
- **Atmospheric driver:** [Met Office Global deterministic](../../../nwp_models/global/uk/ukmo-global.md).
  Note the driver is the *global* model, not [UKV](../../../nwp_models/regional/uk/ukv.md),
  despite the "UK Wave" naming.
- **[IBIWAM](../spain/ibiwam.md)** — overlapping regional coverage in the Bay of Biscay,
  Celtic Sea, and western Channel.

---

## Official documentation
- AWS registry: https://registry.opendata.aws/met-office-nws-wave/
- Met Office external data channels: https://www.metoffice.gov.uk/services/data/external-data-channels
- Met Office wave models: https://www.metoffice.gov.uk/services/data
- Coupled forecasting development: https://www.metoffice.gov.uk/research/weather/ocean-forecasting/coupled-development
- SMC grid reference: Li and Saulter (2014)
