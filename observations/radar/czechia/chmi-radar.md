# ČHMÚ Radar (Czechia — CZRAD: Single-Site Volumes & National Composites)

## What this is
The Czech Hydrometeorological Institute (ČHMÚ / CHMI) publishes the output of
its national weather-radar network **CZRAD** openly through the ČHMÚ Open Data
server (`opendata.chmi.cz`). This entry covers the **observational** products:
the full dual-polarimetric **3D volume data** from each of the two radars, and
the national **2D composite products** (maximum reflectivity, 2 km
pseudo-CAPPI, echo-top height, and a radar+gauge 1-hour precipitation
estimate). All are freely downloadable with no account, in ODIM HDF5.

The same `composite/` tree also carries **extrapolation-forecast** products
(`fct_maxz`, `fct_pseudocappi2km`) — these are COTREC nowcasts and belong in
the catalog's nowcasting area rather than here; they are itemised in the Scope
note. Czechia is a EUMETNET OPERA member, so — as with Germany, the UK, and the
Nordics — these ČHMÚ products are the national counterpart to the OPERA
pan-European composite.

---

## Who operates it
- **Operator:** Czech Hydrometeorological Institute (Český hydrometeorologický ústav, ČHMÚ / CHMI).
- **Country / region:** Czechia.
- **Data distributor:** ČHMÚ Open Data (`opendata.chmi.cz`); WMO location indicator **OKPR** (Prague).
- **Network name:** CZRAD.

---

## Network composition
CZRAD is a **two-radar C-band dual-polarisation** network:

| Site | Code | Position | Antenna height | Bulletin suffix |
|---|---|---|---|---|
| **Brdy-Praha** | `brd` | E 13.8178°, N 49.6583° | 916 m | `60` |
| **Skalky** | `ska` | E 16.7885°, N 49.5011° | 767 m | `50` |

Each radar delivers the full polarimetric moment set — corrected reflectivity
**Z** (DBZH), uncorrected reflectivity **U** (TH), radial velocity **V**
(VRADH), spectral width **W** (WRADH), differential reflectivity **ZDR**,
correlation coefficient **RhoHV**, and differential phase **PhiDP**. The main
volume scan runs **12 elevations** (0.1°, 0.5°, 0.9°, 1.3°, 1.7°, 2.2°, 3.2°,
4.5°, 6.3°, 8.7°, 13.7°, 21.6°) every 5 minutes, with **two supplementary
low-level scans** (0.3° and 1.5°, optimised for radial velocity) on a 10-minute
step. Range is 260 km; radial resolution 400 m below 6.3° and 200 m above;
azimuthal resolution 1°. National composites blend both radars (with foreign
radars filled in during outages) on the CZRAD grid.

---

## Products
**Single-site volume data** (`sites/{brd,ska}/vol_*/hdf5/`) — 3D polar volumes,
ODIM HDF5 v2.0, one folder per moment, 5-minute main scan:
- `vol_z` (Z / DBZH), `vol_u` (U / TH), `vol_v` (V / VRADH), `vol_w` (W / WRADH),
  `vol_zdr` (ZDR), `vol_rhohv` (RhoHV), `vol_phidp` (PhiDP).
- Each folder also carries the supplementary 0.3° / 1.5° low-level sweeps.

**Composite / mosaic products** (`composite/*/hdf5/`) — 2D grids, ODIM HDF5
v2.4, EPSG:3857, 1×1 km:
- **MAX_Z** (`maxz`) — column-maximum reflectivity; 5-minute.
- **PseudoCAPPI_2km** (`pseudocappi2km`) — reflectivity at 2 km MSL; the
  operational precipitation-intensity product (Marshall-Palmer Z–R); 5-minute.
- **Echo_Top** (`echotop`) — max height of ≥4 dBZ reflectivity; 5-minute.

**Precipitation product** (`composite/merge1h/hdf5/`):
- **MERGE** — 1-hour precipitation estimate merging both radars' QPE with ČHMÚ
  and partner **rain-gauge** data via kriging with external drift; 10-minute.
  (Multi-sensor analysis — not raw radar.)

---

## Data availability
- **Is the data free?** Yes — no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** ODIM HDF5 — v2.0 for single-site volumes, v2.4 for composites. (Rendered PNG duplicates exist alongside each composite — see *Notes*; these are not the data feed.)
- **Update cadence:** Main volume scan and MAX_Z / PseudoCAPPI_2km / Echo_Top every 5 minutes; supplementary low-level sweeps and MERGE every 10 minutes. Near-real-time (latest composite typically only a few minutes old, observed).
- **Primary access:** plain HTTPS directory tree (curl/wget) — https://opendata.chmi.cz/meteorology/weather/radar/
  - Volumes: `sites/<site>/vol_<moment>/hdf5/` — filename `T_PA<code><site>_C_OKPR_<YYYYMMDDhhmmss>.hdf`, where the moment/scan code is:
    - Z → `XX` = `GZ` (main) / `YA` (0.3°) / `YB` (1.5°); e.g. `T_PAGZ60_C_OKPR_20260712165026.hdf`
    - U → `J`, V → `H`, W → `I`, ZDR → `K`, RhoHV → `L`, PhiDP → `Q`, each followed by scan char `Z` (main) / `A` (0.3°) / `B` (1.5°); e.g. `T_PAHZ50_C_OKPR_…hdf` = Skalky velocity main scan.
    - Timestamp is the end of the lowest measured elevation, UTC.
  - Composites: `composite/<product>/hdf5/` — `T_PA{B,N,D,S}V23_C_OKPR_<YYYYMMDDhhmmss>.hdf` (MAX_Z `B`, PseudoCAPPI_2km `N`, Echo_Top `D`, MERGE `S`); timestamp = end of the accumulation/selection interval, UTC.
- **New-data notifications:** None (no S3/SNS/API); poll the directory tree.
- **Other mirrors:** None known (single HTTPS origin).
- **Archive depth:** Short near-real-time rolling window — observed ≈4 days for single-site volumes and ≈7 days for composites at time of verification. Not a deep/climatological archive.
- **Licence:** CC BY 4.0, attribution to ČHMÚ (Czech Hydrometeorological Institute). This is ČHMÚ's blanket open-data policy for `opendata.chmi.cz`; the HDF5 files carry no per-file `license` tag, so the licence reflects the data policy rather than an embedded attribute (as with MET Norway).

---

## Scope note
- **Observation vs forecast — this tree mixes both.** In scope here: the single-site volumes and the MAX_Z / PseudoCAPPI_2km / Echo_Top / MERGE composites. **Out of scope for this entry**, because they are nowcasts that belong in `nowcasting_models/`:
  - **FCT_MAX_Z** (`composite/fct_maxz/`) and **FCT_PseudoCAPPI_2km** (`composite/fct_pseudocappi2km/`) — **COTREC** extrapolation forecasts (echoes advected along motion vectors from the last two MAX_Z composites; intensities held constant), out to +10…+60 min in 10-minute steps, packaged one calculation time per `T_PA{B,N}V23_C_OKPR_<YYYYMMDD.hhmm>.ft60s10.tar` archive.
  These are flagged as a future ČHMÚ nowcasting entry (COTREC), not documented here.
- **Rendered PNG is not the feed.** Each composite (and forecast) is also published as rendered PNG (`png/`, and `png_masked/` for MAX_Z / FCT_MAX_Z) with a fixed colour scale — these are viewer imagery, out of scope as data. The ODIM HDF5 grid is the feed.

---

## Notes
- **Observation, not forecast.** Single-site polarimetric volumes plus national reflectivity/echo-top composites; the MERGE product additionally blends rain-gauge data (kriging with external drift), so it is a multi-sensor analysis rather than raw radar.
- **Relationship to OPERA.** Czechia is a EUMETNET OPERA member; the CZRAD radars contribute to the OPERA pan-European composite, and this ČHMÚ feed is the national-resolution counterpart.
- **ODIM HDF5.** OPERA Data Information Model HDF5 — readable with `wradlib`, `xradar`, `h5py`, or Py-ART. Grid/geometry is carried in the ODIM `/where` group; volumes are v2.0, composites v2.4.
- **Data feed vs viewer.** ČHMÚ's public radar map at chmi.cz is rendered; the opendata tree here is the data. The `png` / `png_masked` files are the rendered composites, not the gridded feed.
- **Single-site vs composite.** `sites/<site>/` = per-radar polar volumes; `composite/` = the two-radar national 1 km grids.
- **Colour scales.** `radar/scl/scl-dbz-mmh.png` (dBZ + mm/h) and `radar/scl/scl-mm.png` (mm) accompany the rendered PNGs.
- **Masked display.** `png_masked` (MAX_Z, FCT_MAX_Z) desaturates areas where PseudoCAPPI_2km < 7 dBZ, i.e. where precipitation is judged not to reach the surface.

---

## Recent version history
- **2026-05 — supplementary low-level scans added** (0.3° and 1.5° sweeps, 10-minute step, optimised for radial velocity) to the single-site HDF5 volumes (dataset description v1.3).
- **2024-12 — extrapolation forecasts and PNG composites added** to the open feed (dataset description v1.2).
- **2024-06 — initial open release** of CZRAD volumes and composites on `opendata.chmi.cz` (dataset description v1.0/1.1).

---

## Official documentation
- ČHMÚ radar open-data tree: https://opendata.chmi.cz/meteorology/weather/radar/
- Dataset description (EN): https://opendata.chmi.cz/meteorology/weather/radar/radar_description_en.pdf
- Dataset description (CZ): https://opendata.chmi.cz/meteorology/weather/radar/radar_popis_cz.pdf
- ČHMÚ open-data terms (CC BY 4.0): https://www.chmi.cz/o-chmu/caste-dotazy-faq/open-data
- ODIM HDF5 v2.0 spec: https://www.eumetnet.eu/wp-content/uploads/2019/05/OPERA-ODIM_H5-v2.01.pdf
- ODIM HDF5 v2.4 spec: https://www.eumetnet.eu/wp-content/uploads/2021/07/ODIM_H5_v2.4.pdf
