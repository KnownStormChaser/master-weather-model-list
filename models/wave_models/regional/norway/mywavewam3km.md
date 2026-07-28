# MyWaveWAM3km (Nordic Seas / Arctic Wave Forecasting System)

## What this model is
MyWaveWAM3km is MET Norway's **operational WAM-based regional wave forecast system** covering the Nordic Seas, the Arctic Ocean, and the northern North Atlantic at ~3 km resolution, distributed as NetCDF through MET Norway's own THREDDS server.

It is a third-generation spectral wave model coupled to ocean surface currents and sea ice, running twice daily with alternating 5-day and 10-day forecast lengths. Alongside the gridded fields it publishes **2-D directional wave spectra at 711 fixed points** spanning the North Atlantic and Arctic.

The system is distinct from MET Norway's other operational wave model, [WAVEWATCH III 4 km](./met-norway-ww3.md), in model core, domain, resolution, cycling, and forecast length. It appears to be closely related to — possibly the same production as — the Copernicus Marine [ARCWAM](./arcwam.md) product; see *Relationship to other wave products* for the evidence and the open question.

*Live-verified against the operational THREDDS distribution on 2026-07-27 (cycles 2026-07-24T18Z through 2026-07-27T06Z).*

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (MET Norway)
- **Country / region:** Norway
- **Producing unit:** `creator_email = fou-hi@met.no` (Ocean and Ice / FoU-HI)
- **Publisher:** MET Norway / Arctic Data Centre (`adc-support@met.no`)
- **Project attribution:** `project = "Public service / Wave forecast"`
- **Operational status:** `operational_status = "Operational"` (file global attribute)

---

## What area it covers
- **Coverage:** Nordic Seas, Arctic Ocean, northern North Atlantic — pan-Arctic in extent despite the "Nordic Seas" label in the catalog title
- **Geospatial bounds (file attributes):** 36.167°N – 89.987°N; 180°W – 180°E (**global in longitude**)
- **Grid dimensions:** 2379 (`rlon`) × 1995 (`rlat`)
- **Native grid:** Rotated latitude–longitude (`comment = "Original grid rotated"`)
  - `grid_mapping_name = rotated_latitude_longitude`
  - `grid_north_pole_longitude = 140.0`, `grid_north_pole_latitude = 25.0`
  - `earth_radius = 6371000.0`
  - proj4: `+proj=ob_tran +o_proj=longlat +lon_0=-40 +o_lat_p=25 +R=6.371e+06 +no_defs`
  - Grid-mapping variable name: `projection_ob_tran`
- **Rotated-coordinate extent:** `rlon` −40.40° → 30.94°, `rlat` −11.70° → 48.12°, uniform 0.03° spacing
- **True latitude/longitude:** supplied as 2-D auxiliary variables `latitude[rlat][rlon]` / `longitude[rlat][rlon]`
- **Bathymetry field:** `model_depth` (sea floor depth below sea level, m) included in every file

**Note on the "3 km" label:** the grid is uniform 0.03° in rotated coordinates, which is **≈3.34 km** on the stated earth radius. "3 km" is the product name, not the exact cell size.

**Note on the rotated pole:** this is *not* the same rotation as the [WW3 4 km](./met-norway-ww3.md) product. Both use `grid_north_pole_longitude = 140.0`, but the pole latitude differs (25.0 here vs 22.0 for WW3 4 km). The two grids are not interchangeable and cannot share a reprojection cache.

---

## Basic details
- **Model type:** Regional deterministic spectral wave model, coupled to ocean currents and sea ice
- **Core wave model:** **WAM Cycle 4.7.0** (`source = "WAM wave model version cycle 4.7.0"`)
- **Grid system:** Single regular grid in rotated lat-lon space
- **Horizontal resolution:** 0.03° rotated (≈3.34 km)
- **Spectral discretization:** **29 frequencies × 36 directions** (in the `_SPC` files)
  - Frequencies: geometric, ratio 1.1, from 0.034523 Hz to 0.497856 Hz (≈29.0 s to ≈2.0 s)
  - Directions: 5°, 15°, 25°, …, 355°
- **Forecast length:** **Alternating** — 120 h (5 days) from the 00 UTC production cycle, 240 h (10 days) from the 12 UTC production cycle
- **File time span:** 133 hourly steps (00 UTC cycle) or 253 hourly steps (12 UTC cycle), each beginning **12 h before the production cycle**
- **Update frequency / cycles:** 2× daily
- **Temporal output resolution:** 1 hour, instantaneous (`title = "hourly instantaneous wave fields"`)
- **Time encoding:** `seconds since 1970-01-01 00:00:00`, gregorian calendar, `dt_sec = 3600`
- **Conventions:** CF-1.6, ACDD-1.3

### Cycle timing — read this before writing any ingest code

The filename label and the `forecast_reference_time` variable are set to the **bulletin time, which is 6 hours later than the model production cycle.** Verified for `MyWave_wam3_WAVE_20260727T06Z.nc`:

| Quantity | Value |
|---|---|
| Filename label | `20260727T06Z` |
| `forecast_reference_time` | 2026-07-27T06:00Z (units: **days** since 1970-01-01 — note, not seconds) |
| `time[0]` | 2026-07-26T12:00Z |
| `time[132]` | 2026-08-01T00:00Z |

Interpreted against the **00 UTC production cycle** of 2026-07-27, the file spans exactly **T−12 h to T+120 h**. The 18Z-labelled file (`20260726T18Z`) spans 2026-07-26T00:00Z to 2026-08-05T12:00Z — exactly **T−12 h to T+240 h** against the **12 UTC** cycle. Both reduce to clean round numbers only under the production-cycle interpretation; against the file label they are the untidy −18 h/+114 h and −18 h/+234 h.

**Practical consequences:**
- `forecast_reference_time` is **not** the model initialization time. Treating it as such introduces a 6-hour error.
- `forecast_reference_time` is in **days**, while `time` is in **seconds**. Code that assumes a shared unit will be wrong by a factor of 86400.
- Each file carries a 12-hour hindcast/analysis segment before the forecast proper.

This matches the bulletin-time convention documented for [ARCWAM](./arcwam.md) ("production cycles 00 UTC and 12 UTC, bulletin times 06 UTC and 18 UTC respectively").

---

## Forcing and nesting
- **Wind forcing:** ECMWF. The production `history` chain shows an `EcFC.nc` (ECMWF forecast) file concatenated into the forcing bundle `W3km_force.nc`. The forcing winds are published in the output as `ff` (speed) and `dd` (direction).
- **Current forcing:** **Yes, actively forced.** Surface currents are published as `Current` (speed) and `Currentdir` (direction), with the long names explicitly reading *"from ocean model"*. **The source ocean model is not named in the file metadata (TBD).** ARCWAM's documentation attributes its currents to the Arctic tidal-resolving physics system; whether the same source feeds this distribution is unconfirmed.
- **Ice forcing:** **Yes.** Sea ice concentration (`SIC`) and sea ice thickness (`SIT`) are both forced and published. Source not named in metadata (TBD); ARCWAM documents TOPAZ5 for both.
- **Forcing assembly:** the `history` attribute shows nine `ocean_force_<CYCLE>.nc` files (6-hourly, spanning ~2 days) concatenated with `EcFC.nc` via `ncrcat`, with `fimex` used for the regridding step.
- **Lateral boundaries:** not applicable in the usual sense — the domain is global in longitude and reaches the pole.
- **Nested inside / parent for:** not documented, and this system is **not** the parent of the coastal chain — [MyWaveWAM800m](./mywavewam800m.md) takes its open-boundary spectra from [WW3 4 km](./met-norway-ww3.md)'s `C0`–`C4` `_SPC` files. Despite sharing the WAM 4.7.0 core with MyWaveWAM800m, the two are not in a nesting relationship and neither drives the other.

---

## Data assimilation
- **Assimilates wave observations:** **TBD.** Nothing in the file metadata indicates assimilation. However, [ARCWAM](./arcwam.md) — which this product closely resembles — has run multi-satellite altimetric OI assimilation of SWH and U10 since November 2024. If the two are the same production, this system assimilates too. **Do not assume either way without confirmation from MET Norway.** This is the single most consequential open question about the entry.

---

## What it provides

### Gridded fields (`MyWave_wam3_WAVE_<CYCLE>.nc`) — 43 populated variables

**Total sea state**
`hs` (total significant wave height) · `tp` (peak period) · `tmp` (mean period) · `tm1`, `tm2` (m1/m2 periods) · `thq` (mean direction) · `fpI` (interpolated peak frequency) · `Pdir` (peak direction)

**Wind sea partition**
`hs_sea` · `tp_sea` · `tmp_sea` · `tm1_sea` · `tm2_sea` · `thq_sea`

**Swell partition (aggregate)**
`hs_swell` · `tp_swell` · `tmp_swell` · `tm1_swell` · `tm2_swell` · `thq_swell`

**Individual swell systems**
`fshs`, `fstm1`, `fsdir` (first swell: height, mean period, direction) · `sshs`, `sstm1`, `ssdir` (second swell)

**Extreme-wave diagnostics**
`cmax_F` (height of the highest crest) · `Hmax_N` (maximum crest-to-trough wave height) · `cmax_st` (maximum crest height, space-time / STQD method)

**Stokes drift and transport**
`sdx`, `sdy` (surface Stokes drift, m/s) · `utrs`, `vtrs` (Stokes drift transport, m²/s)

**Air–sea and wave–ocean fluxes**
`phiaw` (energy flux from wind to waves, W m⁻²) · `phioc` (energy flux to ocean, W m⁻²) · `tauocx`, `tauocy` (momentum flux into ocean, N m⁻²)

**Forcing and static fields**
`ff`, `dd` (wind speed/direction) · `Current`, `Currentdir` (surface current speed/direction) · `SIC` (sea ice concentration) · `SIT` (sea ice thickness) · `model_depth` (water depth) · `latitude`, `longitude`

Fill value across geophysical fields is `-9999999.0`.

**Direction convention:** `thq` carries `standard_name = sea_surface_wave_to_direction`, and the spectra files state it explicitly: *"A direction of 0 degrees means a wave propagating towards the North (Oceanographic convention)."* This is the **"to" convention**. Many wave products (including much of the WW3 world) use "from"; mixing the two produces a 180° error.

### Empty placeholder variables — a real trap

Ten variables are **declared in the file but carry no data**. They appear as scalar `Int32` with the value `-2147483647`:

`FV` · `DC` · `mHs` · `mwp` · `tshs` · `tstm1` · `tsdir` · `phibot` · `taubot_x` · `taubot_y`

These are WAM output slots that are declared but not activated. **Confirmed by comparison with [MyWaveWAM800m](./mywavewam800m.md)**, which runs the same WAM 4.7.0 output template and populates all ten with proper long names: a third swell system (`tshs`/`tstm1`/`tsdir`), bottom-interaction fluxes (`phibot`/`taubot_x`/`taubot_y`), friction velocity (`FV`), drag coefficient (`DC`), and expected maximum wave height and period (`mHs`/`mwp`). The coastal configuration activates the shallow-water and multi-swell diagnostics that this offshore one leaves off.

Any script that enumerates variables programmatically and assumes each is a gridded field will pick these up and get garbage. Filter on rank or on the presence of a `units` attribute (the ten placeholders have none). If you are writing one reader for both products, the declared variable list is identical but the populated subset is not.

### Point directional spectra (`MyWave_wam3_SPC_<CYCLE>.nc`)
- `SPEC[time][y=1][x=711][freq=29][direction=36]`, Float32
- **711 points**, spanning **17.70°W – 78.83°E** and **52.39°N – 87.56°N**
- Accompanied by per-point `Pdir`, `dd`, `ff`, `hs`, `tp`, `thq_sea`, `thq_swell`, `model_depth`
- Point locations are given by 1-D `latitude`/`longitude` arrays; there is no station-name variable (TBD what the point set represents — buoy locations, boundary sets, or an operational point list)

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **CC BY 4.0** — declared in the gridded files: `license = "https://spdx.org/licenses/CC-BY-4.0.html (CC-BY-4.0)"`. Corroborated by MET Norway's general free-data licensing terms. Attribution to MET Norway required; no share-alike obligation. **Note:** the `_SPC` files carry **no** `license` attribute; MET Norway's server-wide licensing terms apply, but the per-file declaration is missing.
- **Is the data downloadable?** Yes — direct HTTP file download and OPeNDAP subsetting
- **Data formats:** NetCDF-4 (CF-1.6 / ACDD-1.3)
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS

### Distribution
- **Catalog (top):** https://thredds.met.no/thredds/fou-hi/mywavewam3.html
- **Latest files catalog:** https://thredds.met.no/thredds/catalog/fou-hi/mywavewam3_latest/catalog.html
- **File naming:** `MyWave_wam3_WAVE_YYYYMMDDTHHZ.nc` (gridded), `MyWave_wam3_SPC_YYYYMMDDTHHZ.nc` (spectra), with `HH` ∈ {06, 18}
- **HTTP:** `https://thredds.met.no/thredds/fileServer/fou-hi/mywavewam3_latest/MyWave_wam3_WAVE_YYYYMMDDTHHZ.nc`
- **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/fou-hi/mywavewam3_latest/MyWave_wam3_WAVE_YYYYMMDDTHHZ.nc`

### Retention — short, and there is no archive
**Live-measured 2026-07-27: 6 cycles of each file type, spanning 2026-07-24T18Z to 2026-07-27T06Z — a rolling window of roughly 3 days.**

Unlike [WW3 4 km](./met-norway-ww3.md), this product has **no long-term file archive and no best-estimate aggregation**. The catalog title says so explicitly: *"latest model runs only."* Verified — the `mywavewam3` catalog contains a single `catalogRef` to `mywavewam3_latest` and nothing else.

**If you need historical MyWaveWAM3km output, you must harvest it continuously.** For retrospective Arctic wave work, the alternatives are the Copernicus Marine [ARCWAM](./arcwam.md) rolling archive (analysis from 2021-10-01) or the NORA3 hindcast.

### File sizes (live-measured)
| File | 00 UTC cycle (06Z label) | 12 UTC cycle (18Z label) |
|---|---|---|
| `_WAVE_` | ~32.5 GB | ~61.5 GB |
| `_SPC_` | ~318 MB | ~604 MB |

The gridded files are **large** — a full 10-day cycle is over 60 GB. OPeNDAP variable/time subsetting is strongly preferable to whole-file download, particularly given MET Norway's no-parallel-downloads policy.

### Publication latency (live-measured across 6 cycles)
| Production cycle | Relative to file label | Relative to production cycle |
|---|---|---|
| 00 UTC (06Z label) | +3.86 h to +3.92 h | +9.86 h to +9.92 h |
| 12 UTC (18Z label) | +5.36 h to +5.69 h | +11.36 h to +11.69 h |

Latency is tight and consistent. The 10-day run naturally takes longer. `_SPC_` files land a few minutes before their `_WAVE_` siblings in every observed cycle.

### Terms of service
MET Norway's THREDDS is a shared public service and the operator explicitly asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs that cause traffic overload. WMS is provided but not recommended beyond simple demonstration. Status: https://status.met.no

---

## Relationship to other wave products

### The ARCWAM question — probably the same production, unconfirmed
MyWaveWAM3km and the Copernicus Marine [ARCWAM](./arcwam.md) product share a striking number of characteristics:

| Property | MyWaveWAM3km (verified) | ARCWAM (documented) |
|---|---|---|
| Operator | MET Norway | MET Norway (ARC MFC) |
| Core model | WAM Cycle 4.7.0 | WAM Cycle 4.7 |
| Resolution | ~3 km | 3 km |
| Native grid | Rotated lat-lon | Rotated spherical |
| Spectral discretization | 29 freq × 36 dir | 29 freq × 36 dir |
| Cycling | 2× daily | 2× daily |
| Forecast length | 120 h (00Z) / 240 h (12Z) | 120 h (00Z) / 240 h (12Z) |
| Bulletin times | 06Z / 18Z labels | 06 UTC / 18 UTC |
| Ice coupling | SIC + SIT forced | SIC + SIT from TOPAZ5 |
| Current coupling | Yes, "from ocean model" | Yes, from Arctic tidal system |
| Northern bound | 89.987°N | 89.99°N |
| Longitude | Global | Global |

The most economical explanation is that these are **two distributions of the same operational run** — MET Norway publishing the native rotated grid on its own THREDDS, and Copernicus Marine publishing a polar-stereographic reprojection under the ARC MFC product identifier.

**Two things do not match, and neither is resolved:**
1. **Southern bound.** MyWaveWAM3km reports 36.167°N; ARCWAM's Copernicus product extent is 41.12°N. Consistent with the Copernicus delivery being a subset of a larger native domain, but not proof.
2. **Delivered grid dimensions.** 2379 × 1995 rotated here; 2367 × 2467 polar stereographic for ARCWAM. Different projections make this uninformative on its own.

**This should be confirmed with MET Norway before the two entries are cross-linked as one system.** Until then both entries stand independently, with this section flagging the overlap. The practical stakes are real: if they are the same run, ARCWAM's November 2024 altimetric data assimilation applies to this product too, and this entry's "no documented DA" would be misleading.

### MET Norway's operational wave systems
- **This entry (MyWaveWAM3km)** — WAM Cycle 4.7.0 at ~3.34 km, pan-Arctic, 120/240 h alternating × 2 daily, current- and ice-forced. ~3-day rolling window, no archive, no aggregation.
- **[WW3 4 km](./met-norway-ww3.md)** — WAVEWATCH III v6.07 at ~4.45 km, Nordic/North/Baltic Seas, 66 h × 4 daily, no current forcing. ~11-day rolling window plus a full archive back to 2021-07-02 and a gap-free best-estimate aggregation. Parent of MyWaveWAM800m.
- **[MyWaveWAM800m](./mywavewam800m.md)** — WAM Cycle 4.7.0 at ~890 m, five Norwegian coastal domains, 66 h × 4 daily, MEPS winds and NorKyst v3 currents. Same model core and output template as this entry, different activated field set. Archive to 2023-03-30 plus per-domain aggregations.
- **[ARCWAM](./arcwam.md)** — Copernicus Marine distribution; see the overlap analysis above.
- **NORA3 / NORA10** — long-term hindcasts, distinct systems, not operational forecasts.

**Choosing among the THREDDS products:** this entry for Arctic coverage, medium range (out to 10 days), and wave–ice interaction — accepting that you must harvest continuously to retain anything. WW3 4 km for the shelf seas including the Baltic and for anything needing history. MyWaveWAM800m for nearshore and harbour-scale Norwegian work.

---

## Notes

- **`_SPC` files carry a wrong title.** The spectra files declare `title = "WINDSURFER/NORA3, hindcast hourly 2D wave spectra"`. This is an operational forecast, not a NORA3 hindcast — the attribute is a leftover from the hindcast production chain that shares the same post-processing scripts. The gridded files carry the correct `title = "hourly instantaneous wave fields"`. Do not use the `_SPC` title to identify the product.

- **`_SPC` files carry no `license` attribute**, unlike their gridded siblings. Same server, same terms, but the per-file declaration is absent — worth knowing if you build licence checks into an ingest pipeline.

- **The summary attribute is truncated mid-sentence.** `summary` ends with `"...drift of floating objects (“man-over-board”)` — no closing text. Cosmetic, but a signal that the metadata block is not closely maintained.

- **"Nordic Seas" undersells the domain.** The catalog title reads *"MyWaveWAM3km Nordic Seas wave forecasting system"*, but the grid is global in longitude and reaches from 36°N to the pole. Users searching for an Arctic or North Atlantic wave product will not find this under that name.

- **Mixed time units within one file.** `time` is seconds since epoch; `forecast_reference_time` is *days* since epoch. Both use the same epoch string format, which makes the difference easy to miss.

- **Wave–ocean coupling fields are published.** `phioc`, `phiaw`, `tauocx`, `tauocy`, and the Stokes drift/transport set make this directly usable as forcing for a coupled ocean model, not just as a sea-state forecast.

- **Naming.** MET Norway uses `MyWave_wam3` in filenames, `mywavewam3` in catalog paths, and "MyWaveWam3km" / "MyWaveWAM3km" inconsistently in catalog titles. This entry uses **MyWaveWAM3km**. The "MyWave" prefix derives from the FP7 MyWave project.

---

## Official documentation
- MyWaveWAM3km catalog: https://thredds.met.no/thredds/fou-hi/mywavewam3.html
- Latest files catalog: https://thredds.met.no/thredds/catalog/fou-hi/mywavewam3_latest/catalog.html
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/fou-hi/fou-hi.html
- MET Norway ocean and wave model overview: https://ocean.met.no/models
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/

> **Documentation gap:** MET Norway publishes no model description page, PUM, or validation report for this product. The catalog page carries only a title and the standard terms of service; everything in this entry above the *Official documentation* section was derived from file metadata and live catalog inspection. The [ARCWAM](./arcwam.md) Copernicus PUM is the nearest thing to formal documentation, with the caveat that the two systems are not confirmed to be the same.
