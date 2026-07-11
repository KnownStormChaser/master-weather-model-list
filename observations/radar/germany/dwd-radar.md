# DWD Radar (Germany — RADOLAN, Reflectivity Composites & Single-Site)

## What this is
The Deutscher Wetterdienst (DWD) publishes its national weather-radar products
openly through the DWD Open Data server. This entry covers the **observational**
products: RADOLAN quantitative precipitation estimation (radar QPE, both
gauge-adjusted and radar-only), the national radar reflectivity composites, and
single-site polarimetric moment data. All are freely downloadable with no
account.

The same directory tree also contains **nowcast/forecast** products (RADVOR, the
RADOLAN-RV nowcast steps, and KONRAD3D cell/mesocyclone detection). Those are
forecast systems and belong in the catalog's nowcasting area rather than here;
they are itemised in the Scope note. Germany is a EUMETNET OPERA member, so — as
with the UK and the Nordics — these DWD products are the national counterparts to
the OPERA pan-European composite.

---

## Who operates it
- **Operator:** Deutscher Wetterdienst (DWD).
- **Country / region:** Germany.
- **Data distributor:** DWD Open Data (`opendata.dwd.de`).

---

## Network composition
DWD operates a national network of around 17 C-band dual-polarisation radars
(site codes include `asb`, `boo`, `drs`, `eis`, `ess`, `fbg`, `fld`, `hnr`, and
others). The radars deliver the full polarimetric moment set (reflectivity Z,
differential reflectivity ZDR, correlation coefficient RhoHV, differential phase
PhiDP, Doppler velocity V). National products are gridded on the RADOLAN
polar-stereographic grid (1 km; classic 900 × 900, extended to 1100 × 900) for
the RADOLAN QPE line, and on the newer **DE1200** grid (1200 × 1100, 1 km) for
the WN reflectivity composite and RV. RADOLAN's "online adjustment" ties the
radar QPE to the DWD rain-gauge network.

---

## Products
**RADOLAN quantitative precipitation estimation** (`radolan/`) — provided in both
the classic RADOLAN binary (`.bin.bz2`) and ODIM HDF5 (`.hdf5`):
- **RY** — 5-minute quality-controlled radar precipitation rate (radar-only, not gauge-adjusted).
- **RW** — 60-minute gauge-adjusted precipitation, produced every 10 minutes (sliding hourly sum).
- **SF** — 24-hour gauge-adjusted precipitation, produced hourly (sliding daily sum).
- **YW** — 5-minute gauge-adjusted precipitation.

**Reflectivity composites** (`composite/`):
- **WN** — 5-minute national reflectivity composite (dBZ), as `WN<YYMMDDHHMM>.tar.bz2` (ODIM HDF5 inside), on the DE1200 grid.
- **PG** — legacy composite reflectivity product (`.buf`, GTS bulletin `PAAH21 EDZW`), lower resolution.
- **Derived composites** — hail (`hg`), vertically integrated ice/liquid (`vii`), hydrometeor classification (`hymecng`), and others (`hx`, `dmax`, `rs`); see DWD documentation for the full product catalog.

**Single-site** (`sites/`):
- **`sweep_vol_*` and `sweep_pcp_*`** — per-radar polarimetric moment sweeps (Z, ZDR, RhoHV, PhiDP, V) in ODIM HDF5, one subfolder per site (`asb`, `boo`, `drs`, …).
- **Legacy single-site products** — older DWD formats (`dx`, `px`, `px250`, `pz`, `pe`, `pf`, `pr`, `lmax`).

---

## Data availability
- **Is the data free?** Yes — no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** RADOLAN binary (`.bin.bz2`), ODIM HDF5 (`.hdf5`), tar.bz2 (HDF5 composites), and BUFR-style `.buf` (legacy PG).
- **Update cadence:** RY / WN / PG every 5 minutes; RW every 10 minutes (hourly sum); SF hourly (24-hour sum).
- **Primary access:** plain HTTPS directory tree (curl/wget) — https://opendata.dwd.de/weather/radar/
  - RADOLAN: `/weather/radar/radolan/{ry,rw,sf,yw}/` — filename `raa01-<prod>_10000-<YYMMDDHHMM>-dwd---bin.bz2` (and `.hdf5`); e.g. `raa01-ry_10000-2607092245-dwd---bin.hdf5`.
  - Composite: `/weather/radar/composite/{wn,pg,hg,vii,...}/` — e.g. `WN<YYMMDDHHMM>.tar.bz2`.
  - Sites: `/weather/radar/sites/sweep_vol_z/<site>/`, etc.
  - A `content.log.bz2` at each level lists recent file additions (useful for polling).
- **New-data notifications:** none (no S3/SNS/API); poll the directory tree or watch `content.log.bz2`.
- **Archive depth:** DWD Open Data is a short near-real-time rolling window (hours). For long-term records and climatology, use the DWD Climate Data Center (CDC) RADOLAN / RADKLIM archives (`opendata.dwd.de/climate_environment/CDC/grids_germany/`).
- **Licence:** Free and open under DWD's terms (GeoNutzV; effectively CC BY 4.0), with mandatory attribution to DWD ("Source: Deutscher Wetterdienst").

---

## Scope note
- **Observation vs forecast — this tree mixes both.** In scope here: RADOLAN QPE (RW/RY/SF/YW), reflectivity composites (WN/PG and derived diagnostics), and single-site moments. **Out of scope for this entry**, because they are forecast/nowcast systems that belong in `nowcasting_models/`:
  - **RADVOR** (`radvor/re`, `radvor/rq`) — radar precipitation nowcast to +2 hours.
  - **RADOLAN-RV** (`composite/rv`, `DE1200_RV*`) — a 5-minute QPE analysis plus a +5…+120 min nowcast; the t0 field is observational, but the product is primarily a nowcast, so treat it as a nowcasting candidate.
  - **KONRAD3D** (`konrad3d/`) and **mesocyclones** (`mesocyclones/`) — object-based convective-cell and mesocyclone detection/tracking, distributed as XML feature lists (not gridded fields); KONRAD3D includes cell nowcasts.
  These are flagged as future `nowcasting_models/` entries (DWD RADVOR / RADOLAN-RV), not documented here.
- **Single-site moments** are raw structured (polar) data, in on the same basis as the NEXRAD and OPERA single-site families.
- **National composite, complementary to OPERA** (Germany is an OPERA member).

---

## Notes
- **Dual format.** RADOLAN products are published in both the legacy RADOLAN binary and ODIM HDF5. New users should prefer the HDF5 (readable with `wradlib`, `xradar`, `h5py`); `wradlib` also reads the RADOLAN binary and provides RADOLAN/DE1200 grid helpers.
- **Gauge adjustment.** RW/SF/YW are radar QPE adjusted against the DWD rain-gauge network (RADOLAN online adjustment); RY is radar-only, quality-controlled but not gauge-adjusted.
- **Two grids.** RADOLAN polar-stereographic national grid (1 km) for RW/RY/SF/YW; DE1200 (1200 × 1100, 1 km) for WN/RV. Reproject before combining.
- **Data feed vs viewer.** DWD's WarnWetter app and web radar viewer are rendered; the opendata tree here is the data.
- **Archive split.** Near-real-time here; multi-year RADOLAN and the RADKLIM reanalysis live on the DWD Climate Data Center.

---

## Official documentation
- DWD open radar tree: https://opendata.dwd.de/weather/radar/
- RADOLAN / RADVOR product pages (DWD): https://www.dwd.de/DE/leistungen/radolan/radolan.html
- DWD Open Data terms (GeoNutzV): https://opendata.dwd.de/README.txt
- `wradlib` (reading RADOLAN & ODIM): https://docs.wradlib.org
- DWD Climate Data Center — historical RADOLAN / RADKLIM: https://opendata.dwd.de/climate_environment/CDC/grids_germany/
