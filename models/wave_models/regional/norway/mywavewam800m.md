# MyWaveWAM800m (Norwegian Coastal Wave Forecasting System)

## What this model is
MyWaveWAM800m (`wam800m_curr`) is MET Norway's **operational high-resolution coastal wave forecast system** for the Norwegian coast, run as **five separate nested domains** at ~800 m nominal resolution and distributed as NetCDF through MET Norway's THREDDS server.

It is a WAM Cycle 4.7.0 spectral wave model forced by MEPS winds and NorKyst v3 surface currents, taking open-boundary wave spectra from MET Norway's [WAVEWATCH III 4 km](./met-norway-ww3.md) system. Wave–current interaction is a defining feature — the `_curr` in the product name distinguishes this current-forced configuration.

The five domains are treated by MET Norway as a single product (one catalog page, one model version, one configuration, shared production schedule), so they are documented here as one entry with per-domain specifications rather than as five entries.

*Live-verified against the operational THREDDS distribution on 2026-07-27, across all five domains and all three access channels.*

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (MET Norway)
- **Country / region:** Norway
- **Producing unit:** Division for Ocean and Ice (`creator_email = fou-hi@met.no`)
- **Project attribution:** `project = "Ocean and Ice - Research to Operation (HI-R2O)"`
- **Named contributors (file metadata):** Ana Carrasco (technical contact), Harald Engedahl, Mateusz Matuszak
- **Operational status:** `operational_status = "Operational"`, but `dataset_production_status = "In Work"` — see *Notes*

---

## What area it covers
The Norwegian coast, divided into five domains. **Each domain has its own rotated pole** — they are not slices of a common grid.

| ID | Name | Catalog key | Grid (rlon × rlat) | Rotated pole (lon / lat) | Longitude | Latitude |
|---|---|---|---|---|---|---|
| c0 | Finnmark | `f` | 381 × 199 | 170.0 / **60.0** | 25.62° – 33.66°E | 69.35° – 71.74°N |
| c1 | NordNorge | `n` | 228 × 952 | 140.0 / 22.0 | 9.49° – 29.70°E | 66.12° – 72.01°N |
| c2 | MidtNorge | `m` | 178 × 688 | 160.0 / 22.0 | 7.02° – 16.29°E | 62.33° – 67.83°N |
| c3 | Vestlandet | `v` | 231 × 677 | 172.0 / 22.0 | 2.78° – 8.90°E | 57.93° – 63.61°N |
| c4 | Skagerrak | `s` | 184 × 397 | 140.0 / 22.0 | 5.55° – 11.82°E | 57.10° – 60.39°N |

- **Grid type:** rotated latitude–longitude, `earth_radius = 6371000.0`, grid-mapping variable `projection_3`
- **proj4 pattern:** `+proj=ob_tran +o_proj=longlat +lon_0=<-(180-pole_lon)> +o_lat_p=<pole_lat> +R=6.371e+06 +no_defs` (e.g. Finnmark: `lon_0=-10 +o_lat_p=60`)
- **Spacing:** uniform **0.008°** in rotated coordinates for all five domains
- **True coordinates:** 2-D `latitude` / `longitude` auxiliary variables in every file
- **Bathymetry:** `depth` field included per domain

**Note on the "800 m" label:** 0.008° on the stated earth radius is **≈890 m**, not 800 m. Treat "800m" as the product name.

**Note on the per-domain rotation:** c1 and c4 share the 140/22 pole (also used by [WW3 4 km](./met-norway-ww3.md)), c2 and c3 use different longitudes, and **c0 uses an entirely different pole latitude (60.0)**. Any reprojection or regridding code must handle each domain separately; a shared transform will silently misplace Finnmark.

---

## Basic details
- **Model type:** Regional deterministic spectral wave model, forced by surface currents
- **Core wave model:** **WAM Cycle 4.7.0** (confirmed in aggregation titles: `"Wave model - WAM 4.7.0, wam800m_curr ..."`)
- **Horizontal resolution:** 0.008° rotated (≈890 m)
- **Spectral discretization:** **36 frequencies × 36 directions** (all five domains) — matching the WW3 4 km spectra that supply the boundaries
- **Forecast length:** **66 hours**
- **File time span:** 73 hourly steps, **T−6 h to T+66 h** relative to the cycle
- **Update frequency / cycles:** **4× daily — 00, 06, 12, 18 UTC.** See the correction below.
- **Temporal output resolution:** 1 hour
- **Conventions:** CF-1.8, ACDD-1.3
- **Time encoding:** `seconds since 1970-01-01 00:00:00`; `forecast_reference_time` in **days** since epoch (same mixed-unit quirk as [MyWaveWAM3km](./mywavewam3km.md))

### Cycle count — documentation is wrong

The catalog page states the model *"is running twice a day, at 00 utc and 12 utc, and produces a +66 hrs forecast."* **Four distinct cycles are published daily.** Verified on the Finnmark domain, 2026-07-27:

| File | `time_coverage_start` → `end` | `date_created` |
|---|---|---|
| `c0WAVE00.nc` | 2026-07-26T18:00 → 2026-07-29T18:00 | 04:07:43Z |
| `c0WAVE06.nc` | 2026-07-27T00:00 → 2026-07-30T00:00 | 09:42:00Z |
| `c0WAVE12.nc` | 2026-07-27T06:00 → 2026-07-30T06:00 | 16:12:02Z |
| `c0WAVE18.nc` | 2026-07-26T12:00 → 2026-07-29T12:00 | 21:47:38Z (prev. day) |

Four independent runs, six hours apart, each spanning T−6 h to T+66 h, each with its own creation timestamp. The 06 and 18 UTC cycles are genuine model runs, not copies. The "+66 hrs" and "00/12 utc" halves of the sentence are not equally reliable — the forecast length checks out, the cycle count does not.

**However, only the 00 UTC cycle is archived** (see *Data availability*), which is consistent with the catalog's separate note that *"aggregations are only updated at 00 utc."* The documentation appears to describe the archived subset rather than the published output.

---

## Forcing and nesting
- **Wind forcing:** [MEPS](../../../nwp_models/regional/norway/meps.md) (HARMONIE-AROME, 2.5 km), per the catalog description. Forcing winds published as `ff` / `dd`.
- **Current forcing:** surface currents from [NorKyst v3](../../../ocean_models/regional/norway/norkyst-v3.md), MET Norway's 800 m ROMS coastal ocean system, per the catalog description. Published here as `Current` / `Currentdir` (`"surface current speed from ocean model"`). Currents act as a forcing field alongside the winds — this is the product's distinguishing feature. Note that NorKyst v3 runs a 00 UTC cycle only, so the 06/12/18 UTC wave cycles are re-using current fields from the same daily ocean run.
- **Open boundary spectra:** from **[WAVEWATCH III 4 km](./met-norway-ww3.md)**, per the catalog description.
- **Ice forcing:** none. Unlike [MyWaveWAM3km](./mywavewam3km.md), no `SIC`/`SIT` fields — these domains are ice-free in operational practice.

### Boundary nesting — cross-verified
The WW3 4 km system publishes boundary spectra files `C0_SPC` … `C4_SPC` labelled Finnmark, NordNorge, MidtNorge, Vestlandet, Skagerak. Their geographic extents match these domains closely enough to confirm the nesting independently of the prose documentation:

| Domain | WAM800m bounds (this entry) | WW3 4 km `C*_SPC` extent |
|---|---|---|
| c0 Finnmark | 25.62–33.66°E, 69.35–71.74°N | 26.48–33.66°E, 69.90–71.74°N |
| c1 NordNorge | 9.49–29.70°E, 66.12–72.01°N | 9.49–29.02°E, 66.20–72.01°N |
| c2 MidtNorge | 7.02–16.29°E, 62.33–67.83°N | 7.07–14.64°E, 63.01–67.83°N |
| c3 Vestlandet | 2.78–8.90°E, 57.93–63.61°N | 2.78–8.07°E, 57.93–63.61°N |
| c4 Skagerrak | 5.55–11.82°E, 57.10–60.39°N | 5.55–11.12°E, 57.10–59.00°N |

Western/southern boundary coordinates agree to two decimal places in every case. **This also confirms that WW3 4 km's undocumented sixth spectra file `C5_SPC` is surplus to the WAM800m chain** — there are five coastal domains and five boundary sets are needed. See the [WW3 4 km entry](./met-norway-ww3.md) for that open question.

---

## Data assimilation
- **Assimilates wave observations:** No documented assimilation. Nothing in the file metadata or catalog description indicates it. *(Absence of documentation, not positive confirmation.)*

---

## What it provides

### Gridded fields (`WAVE` files) — 48 populated variables

**Total sea state** — `hs` · `tp` · `tmp` · `tm1` · `tm2` · `thq` · `fpI` · `Pdir`

**Wind sea partition** — `hs_sea` · `tp_sea` · `tmp_sea` · `tm1_sea` · `thq_sea`

**Swell partition (aggregate)** — `hs_swell` · `tp_swell` · `tmp_swell` · `tm1_swell` · `thq_swell`

**Three individual swell systems** — `fshs`/`fstm1`/`fsdir` (first) · `sshs`/`sstm1`/`ssdir` (second) · `tshs`/`tstm1`/`tsdir` (**third**)

**Extreme-wave diagnostics** — `mHs` (expected maximum wave height) · `mwp` (expected wave period) · `Hmax_N` (maximum crest-to-trough height) · `hmax_st` (maximum wave height, space-time / STQD)

**Stokes drift and transport** — `sdx`/`sdy` · `utrs`/`vtrs`

**Air–sea fluxes** — `FV` (friction velocity) · `DC` (drag coefficient) · `phiaw` · `phioc` · `tauocx`/`tauocy`

**Bottom fluxes** — `phibot` (energy flux from waves to bottom) · `taubot_x`/`taubot_y` (momentum flux into bottom)

**Forcing and static** — `ff`/`dd` · `Current`/`Currentdir` · `depth` · `latitude`/`longitude`

**Direction convention:** CF standard names are `..._wave_to_direction` (e.g. `sea_surface_primary_swell_wave_to_direction`) — the **"to" (oceanographic) convention**, consistent with the rest of MET Norway's wave portfolio and opposite to the "from" convention used by many other operational wave products.

**Richer than the offshore sibling.** The third swell system (`tshs`/`tstm1`/`tsdir`) and the bottom-flux group (`phibot`/`taubot_x`/`taubot_y`) are **populated here but present as empty scalar placeholders in [MyWaveWAM3km](./mywavewam3km.md)**. Both products use the same WAM output template; the coastal configuration activates the shallow-water and multi-swell diagnostics that the offshore one leaves off. If you are building a shared reader for both, the variable list is the same but the populated subset is not.

### Point directional spectra (`SPC` files)
`SPEC[time=73][y=1][x=N][freq=36][direction=36]`, with per-point `Pdir`, `dd`, `ff`, `hs`, `tp`, `thq_sea`, `thq_swell`, `depth`.

| Domain | Spectral points | `SPC` file size |
|---|---|---|
| c0 Finnmark | **9** | 3.4 MB |
| c1 NordNorge | **1171** | 445.5 MB |
| c2 MidtNorge | **32** | 12.2 MB |
| c3 Vestlandet | **63** | 24.0 MB |
| c4 Skagerrak | **586** | 222.9 MB |

The point counts differ by more than two orders of magnitude. c0's nine points sit at round coordinates (30.5°E, 32.0°E, 31.0°E, …), suggesting a small hand-picked station list, while c1 and c4 carry dense sets. Point locations come from 1-D `latitude`/`longitude` arrays; there is no station-name variable, so **what these point sets represent is TBD** and differs by domain.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **CC BY 4.0** — `license = "https://spdx.org/licenses/CC-BY-4.0 (CC-BY-4.0)"`, declared consistently across `WAVE` files, `SPC` files, archive files, and aggregations. Attribution to MET Norway required; no share-alike obligation.
- **Is the data downloadable?** Yes — direct HTTP and OPeNDAP
- **Data formats:** NetCDF-4 (CF-1.8 / ACDD-1.3)
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Top-level catalog:** https://thredds.met.no/thredds/fou-hi/mywavewam800current.html

The product is distributed through **three parallel channels per domain**, with different retention and different content. Substituting one for another is not safe.

### 1. Latest files (rotating, all four cycles)
- **Catalogs:** `https://thredds.met.no/thredds/catalog/fou-hi/mywavewam800{f,n,m,v,s}_curr/catalog.html`
- **Naming:** `MyWave_wam800_curr_<cID>WAVE<HH>.nc` and `MyWave_wam800_curr_<cID>SPC<HH>.nc`, `HH` ∈ {00, 06, 12, 18}
- **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/fou-hi/mywavewam800<key>_curr/MyWave_wam800_curr_<cID>WAVE<HH>.nc`
- **Retention: four slots only.** Filenames contain **no date** — each slot is overwritten daily by the same cycle hour. To retain output you must harvest within 24 hours; there is no way to recover a missed cycle from this channel.

| Domain | `WAVE` file size |
|---|---|
| c0 Finnmark | ~425 MB |
| c1 NordNorge | ~1.20 GB |
| c2 MidtNorge | ~677 MB |
| c3 Vestlandet | ~915 MB |
| c4 Skagerrak | ~294 MB |

### 2. Hourly Files archive (00 UTC cycle only, ~3.3 years)
- **Catalogs:** `https://thredds.met.no/thredds/catalog/fou-hi/mywavewam800{f,n,m,v,s}_currhf/catalog.html`
- **Naming:** `mywavewam800_<domain>.an.<YYYYMMDD>18.nc` and `mywavewam800_<domain>.fc.<YYYYMMDD>18.nc`
- **Live-measured (Finnmark, 2026-07-27): 1201 files — 1200 `an` files plus a single `fc` file**, spanning **2023-03-30** to 2026-07-26.
- **Archive start coincides with the v4.7 upgrade** (28 March 2023), giving a clean model-version boundary — the whole archive is one configuration.

**The `an` / `fc` split, decoded.** Every file is labelled with hour `18`, which is *not* a cycle time — it is the start of the 00 UTC run's window (00Z minus 6 h, on the previous day):
- `an.<D>18.nc` — **24 hourly steps**, `<D>`T18:00Z to `<D+1>`T17:00Z. The first 24 hours of the following day's 00 UTC run.
- `fc.<D>18.nc` — **49 hourly steps**, `<D+1>`T18:00Z to `<D+3>`T18:00Z. Hours 24–72 of that same run.

Together they reconstruct the full 73-step 00 UTC run. Only one `fc` file is retained (the most recent); the historical archive is analysis segments only. **Verified example:** `an.2026072518.nc` spans 2026-07-25T18:00 → 2026-07-26T17:00; `fc.2026072618.nc` spans 2026-07-27T18:00 → 2026-07-29T18:00, which is exactly the tail of the 00 UTC run of 2026-07-27.

*Catalog fetch time for one domain's Hourly Files XML was ~10 s (416 KB) from a server-side client. The HTML rendering of the same listing is considerably slower in a browser — use the `.xml` endpoint for programmatic traversal.*

### 3. Best-estimate aggregation (continuous time series)
- **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/fou-hi/mywavewam800{f,n,m,v,s}_curr_be`
- **Live-measured (Finnmark): 28,849 hourly steps, 2023-03-30T18:00Z → 2026-07-29T18:00Z** (through the end of the current forecast)
- **Not fully contiguous.** The span covers 29,208 hours but only 28,849 steps are present — **roughly 359 missing hours (~1.2%)**. Unlike the [WW3 4 km aggregation](./met-norway-ww3.md), which is gap-free, this one has holes. Do not assume a regular hourly index; read the time axis.
- Updated only from the 00 UTC cycle, per the catalog note.

**This is the practical route for coastal wave climatology and retrospective work** — a single endpoint per domain gives 3+ years of hourly current-coupled coastal wave fields.

### Publication latency (live-measured, all domains)
| Cycle | Published at | Latency |
|---|---|---|
| 00 UTC | ~04:21 UTC | +4 h 21 m |
| 06 UTC | ~09:57 UTC | +3 h 57 m |
| 12 UTC | ~16:33 UTC | +4 h 33 m |
| 18 UTC | ~21:57 UTC | +3 h 57 m |

Consistent across all five domains to within seconds; `SPC` files land a few seconds after their `WAVE` siblings.

### Terms of service
MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. WMS is not recommended beyond simple demonstration. Status: https://status.met.no

---

## Notes

- **Documentation understates the cycle count.** The catalog says 2× daily; four cycles are published. Documented forecast length (+66 h) and domain list are accurate. The likely explanation is that the description tracks the archived/aggregated subset rather than the full published output.

- **Latest files carry no date in the filename.** `MyWave_wam800_curr_c0WAVE06.nc` is a slot, not a snapshot. Cache-aware clients that key on URL will silently serve stale data; check `date_created` or `time_coverage_start`, not the path.

- **Only the 00 UTC cycle survives past 24 hours.** The 06, 12, and 18 UTC runs exist only in the rotating slots. For a complete four-cycle record you must harvest continuously.

- **Stale `time_coverage` attributes in archive files.** The `an` files declare a 72-hour `time_coverage_start`/`end` inherited from the parent run, but contain only 24 steps. Verified: `an.2026072518.nc` advertises 2026-07-25T18:00 → 2026-07-28T18:00 while its actual time axis ends 2026-07-26T17:00. **Trust the time axis, not the global attributes.**

- **Unit error on `tstm1`.** Third swell mean period is declared with `units = "m/s"`; it should be `s` (its first- and second-swell counterparts `fstm1` and `sstm1` are both correctly `s`). Harmless if you ignore units, breaking if you use a unit-aware library like `cf-units` or Iris.

- **`dataset_production_status = "In Work"`** despite `operational_status = "Operational"`. The two attributes disagree. Given ~3.3 years of stable archive and consistent sub-5-hour latency, the operational designation is the credible one, but the metadata is worth flagging.

- **Domain-specific rotated poles** are the single most likely source of silent error when working across all five areas. See *What area it covers*.

- **Relationship to MET Norway's other wave systems:**
  - **This entry (MyWaveWAM800m)** — WAM 4.7.0 at ~890 m, five Norwegian coastal domains, 66 h × 4 daily, MEPS winds + NorKyst v3 currents, WW3-4km boundaries, archive to 2023-03-30.
  - **[WW3 4 km](./met-norway-ww3.md)** — WAVEWATCH III v6.07 at ~4.45 km, Nordic/North/Baltic Seas, 66 h × 4 daily. **Supplies this system's open boundaries.**
  - **[MyWaveWAM3km](./mywavewam3km.md)** — WAM 4.7.0 at ~3.34 km, pan-Arctic, 120/240 h × 2 daily, ice-coupled, no archive. Same core and output template as this entry, different activated field set.
  - **[ARCWAM](./arcwam.md)** — Copernicus Marine Arctic distribution.
  - **NORA3 / NORA10** — long-term hindcasts, distinct systems.

- **Choosing among them:** MyWaveWAM800m for anything nearshore, harbour-scale, or where tidal/coastal currents matter. WW3 4 km for the shelf seas including the Baltic. MyWaveWAM3km or ARCWAM for the open Arctic.

- **Naming.** MET Norway uses `wam800m_curr` internally, `MyWaveWam800m` in catalog titles, and `mywavewam800` in filenames. The `_curr` suffix marks the current-forced configuration; a non-current variant existed historically. This entry uses **MyWaveWAM800m**.

---

## Recent version history

### 2023-03-28 — Upgrade from WAM Cycle 4.5 to Cycle 4.7
Documented on the catalog page. Brought an updated physics package and new output variables, and **extended the c1 (NordNorge) domain to cover the Mosken area at the southern tip of Lofoten**. All currently distributed data is v4.7.

### 2023-03-30 — Archive and aggregation start
First `an` file (`2023033018`) and first aggregation timestamp (2023-03-30T18:00Z), two days after the v4.7 cutover. The archive therefore contains no v4.5 data, which is convenient: the entire retrievable record is a single model configuration.

---

## Official documentation
- MyWaveWAM800m catalog and model description: https://thredds.met.no/thredds/fou-hi/mywavewam800current.html
- Per-domain latest catalogs: `https://thredds.met.no/thredds/catalog/fou-hi/mywavewam800{f,n,m,v,s}_curr/catalog.html`
- Per-domain hourly-file archives: `https://thredds.met.no/thredds/catalog/fou-hi/mywavewam800{f,n,m,v,s}_currhf/catalog.html`
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/fou-hi/fou-hi.html
- MET Norway ocean and wave model overview: https://ocean.met.no/models
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/

> **Documentation gap:** the catalog page carries a short model description (five paragraphs) but there is no PUM, validation report, or grid specification document. Everything above the *Official documentation* section beyond that description was derived from file metadata and live catalog inspection — including the cycle count, which contradicts the description.
