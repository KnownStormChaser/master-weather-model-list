# WAVEWATCH III 4 km — MET Norway (Nordic Seas, North Sea, Baltic Sea)

## What this model is
`ww3_4km` is MET Norway's **operational third-generation spectral wave forecast system**, built on WAVEWATCH III and run four times daily over the Nordic Seas, North Sea, and Baltic Sea. It is the wave component of MET Norway's operational marine forecast chain and the system that supplies boundary spectra to the WAM800m coastal wave models.

The model is phase-averaging: it does not resolve individual waves but produces a statistical representation of the sea state as a superposition of wave fields of differing origin, propagated in space and time under source and sink terms.

Alongside integral wave parameters on a gridded domain, the system publishes **directional wave spectra at fixed point sets** — five (nominally) boundary sets feeding the WAM800m coastal areas, plus a "Points Of Interest" set within the model domain.

*Live-verified against the operational THREDDS distribution on 2026-07-27 (00/06/12Z cycles of 2026-07-27 and surrounding archive).*

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (MET Norway)
- **Country / region:** Norway
- **Producing unit:** Research and Development Department (`creator_email = fou-om@met.no`)
- **Publisher:** MET Norway / Arctic Data Centre (`adc-support@met.no`)
- **Project attribution:** MET Core Service
- **Operational status:** `operational_status = "Operational"` (file global attribute)

---

## What area it covers
- **Coverage:** Nordic Seas, North Sea, and **Baltic Sea** — per the file global attribute `area = "4km operational domain covering: Nordic Seas, North Sea, Baltic Sea"`
- **Geospatial bounds (file attributes):** 43°N – 85°N, 31°W – 94°E
- **Grid dimensions:** 624 (`rlon`) × 1026 (`rlat`)
- **Native grid:** Rotated latitude–longitude
  - `grid_mapping_name = rotated_latitude_longitude`
  - `grid_north_pole_longitude = 140.0`, `grid_north_pole_latitude = 22.0`
  - `earth_radius = 6371000.0`
  - proj4: `+proj=ob_tran +o_proj=longlat +lon_0=-40 +o_lat_p=22 +R=6.371e+06 +no_defs`
- **Rotated-coordinate extent:** `rlon` 5.53° → 30.45°, `rlat` −14.35° → 26.65°, uniform 0.04° spacing
- **True latitude/longitude:** provided as 2-D auxiliary variables `latitude[rlat][rlon]` and `longitude[rlat][rlon]` — required for georeferencing; the `rlat`/`rlon` axes are rotated-pole coordinates, not geographic ones
- **Land/sea and status:** `MAPSTA` (WW3 status map) and `dpt` (depth) are carried in every file

**Note on the "4 km" label:** the grid is uniform 0.04° in rotated coordinates, which is **≈4.45 km** on the stated earth radius. "4 km" is the product name, not the exact cell size.

---

## Basic details
- **Model type:** Regional deterministic spectral wave model
- **Core wave model:** WAVEWATCH III **v6.07** (`WAVEWATCH_III_version_number = "6.07"`)
- **Grid system:** Single regular grid in rotated lat-lon space
- **Horizontal resolution:** 0.04° rotated (≈4.45 km)
- **Spectral discretization:** 36 frequencies × 36 directions (carried in the `_SPC` point-spectra files; the gridded product carries integral parameters only)
- **Forecast length:** **66 hours** from the cycle reference time
- **File time span:** **72 hours / 73 hourly steps**, running from **T−6 h to T+66 h** relative to `forecast_reference_time`
- **Update frequency / cycles:** 4× daily, 00 / 06 / 12 / 18 UTC
- **Temporal output resolution:** 1 hour
- **Time encoding:** `seconds since 1970-01-01T00:00:00+00:00`, standard calendar
- **Conventions:** CF-1.6, ACDD-1.3
- **Compilation switches:** `F90 NOGRB NC4 TRKNC DIST MPI PR2 UNO FLX0 LN1 ST4 STAB0 NL1 BT4 DB1 MLIM TR0 BS0 IC1 IS2 REF1 XX0 WNT1 WNX1 RWND CRT1 CRX1 O0 O1 O2 O2a O2b O2c O3 O4 O5 O6 O7`
  - `ST4` — Ardhuin et al. input/dissipation
  - `IC1 IS2` — ice-induced dissipation and ice scattering
  - `REF1` — shoreline reflection
  - `CRT1 CRX1` — current interaction compiled in (see *Forcing* caveat below)

**Cycle semantics, verified:** for `ww3_20260727T12Z.nc`, `forecast_reference_time = 2026-07-27T12:00Z` (matching the filename label) while `time[0] = 2026-07-27T06:00Z`. Each file therefore begins with a **6-hour segment preceding its nominal cycle time**. Code that assumes `time[0]` equals the cycle hour will be off by six hours.

---

## Forcing and nesting
The file-level `Forcing` global attribute reads exactly: `EC_WINDS MEPS_WINDS OSISAF_ICE`.

- **Wind forcing:** ECMWF IFS winds over the open ocean, blended with [MEPS](../../../nwp_models/regional/norway/meps.md) (HARMONIE-AROME, 2.5 km) over the MetCoOp domain. The forcing winds are echoed into the output as `uwnd` / `vwnd` (and `ff` / `dd` as speed/direction), so the blend is inspectable directly from the distributed files.
- **Ice forcing:** OSI SAF sea ice concentration; carried in output as `ice` (sea ice area fraction).
- **Current forcing:** **TBD.** The `CRT1`/`CRX1` switches indicate current interaction is compiled into the executable, but the `Forcing` attribute lists no current source. Whether currents are actually supplied operationally is unconfirmed.
- **Lateral boundary spectra:** **TBD.** The previous version of this entry stated ECMWF ECWAM boundary spectra. That is **not corroborated** by any file or catalog metadata and should be treated as unverified until confirmed with MET Norway.
- **Parent for:** supplies boundary wave spectra to MET Norway's **WAM800m** coastal wave areas via the `C*_SPC` files (see below). The WAM800m system is not yet documented in this repository.

---

## Data assimilation
- **Assimilates wave observations:** No documented assimilation. Nothing in the file metadata, switch list, or catalog description indicates altimeter or SAR assimilation. This distinguishes `ww3_4km` from the sibling [ARCWAM](./arcwam.md) product, which does assimilate. *(Absence of documentation, not a positive confirmation of absence.)*

---

## What it provides

### Gridded fields (`ww3_<CYCLE>.nc`) — 50 variables, live-enumerated

**Integral sea state**
`hs` (significant height of wind and swell waves, m) · `lm` (mean wave length, m) · `t01`, `t02`, `t0m1` (mean periods, s) · `fp` (peak frequency, s⁻¹) · `tp` (peak period, s) · `dir` (mean direction, °) · `dp` (peak direction, °) · `spr` (directional spread, °)

**Extreme-wave diagnostics**
`hmaxe`, `hcmaxe` (expected maximum wave height, and from crest — linear 1st order, m) · `hmaxd`, `hcmaxd` (standard deviations of the same, m)

**Two-partition decomposition** (partitions 0 and 1)
`phs0`/`phs1` (partition significant height) · `ptp0`/`ptp1` (partition peak period) · `pdir0`/`pdir1` (partition mean direction) · `pt01c0`/`pt01c1` and `pt02c0`/`pt02c1` (partition mean periods) · `fws` (wind-sea mean period T0m1)

*Only two partitions are published — conventionally wind sea and the leading swell in WW3 output, though the mapping is not stated in the file metadata (TBD).*

**Air–sea and wave–ocean fluxes**
`uust`/`vust` (friction velocity) · `cha` (Charnock coefficient) · `faw` (wind-to-wave energy flux, W m⁻²) · `utaw`/`vtaw` (wave-supported wind stress) · `utwo`/`vtwo` (wave-to-ocean stress) · `foc` (wave-to-ocean energy flux)

**Stokes drift and transport**
`uuss`/`vuss` (surface Stokes drift, m s⁻¹) · `utus`/`vtus` (Stokes transport, m² s⁻¹)

**Wave–ice interaction**
`utic`/`vtic` (wave-to-sea-ice stress) · `fic` (wave-to-sea-ice energy flux, W m⁻²)

**Forcing and static fields**
`uwnd`/`vwnd`, `ff`/`dd` (10 m wind) · `ice` (sea ice fraction) · `dpt` (depth) · `MAPSTA` (status map) · `latitude`/`longitude` (2-D geographic coordinates)

Fill value across geophysical fields is `-999.0`.

### Point directional spectra (`ww3_<SET>_SPC_<CYCLE>.nc`)
Each spectra file carries `SPEC[time=73][y=1][x=N][freq=36][direction=36]` as Float64, with `latitude`/`longitude` per point.

| File | Documented area | Points (`x`) | Longitude range | Latitude range | Size |
|---|---|---|---|---|---|
| `C0_SPC` | Finnmark | 611 | 26.48° – 33.66°E | 69.90° – 71.74°N | 463.7 MB |
| `C1_SPC` | NordNorge | 1328 | 9.49° – 29.02°E | 66.20° – 72.01°N | 1.007 GB |
| `C2_SPC` | MidtNorge | 763 | 7.07° – 14.64°E | 63.01° – 67.83°N | 579.1 MB |
| `C3_SPC` | Vestlandet | 283 | 2.78° – 8.07°E | 57.93° – 63.61°N | 214.8 MB |
| `C4_SPC` | Skagerrak | 566 | 5.55° – 11.12°E | 57.10° – 59.00°N | 429.6 MB |
| `C5_SPC` | *undocumented* | 1328 | 9.49° – 29.02°E | 66.20° – 72.01°N | 1.007 GB |
| `POI_SPC` | Points Of Interest | 923 | — | — | 700.5 MB |

**Direction convention:** verified `direction = 5, 15, 25, ... 355` — the offset convention introduced 15 May 2023 (previously 0, 10, 20, ... 350). This affects the `_SPC` files only; gridded output is unaffected.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **CC BY 4.0** — declared in every file: `license = "https://spdx.org/licenses/CC-BY-4.0.html (CC-BY-4.0)"`. Corroborated by MET Norway's general free-data licensing terms. Attribution to MET Norway required; no share-alike obligation.
- **Is the data downloadable?** Yes — direct HTTP file download and OPeNDAP subsetting
- **Data formats:** NetCDF-4 (CF-1.6 / ACDD-1.3)
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS. The aggregation additionally exposes NCML, ISO, and UDDC endpoints.

### Distribution channels

**1. Latest files** (rolling window)
- Catalog: https://thredds.met.no/thredds/catalog/ww3_4km_latest_files/catalog.html
- File naming: `ww3_YYYYMMDDTHHZ.nc`, `ww3_<SET>_SPC_YYYYMMDDTHHZ.nc`
- HTTP: `https://thredds.met.no/thredds/fileServer/ww3_4km_latest_files/ww3_YYYYMMDDTHHZ.nc`
- OPeNDAP: `https://thredds.met.no/thredds/dodsC/ww3_4km_latest_files/ww3_YYYYMMDDTHHZ.nc`
- **Retention (live-measured 2026-07-27):** **45 cycles per file type ≈ 11.25 days**, spanning 2026-07-16T12Z through 2026-07-27T12Z. The catalog description says "the last few days" — the actual window is considerably longer.
- **Gridded file size:** 8.793 GB per cycle

**2. Archive** (long-term)
- Catalog: https://thredds.met.no/thredds/catalog/ww3_4km_archive_files/catalog.html
- Directory structure: `YYYY/MM/DD/`
- **File naming differs from the latest feed:** `ww3_4km_YYYYMMDDTHHZ.nc` (note the extra `4km_`). Scripts that walk from the latest feed into the archive must switch prefix.
- **Archive genuinely starts 2021-07-02** (first available cycles: 06Z, 12Z, 18Z that day; no 00Z). Year directories for 2016–2021 exist in the DatasetScan and are browsable but **empty** — the catalog's stated 2021/07/02 start is correct and the earlier directories are an artifact.
- All eight file types (gridded + C0–C5 + POI) are retained in the archive.

**3. Best-estimate aggregation** (continuous time series)
- OPeNDAP: `https://thredds.met.no/thredds/dodsC/ww3_4km_agg`
- **Live-measured span:** **28,704 contiguous hourly steps, 2023-04-20T06:00Z → 2026-07-29T05:00Z** (i.e. through the end of the current forecast). No gaps in the time axis.
- Constructed from the first 6 hours of each cycle tiled together, extended by the most recent forecast. Missed production runs are gap-filled by the most recent forecast covering the same period.
- Carries the same 1026 × 624 grid and the same CC BY 4.0 licence declaration.
- **This is the practical route for hindcast/climatology work** — a single OPeNDAP endpoint gives 3+ years of continuous hourly analysis-quality wave fields without touching the file archive.

### Terms of service
MET Norway's THREDDS is a shared public service and the operator explicitly asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs that cause traffic overload. WMS is provided but is not recommended for anything beyond simple demonstration. Planned maintenance and incidents: https://status.met.no

---

## Notes

- **Coverage correction.** Earlier versions of this entry described the domain as "North Atlantic, Nordic Seas, and adjacent Arctic waters" and omitted the Baltic Sea. The operational file metadata is explicit that the domain covers **Nordic Seas, North Sea, and Baltic Sea**. Baltic coverage is a substantive difference for users choosing between this and the Copernicus Baltic wave products.

- **C5_SPC is undocumented and appears to duplicate C1_SPC.** The catalog description names five WAM800m boundary sets (C0–C4), but **six** are published. `C5_SPC` has an identical point count (1328), identical geographic extent, identical file size (1.007 GB), and identical sampled spectral values to `C1_SPC` at the points checked. `C5_SPC` is present throughout both the latest feed and the archive, so it is not a transient artifact. Whether it is a genuine sixth WAM800m area whose configuration has not yet diverged, a staging duplicate, or a documentation lag is **TBD** — worth confirming with MET Norway before building against it.

- **Rotated-pole georeferencing.** The `rlat`/`rlon` axes are rotated-pole coordinates. Use the 2-D `latitude`/`longitude` auxiliary variables or the supplied proj4 string; treating `rlat`/`rlon` as geographic degrees will place the field in the wrong hemisphere.

- **Six-hour lead-in.** Every file starts at T−6 h relative to its label. This is what makes the best-estimate aggregation contiguous, but it means a naive "forecast hour = time index" mapping is wrong by six steps.

- **Field set is unusually rich for a wave product.** Stokes drift and transport, wave-to-ocean and wave-to-ice stress and energy flux, Charnock coefficient, and expected-maximum-wave statistics are all published as raw fields. This makes the product directly usable for coupled ocean/ice forcing and for offshore extreme-wave applications, not just conventional sea-state forecasting.

- **Relationship to MET Norway's other wave systems.** MET Norway operates several distinct wave systems, and picking the wrong one is easy:
  - **This entry (`ww3_4km`)** — WAVEWATCH III v6.07 at ~4.4 km, Nordic/North/Baltic Seas, 66 h × 4 daily, no DA, own THREDDS, CC BY 4.0.
  - **[ARCWAM](./arcwam.md)** — WAM Cycle 4.7 at 3 km, full Arctic, 5/10-day alternating × 2 daily, multi-satellite DA, coupled to ARC MFC physics/ice, distributed via Copernicus Marine.
  - **MyWaveWAM3km** (Nordic Seas) and **MyWaveWAM800m** (Norwegian coastal, with currents) — WAM-based, on the same THREDDS server, **not yet documented in this repository**. The `C0`–`C5` spectra files from this entry are the boundary input to the 800 m coastal areas.
  - **NORA10 / NORA3** — long-term hindcasts, distinct systems, not operational forecasts.

- **Naming.** MET Norway uses `ww3_4km` as the internal product identifier; the catalog title is "WAVEWATCH III 4km regional wave model". There is no separate marketing short-name.

---

## Recent version history

### 2023-04-20 — Best-estimate aggregation start
Earliest timestamp in the continuous `ww3_4km_agg` series (live-measured). The aggregation window does not extend back to the file-archive start.

### 2023-05-15 — Point-spectra direction convention change
Output directions for point spectra shifted from 0, 10, 20, …, 350 to **5, 15, 25, …, 355**. Affects `*_SPC` files only; gridded output unchanged. Verified in current files. Users combining pre- and post-May-2023 spectra must account for the 5° offset.

### 2021-07-02 — File archive start
First cycles in the long-term archive (06Z onward). Earlier year directories exist but are empty.

### Undated — `tp` added post-production
File history records that peak period `tp` was added to the output during the post-processing adjustment step rather than being written by WW3 directly, alongside CF-convention attribute harmonization. Note that `tp` and `fp` are both published and should be mutually consistent.

---

## Official documentation
- WW3 4 km catalog (model description and dataset tree): https://thredds.met.no/thredds/fou-hi/ww3_4km.html
- Latest files catalog: https://thredds.met.no/thredds/catalog/ww3_4km_latest_files/catalog.html
- Archive catalog: https://thredds.met.no/thredds/catalog/ww3_4km_archive_files/catalog.html
- MET Norway ocean and wave model overview: https://ocean.met.no/models
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no
- MET Norway NWP documentation wiki: https://github.com/metno/NWPdocs/wiki
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/
- WAVEWATCH III model documentation (NOAA/NCEP): https://github.com/NOAA-EMC/WW3
