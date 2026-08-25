# Met Office NWS Ocean (FOAM-NWSO — AMM15, Atlantic Margin Model 1.5 km)

## What this model is
The Met Office NWS Ocean product is a **regional physical ocean analysis and 7-day forecast for the North-West European Shelf**, run on the **Atlantic Margin Model 1.5 km (AMM15)** domain. The science configuration is referred to as **FOAM-NWSO**. It predicts temperature, salinity, and circulation for the waters surrounding the UK at ~1.5 km resolution, produced by the Met Office Operational Marine Post-Processing Shelf-Seas suite (MaPP-SS).

AMM15 is a NEMO shelf-seas configuration with explicit tides, **two-way online coupled to a WAVEWATCH III wave model** via OASIS3-MCT. The physics and wave halves are delivered on the same horizontal grid and share a static file set.

The system is distributed through **two channels**:

- **Met Office AWS Open Data** as `metoffice_foam1_amm15_NWS`, anonymous access, CC BY-SA 4.0
- **Copernicus Marine** as `NWSHELF_ANALYSISFORECAST_PHY_004_013`, registration required

**The AWS channel is the superset.** It carries in-situ temperature and water-column maximum-current diagnostics that appear in neither the Copernicus PUM nor the live Copernicus datasets — see *Data availability*.

> **The Copernicus product identifier has not always carried this system.** Between September 2023 and November 2025 `NWSHELF_ANALYSISFORECAST_PHY_004_013` was operationally an **extraction of the IBI system**, not AMM15. The Met Office re-introduced its own product at the November 2025 upgrade. This mirrors the lineage of the [AMM15-WW3 wave product](../../../wave_models/regional/uk/amm15-ww3-uk.md) exactly, and it means the Copernicus rolling archive spans two different models. See *Version history*.

**Bucket caveat:** on AWS the physics fields are **co-located in the same S3 bucket** as the AMM15-WW3 wave product (the wave files share the `NWS`/`amm15` naming). This makes the ocean bucket easy to mistake for the Met Office's only AWS wave source. It is not. A separate bucket, `met-office-nws-wave-model-data`, carries a **different** wave configuration ([Met Office UK Wave](../../../wave_models/regional/uk/uk-wave-metoffice.md)) on the same domain — 4 cycles daily to T+60 h, forced rather than coupled. The wave files in *this* bucket are the coupled MaPP-SS product only.

*AWS channel live-verified from files. Copernicus channel verified 2026-08-25 from the PUM (Issue 3.0, November 2025), Marine Data Store STAC records, and live ARCO Zarr metadata.*

---

## Who runs it
- **Production Unit:** UK Met Office — also the Copernicus Marine producer of record ("Met Office (UK)")
- **Country:** United Kingdom
- **Programme or coordinating body:** Met Office Operational Marine Post-Processing Shelf-Seas suite (MaPP-SS); Copernicus Marine North West Shelf MFC
- **Copernicus product identifier:** `NWSHELF_ANALYSISFORECAST_PHY_004_013` — **confirmed**, not inferred. The PUM gives `System Name: AMM15`, and its file-size table and annex both use the `metoffice_foam1_amm15_NWS_…` naming convention found in the AWS bucket (the annex header reads `ncdump -h metoffice_foam1_amm15_NWS_TEM_b20250526_dm20250524.nc`). Grid dimensions and the 33-level list match the AWS files exactly.
- **Role in any larger system:** Regional downscaling of a Met Office global **ORCA12** ocean over the NW European Shelf; two-way coupled to the AMM15-WW3 regional wave model; supplies the western boundary of the Copernicus Baltic physics product, and takes its own Baltic boundary from that product in return.

---

## What area it covers
- **Coverage:** Atlantic North-West European Shelf
- **Domain bounds:** 16°W–13°E, 46°N–62.74°N (verified: lat 46.0000–62.7432, lon −16.0000–13.0000)
- **Grid dimensions:** 958 × 1240 (lon × lat) — verified from live files on both channels; identical delivery grid to the [AMM15-WW3 wave product](../../../wave_models/regional/uk/amm15-ww3-uk.md)
- **Grid spacing (live-verified):** 0.030303030° longitude × 0.013513511° latitude, i.e. exactly 1/33° × 1/74°
- **Special masked or excluded regions:** **all grid points in the Baltic, and in the Kattegat south of 57.25°N, are masked** (Baltic covered by BAL MFC products). A minimum-depth floor applies in the underlying NEMO configuration — see *Notes*.

The domain deliberately extends beyond the continental shelf so that the model's boundary region sits in the deep water of the adjacent North-East Atlantic. The focus region is the shelf seas proper: North Sea, Irish Sea, English Channel, Celtic Sea, and Bay of Biscay.

---

## Basic details
- **Model type:** Regional shelf-seas ocean physics; deterministic; two-way coupled to a regional wave model
- **Core ocean model:** **NEMO 3.6** (Madec et al., 2017), AMM15 configuration
- **Sea ice model:** None (shelf-seas domain)
- **System name:** FOAM-NWSO (AMM15)
- **Horizontal resolution (delivered):** **1.9 ± 0.4 km longitude × 1.5 km latitude**, on a regular 1/33° × 1/74° grid. Longitudinal spacing varies with latitude (~1.3 km at 46°N, ~1.5 km at 63°N); latitudinal spacing is constant at 1.5 km.
- **Horizontal grid (native):** **1/60° with an Euler rotation** applied to the spherical-polar grid, placing a pseudo-equator through the domain and giving ~1.8 km spacing both zonally and meridionally (Guihou et al. 2017). The delivered regular grid is interpolated from this.
- **Vertical levels (delivered):** **33 standard IOC geopotential levels, 0–5000 m** (surface, 3, 5, 10, 15, 20, 25, 30, 40, 50, 60, 75, 100, 125, 150, 175, 200, 225, 250, 300, 350, 400, 450, 500, 550, 600, 750, 1000, 1500, 2000, 3000, 4000, 5000 m) — live-verified on both channels. **The surface level is not interpolated**; it is the first model level, 1 m thick where bathymetry exceeds 50 m and thinner where it is shallower.
- **Vertical coordinate (native):** **hybrid S-σ-z, 51 levels almost everywhere**, with a stretching function maintaining near-uniform vertical resolution at the surface. Native cell thickness ranges from **0.3 m** (bathymetry < 50 m) to **99 m** (bathymetry > 4000 m). All delivered 3D fields except `bottomT` are interpolated from these 51 native levels to the 33 fixed depths.
- **Forecast length:** **7 days (168 h) forecast plus 2 analysis days**, giving 9 validity days per bulletin (T−48 h → T+168 h). Raised from 5 days at the November 2024 upgrade; the current 7-day horizon dates from the November 2025 Met Office re-introduction.
- **Update frequency:** Once daily
- **Production cycles:** 00Z (single `T0000Z` folder per day on AWS)
- **Target delivery time:** PUM target **12:00 UTC**; actual delivery usually **06:40–10:00 UTC**, consistent with the ~08:30 UTC observed on AWS
- **Temporal output resolution:** Daily mean (25-hour mean), hourly instantaneous, and quarter-hourly (15-min) instantaneous for surface currents and SSH
- **Archive availability:** 2-year rolling on both channels — but see the lineage warning in *Version history*
- **Bathymetry source:** **EMODnet 2015**
- **Initial conditions (system spinup):** the model run started **10 January 2016** from a long simulation

---

## Forcing
- **Atmospheric forcing:** Met Office Global Unified Model (MetUM) surface fields (one-way)
- **River runoff:** **daily climatology of gauge data averaged over 1980–2014** — not real-time. UK data processed from raw records supplied by the Environment Agency, SEPA, the Rivers Agency (Northern Ireland), and the National River Flow Archive (personal communication, Sonja M. van Leeuwen, CEFAS, 2016). Major rivers missing from that set — notably along the French and Norwegian coasts — use the same climatology as AMM7 (Vörösmarty et al. 2000; Young and Holt 2007). A meaningful limitation for freshwater-sensitive applications.
- **Lateral boundary conditions:**
  - **Atlantic** (T, S, SSH, barotropic u and v): **Met Office Operational ORCA12 forecast implementation**
  - **Baltic** (T, S, barotropic u and v): **`BALTICSEA_ANALYSISFORECAST_PHY_003_006`** — see *Relationship to other entries* for the reciprocal dependency this creates
- **Tidal forcing:** explicit, **11 constituents — M2, S2, N2, K2, K1, O1, P1, Q1, M4, MS4, MN4**. Applied both at the open boundary via **Flather radiation conditions** (Flather 1976) and as an **equilibrium tide**. Amplitude and phase from the TOPEX/Poseidon cross-over solution (Egbert and Erofeeva 2002; TPXO7.2, Atlantic Ocean 2011-ATLAS) at 1/12°.
- **Ice forcing or coupling:** N/A

---

## Coupling
**Two-way online ocean–wave coupling via OASIS3-MCT, at hourly coupling frequency.** The PUM specifies the exchanged effects:

- Modification of water-side surface stress on the ocean by wave growth and dissipation
- Stokes–Coriolis force in the momentum equation
- Wave-height-dependent ocean surface roughness
- The wave model is forced by the ocean surface currents

The wave counterpart is **[AMM15-WW3](../../../wave_models/regional/uk/amm15-ww3-uk.md)**, whose native grid is a Spherical Multiple Cell grid at 3–1.5 km with sub-grid blocking cells for islands and headlands (Chawla and Tolman 2008), delivered on this product's regular grid. The two products are distributed together on AWS and **share the same static bathymetry, land-sea mask, and MDT** on Copernicus.

> **The physics PUM's wave model version is stale.** `CMEMS-NWS-PUM-004-013` (physics, Issue 3.0) states **WAVEWATCH III v4.18**; `CMEMS-NWS-PUM-004-014` (wave, Issue 3.0) states **v7.1**; and the wave PUM's own reference list cites Tolman (2014), the **v4.18** manual. Documentation alone cannot settle this.
>
> **The data does.** Live AWS wave files stamp **`WAVEWATCH-III v7.12`** in their global attributes, as recorded in the [AMM15-WW3 entry](../../../wave_models/regional/uk/amm15-ww3-uk.md). Treat v7.12 as authoritative and the physics PUM's v4.18 as text carried forward from an earlier configuration. Another instance of live verification correcting current documentation.

---

## Data assimilation
- **DA scheme:** **3D-Var FGAT, NEMOVAR**
- **Assimilated observations:**
  - In-situ and satellite (L2/L3) sea surface temperature
  - Satellite sea level anomaly from the CMEMS SL-TAC
  - Temperature and salinity subsurface profiles from GTS and the CMEMS INS-TAC

---

## What it provides
Variable names (NetCDF), verified from live files on both channels. **The AWS distribution carries more variables than either the 2023 product sheet or the Copernicus product** (marked † below — AWS-only).

### Daily-mean (25-hour mean)
- `thetao` — potential temperature [°C] (3D, 33 levels) — filetype `TEM`
- `t0` — **in-situ** temperature [°C] (3D) — filetype `TEMPIS` †
- `so` — salinity [PSU] (3D) — `SAL`
- `uo`, `vo`, `wo` — eastward/northward/upward current [m s⁻¹]; `ubar`, `vbar` — barotropic currents — `CUR`
- `bottomT` — bottom potential temperature [°C] — `BED`
- `mlotst` — mixed-layer thickness [m] — `MLD`

### Hourly instantaneous
- `TEM`/`SST`/`BED` (potential temperature: full column / surface / bottom), `SAL`/`SSS` (salinity: full column / surface), `CUR`/`SSC` (currents: full column / surface), `SSH` (`zos`), `MLD`
- `uomax` (`MAXCURU`), `vomax` (`MAXCURV`) — max current velocity in the water column [m s⁻¹] †
- `zomax` (`ZMAXCUR`) — depth of max current in the water column [m] †

### Quarter-hourly (15-min) instantaneous
- `SSC` (surface currents `uo`, `vo`), `SSH` (`zos`) — surface only (96 timesteps/day, verified)

### Static file
- Land-sea mask (`mask`), `depth`, bathymetry (`deptho`), mean dynamic topography (`mdt`), and `deptho_lev_interp` — the deepest wet grid point **of the interpolated 3D fields**. Shared with the AMM15-WW3 wave product.

> **`bottomT` is output directly from the native model and is not interpolated.** Every other 3D field is interpolated from the 51 native S-σ-z levels to the 33 standard depths; `bottomT` is not. The PUM notes the consequence: the seabed value and the lowest wet point of the interpolated 3D temperature field **can differ**. Use `deptho_lev_interp` to mask `thetao`, not `bottomT`.

---

## Data availability

### Channel comparison

| | **Met Office AWS Open Data** | **Copernicus Marine** |
|---|---|---|
| Registration | None (anonymous S3) | Required (free) |
| Licence | **CC BY-SA 4.0** (share-alike) | Copernicus Marine Service licence |
| Extra variables | **`TEMPIS`, `MAXCURU`, `MAXCURV`, `ZMAXCUR`** | Not distributed |
| Structure | Per-variable files in one cycle folder | 16 single-purpose datasets + 3 static variants |
| Formats | NetCDF-4, CF-1.8 / ACDD-1.3 | NetCDF-4 CF-1.7; Zarr (ARCO) |
| Archive | 2-year rolling | 2-year rolling (live extent from 2023-09-29) |

**AWS is the superset.** The in-situ temperature and water-column maximum-current diagnostics are absent from the Copernicus PUM's dataset table *and* from live Copernicus Zarr metadata. This inverts the usual assumption that the Copernicus copy is canonical — anyone needing those diagnostics must use AWS, and the share-alike obligation comes with them.

### Channel 1 — Met Office AWS Open Data
- **Is the data free?** Yes — anonymous S3 access, no registration
- **License:** **CC BY-SA 4.0** (British Crown copyright 2025, Met Office). Share-alike obligation applies (derivatives under the same licence) in addition to attribution.
- **Data formats:** NetCDF-4; **CF-1.8 and ACDD-1.3 conventions** (verified from live files)
- **Product identifier:** `metoffice_foam1_amm15_NWS`; AWS registry: *Met Office NWS Ocean model on a 2-year rolling archive*
- **Dataset identifiers (per-variable files):** `TEM`, `TEMPIS`†, `SAL`, `CUR`, `BED`, `MLD`, `SST`, `SSS`, `SSC`, `SSH`, `MAXCURU`†, `MAXCURV`†, `ZMAXCUR`†. Frequencies: `dm` (daily mean), `hi` (hourly instant), `qh` (quarter-hourly instant). Standard variables carry 9 validity-day files per bulletin (2 analysis + 7 forecast); the max-current diagnostics and in-situ temperature appear with fewer files (5–6), suggesting forecast-focused coverage — **exact leadtime coverage TBD**.
- **File naming:** `metoffice_foam1_amm15_NWS_{VARIABLE}_b{YYYYMMDD}_{FREQ}{YYYYMMDD}.nc`, where the first date is the bulletin date and the second the validity date; `FREQ` ∈ {`dm`, `hi`, `qh`}. Bucket path: `nws-ocean/{YYYY}/{MM}/{DD}/T0000Z/`. The **AMM15-WW3 wave files** (`level1_wave_amm15_NWS_WAV_b..._hi....nc`) reside in the same folder.
- **File size:** ~12 MB per hourly 2D surface file (e.g. SSH hourly, 24×1240×958); larger for 3D and quarter-hourly (96 timesteps)
- **Official access:**
  - AWS registry: https://registry.opendata.aws/met-office-nws-ocean/
  - Bucket: `s3://met-office-nws-ocean-model-data/` (region `eu-west-2`)
  - Browse: https://met-office-nws-ocean-model-data.s3.eu-west-2.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://met-office-nws-ocean-model-data/`
  - AMM15 product sheet: https://www.metoffice.gov.uk/binaries/content/assets/metofficegovuk/pdf/data/amm15-data-product-sheet.pdf
- **DOI:** none on the registry
- **Delivery mechanism:** AWS Open Data (S3, anonymous); also SFTP/FTP pull to UKMCAS users. New-object notifications via SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-nws-ocean-model-data-object_created`.

### Channel 2 — Copernicus Marine
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence
- **Product identifier:** `NWSHELF_ANALYSISFORECAST_PHY_004_013`. *The PUM consistently uses the legacy form `NORTHWESTSHELF_ANALYSIS_FORECAST_PHY_004_013` in its own tables — the same legacy form the BAL MFC PUM uses when naming this product as its boundary source.*
- **DOI:** https://doi.org/10.48670/moi-00054
- **Data formats:** NetCDF-4, CF-1.7; ARCO Zarr under `https://s3.waw3-1.cloudferro.com/mdl-arco-time-041/arco/NWSHELF_ANALYSISFORECAST_PHY_004_013/…`
- **Delivery mechanism:** Copernicus Marine Toolbox
- **Dissemination schedule (all from the 00 UTC run, delivered daily until 12 UTC):**

| Type | Temporal coverage |
|---|---|
| Forecast | 0 to +168 h |
| NRT analysis | −24 to 0 h |
| Best estimate analysis | −48 to −24 h |

  Each day the previous day's NRT analysis and 7-day forecast are deleted at the start of the process. Catalogue data from two years before present to T−24 h is taken from the T−48 h to T−24 h block of each run (best estimate).

- **Datasets (16 time-varying + 3 static variants), all tagged `_202511`:**

| Dataset | Variables | File size |
|---|---|---|
| `cmems_mod_nws_phy-cur_anfc_1.5km-3D_PT1H-i` | `uo`, `vo`, `wo` (3D); `ubar`, `vbar` (2D) | 1331 MB |
| `cmems_mod_nws_phy-cur_anfc_1.5km-3D_P1D-m` | same | 50 MB |
| `cmems_mod_nws_phy-cur_anfc_1.5km-2D_PT1H-i` | `uo`, `vo` (surface) | 37 MB |
| `cmems_mod_nws_phy-cur_anfc_1.5km-2D_PT15M-i` | `uo`, `vo` (surface) | 147 MB |
| `cmems_mod_nws_phy-tem_anfc_1.5km-3D_PT1H-i` / `_P1D-m` | `thetao` | 376 / 16 MB |
| `cmems_mod_nws_phy-sal_anfc_1.5km-3D_PT1H-i` / `_P1D-m` | `so` | 305 / 13 MB |
| `cmems_mod_nws_phy-ssh_anfc_1.5km-2D_PT1H-i` / `_PT15M-i` | `zos` | 13 / 50 MB |
| `cmems_mod_nws_phy-bottomt_anfc_1.5km-2D_PT1H-i` / `_P1D-m` | `bottomT` | 21 / 0.9 MB |
| `cmems_mod_nws_phy-mld_anfc_1.5km-2D_PT1H-i` / `_P1D-m` | `mlotst` | 19 / 0.8 MB |
| `cmems_mod_nws_phy-sst_anfc_1.5km-2D_PT1H-i` | `thetao` (surface) | 20 MB |
| `cmems_mod_nws_phy-sss_anfc_1.5km-2D_PT1H-i` | `so` (surface) | 18 MB |
| `cmems_mod_nws_phy_anfc_1.5km_static` | `mask`, `deptho`, `mdt`, `deptho_lev_interp`, `depth` (`--ext--coords`, `--ext--bathy`, `--ext--mdt`) | 161 MB |

- **File naming:** identical to the AWS convention — `metoffice_foam1_amm15_NWS_{VAR}_b{yyyymmdd}_{dm|hi|qh}{yyyymmdd}.nc`
- **Official access:** https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_PHY_004_013/description

> **The PUM's dataset table is incomplete on one point.** It lists `ubar`/`vbar` only under the hourly 3D current dataset. Live Zarr metadata shows both barotropic components are present in the **daily-mean** 3D current dataset as well — verified shapes `[1067, 1240, 958]` alongside the 3D `[1067, 33, 1240, 958]` fields.

> **Verified 2026-08-25** (Copernicus channel): grid dimensions and spacing, the 33-level list, per-dataset variable inventory and dimensionality, dataset version tags, and archive extents confirmed from Marine Data Store STAC records and live ARCO Zarr metadata.

---

## Relationship to other entries

- **Coupled counterpart:** **[AMM15-WW3 (NWS wave)](../../../wave_models/regional/uk/amm15-ww3-uk.md)** — the wave half of the same coupled executable, on the same domain and delivery grid, distributed in the same AWS bucket and sharing the Copernicus static file set. The strongest companion link in the repository. Both products share the same operator history (see *Version history*), and the wave model version discrepancy between the two PUMs is resolved from AWS file metadata (see *Coupling*).

- **Reciprocal boundary exchange with the Baltic.** This product takes its **Baltic** boundary (T, S, barotropic u and v) from **[BAL MFC-NEMO](../../regional/sweden/bal-mfc-nemo.md)** (`BALTICSEA_ANALYSISFORECAST_PHY_003_006`); that product in turn takes its **western** boundary from this one, at Orkney–Norway and the English Channel. **The two feed each other at their shared boundary.** Neither PUM mentions the other direction — it only surfaces from reading both. This is a mutual dependency, not a nesting hierarchy, so neither product is the other's parent.

- **Parent global model — not FOAM-GC.** The Atlantic boundary comes from the Met Office **Operational ORCA12** forecast implementation (1/12°). [FOAM-GC](../../global/uk/foam-gc.md) is **ORCA025** (1/4°). These are different configurations, so FOAM-GC is not this model's parent despite being the same operator's global system. **The actual ORCA12 parent is not currently in this repository** — a gap worth recording.

- **One-way downstream consumer:** **[Met Office UK Wave (`uk_wav_det`)](../../../wave_models/regional/uk/uk-wave-metoffice.md)** — a second wave system on the same domain, which takes this model's surface currents as one-way forcing rather than being coupled to it. Published in its own AWS bucket.

- **Regional peers / overlapping coverage:** overlaps the Copernicus **IBI** domain (Bay of Biscay, Celtic Sea, western Channel) — a relationship complicated by the fact that between 2023 and 2025 the Copernicus NWS product *was* an IBI extraction. See the overlap discussion in the [AMM15-WW3 entry](../../../wave_models/regional/uk/amm15-ww3-uk.md).

- **AI-based counterparts:** TBD.

- **Programme context:** see [`COPERNICUS.md`](../../../../COPERNICUS.md) for the full Copernicus Marine portfolio.

---

## Version history

This product has an unusual lineage on the Copernicus side: the identifier persisted across a change of operator **and of model**, in both directions.

### November 2025 — PUM Issue 3.0
**Re-introduction of the NWS physical analysis and forecasting product by the UK Met Office**, with a 7-day forecast. Authored by J. Tinker and T. Brüning (Met Office), replacing the IBI-team authorship of Issue 2.x. The Copernicus product returns to AMM15.

### November 2024 — PUM Issue 2.1
New variables distributed: de-tided surface currents and mean sea level, and vertical velocity. Forecast horizon increased from 5 to 10 days. *(Authored by the IBI team — this is the IBI-extraction system, not AMM15.)*

### September / November 2023 — PUM Issue 2.0
**First release of the NWS ocean circulation analysis and forecast product as an extraction of the IBI operational system.** Authored by R. Aznar, A. Amo, L. Castrillo, G. Reffray, and R. Escudier.

### 2025 — AWS Open Data distribution (CC BY-SA 4.0)
NWS Ocean physics published on AWS as a 2-year rolling archive; carries additional diagnostics beyond the earlier UKMCAS/FTP product sheet (in-situ temperature `TEMPIS`; water-column max-current `MAXCURU`/`MAXCURV`/`ZMAXCUR`).

### 2023 — AMM15 product sheet (UKMCAS/FTP distribution)
Documented AMM15 NWS physics delivered via FTP to UKMCAS by the MaPP-SS suite (Crown Copyright 2023). Variable set: TEM, SAL, CUR, BED, MLD, SST, SSS, SSC, SSH.

### February 2022 — PUM Issue 1.3
Upgrade to GLO-HR boundary conditions and new MDT.

### December 2020 — PUM Issue 1.1
Addition of SSC and SSH 15-minute datasets; time units changed to seconds since 1970-01-01T00:00:00Z.

### August 2020 — PUM Issue 1.0
Merged the separate 004_013 and 004_014 PUMs. Earlier in 2020: correction of a 30-minute shift in the hourly dataset time record; TMB dataset removed; SST, SSS, SSC, MLD, BED hourly datasets added.

> **The Copernicus rolling archive spans two different models.** Live Copernicus datasets begin **2023-09-29** — essentially the IBI-extraction changeover — and carry the `_202511` version tag throughout. A user assembling a multi-year series from this product crosses a **complete system replacement in November 2025**, with no discontinuity flag in the data itself. The AWS distribution is not affected in the same way, since it has always carried the Met Office AMM15 output. This is the same trap documented on the wave side in the [AMM15-WW3 entry](../../../wave_models/regional/uk/amm15-ww3-uk.md).

*(AMM15 replaced the earlier 7 km AMM7 shelf configuration; document AMM7 lineage separately if needed. Figure lineage: Tonani et al., 2019.)*

---

## Notes
- **Undocumented-on-sheet variables, and AWS-only.** The AWS distribution adds in-situ temperature (`TEMPIS`/`t0`, distinct from potential temperature `thetao`) and water-column maximum-current diagnostics (`MAXCURU`/`uomax`, `MAXCURV`/`vomax`, `ZMAXCUR`/`zomax` — the depth at which the max current occurs). These are absent from the 2023 product sheet, from the Copernicus PUM, and from live Copernicus datasets. Confirmed only by inspecting live files.

- **Daily means are already quasi-de-tided.** Daily means are the mean of **25 hourly instantaneous values**, starting at midnight UTC and finishing at the following midnight — the PUM states this removes both diurnal and tidal cycles. This is why the physics product ships no separate de-tided daily dataset, unlike the [Baltic product](../../regional/sweden/bal-mfc-nemo.md), which does. Note the November 2024 IBI-era upgrade *did* add de-tided currents and mean sea level; those datasets are not in the current Met Office configuration.

- **`mlotst` has two conflicting definitions in the same PUM.** The dataset table gives *"reference depth of 3 m, dt_crit of −0.2"*; the parameter-notes section says the density increase is referenced to **10 m** depth with a 0.2 °C equivalent criterion. **Flagged, not resolved** — check the file attribute directly before relying on the definition.

- **Minimum-depth floor of 10 m, and what it does.** NEMO v3.6 has no wetting-and-drying capability, so every bathymetric grid point shallower than 10 m is reassigned a depth of 10 m to keep the model stable under this domain's large tides. Points are interpolated back to their original depth before delivery, so the floor is invisible in the bathymetry field but present in the dynamics. Tide and surge propagation speed goes as √(gH), so where model depth exceeds reality the propagation is wrong; where the seabed is genuinely exposed the drying process is **absent by design** — there is water in the model where there is none in reality. The PUM explicitly advises against using model fields directly in regions with extensive sub-10 m bathymetry (the **Wadden Sea** is named), and recommends that any nested domain place its boundaries well away from such areas. Wetting and drying is implemented in NEMO 4 and is flagged as future development (O'Dea et al. 2020).

- **Shared bucket with waves:** physics and AMM15-WW3 wave files sit in the same `nws-ocean/.../T0000Z/` folder; filter on the `metoffice_foam1_` vs `level1_wave_` prefix. The wave files carry 9 validity days per cycle (T−48 h to T+167 h, 24 hourly steps each, ~230 MB apiece), against 18 files for the standard physics variables. Note that a **second Met Office wave bucket exists** (`met-office-nws-wave-model-data`) holding an unrelated configuration — see the [UK Wave entry](../../../wave_models/regional/uk/uk-wave-metoffice.md).

- **Time coordinate:** `seconds since 1970-01-01T00:00:00Z` (note: differs from the Global Ocean product's 1900 epoch). Hourly files carry 24 steps/day; quarter-hourly carry 96.

- **River forcing is climatological, not real-time.** A 1980–2014 gauge climatology, with French and Norwegian rivers falling back to the older AMM7 climatology. Anything sensitive to individual flood events or to recent trends in river discharge should not use this product's freshwater fields uncritically.

---

## Official documentation
- AMM15 data product sheet (NWS-Ocean / FOAM-NWSO / AMM15), Met Office, Crown Copyright 2023: https://www.metoffice.gov.uk/binaries/content/assets/metofficegovuk/pdf/data/amm15-data-product-sheet.pdf
- AWS registry: https://registry.opendata.aws/met-office-nws-ocean/
- **Copernicus product page:** https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_PHY_004_013/description
- **Product User Manual (Issue 3.0, November 2025):** https://documentation.marine.copernicus.eu/PUM/CMEMS-NWS-PUM-004-013.pdf
- **Quality Information Document (QUID):** https://documentation.marine.copernicus.eu/QUID/CMEMS-NWS-QUID-004-013.pdf
- **DOI (Copernicus):** https://doi.org/10.48670/moi-00054
- UK Met Office: https://www.metoffice.gov.uk/

### Key references
- Tonani, M., Sykes, P., King, R.R., McConnell, N., Pequignet, A.C., O'Dea, E., Graham, J.A., Polton, J. and Siddorn, J. (2019). The impact of a new high-resolution ocean model on the Met Office North-West European Shelf forecasting system. *Ocean Science*, 15, 1133–1158. https://doi.org/10.5194/os-15-1133-2019
- Guihou, K., Polton, J., Harle, J., Wakelin, S., O'Dea, E. and Holt, J. (2017). Kilometric scale modelling of the North West European shelf seas. *J. Geophys. Res.-Oceans*, 122. https://doi.org/10.1002/2017JC012960
- O'Dea, E., Bell, M.J., Coward, A. and Holt, J. (2020). Implementation and assessment of a flux limiter based wetting and drying scheme in NEMO. *Ocean Modelling*, 155. https://doi.org/10.1016/j.ocemod.2020.101708
- Egbert, G.D. and Erofeeva, A. (2002). Efficient inverse modeling of barotropic ocean tides. *J. Atmos. Oceanic Tech.*, 19, 183–204.
- Flather, R.A. (1976). A tidal model of the north west European continental shelf. *Mémoires de la Société Royale des Sciences de Liège*, 6, 141–164.
- Breivik, Ø., Bidlot, J.R. and Janssen, P.A. (2016). A Stokes drift approximation based on the Phillips spectrum. *Ocean Modelling*, 100, 49–56.
- Chawla, A. and Tolman, H.L. (2008). Obstruction grids for spectral wave models. *Ocean Modelling*, 22, 12–25.
- Vörösmarty, J., Green, P., Salisbury, J. and Lammers, R.B. (2000). Global water resources: vulnerability from climate change and population growth. *Science*, 289, 284–288. https://doi.org/10.1126/science.289.5477.284
- Young, E.F. and Holt, J.T. (2007). Prediction and analysis of long-term variability of temperature and salinity in the Irish Sea. *J. Geophys. Res.*, 112, C01008. https://doi.org/10.1029/2005JC003386
