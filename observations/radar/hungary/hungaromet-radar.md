# HungaroMet Radar (Hungary — National Composite: CMax, PseudoCAPPI & 3D)

## What this is
HungaroMet (the Hungarian Meteorological Service, formerly OMSZ) publishes the
**national composite** products of its weather-radar network openly through the
HungaroMet Meteorological Data Store (`odp.met.hu`). This entry covers the three
gridded **NetCDF composites** — column-maximum reflectivity (CMax), a hybrid
2 km-equivalent PseudoCAPPI, and a 3D reflectivity volume — all freely
downloadable with no account.

Unlike the neighbouring ČHMÚ / DWD / FMI feeds, HungaroMet's open channel
distributes **only national composites** — there is no open single-site /
polarimetric-volume feed here. The same tree also carries rendered PNG imagery
(a viewer equivalent, not the data). Hungary is a EUMETNET OPERA member, so
these composites are the national counterpart to the OPERA pan-European product;
this feed is also the radar input behind HungaroMet's MEANDER nowcasting system.

---

## Who operates it
- **Operator:** HungaroMet Hungarian Meteorological Service (formerly OMSZ — Országos Meteorológiai Szolgálat).
- **Country / region:** Hungary.
- **Data distributor:** HungaroMet Meteorological Data Store / Open Data Portal (`odp.met.hu`).
- **Contact:** `odp@met.hu` (data portal); `radar@met.hu` (radar programme).

---

## Network composition
HungaroMet operates a national network of **five C-band (5.5 cm) dual-polarisation
Doppler radars** (EEC DWSR): **Budapest**, **Napkor**, **Pogányvár**, **Szentes**,
and **Hármas-hegy** (Mecsek, commissioned autumn 2021 as the 5th site). Each
radar measures the full polarimetric moment set — reflectivity **Z**, radial
velocity **Vr**, differential reflectivity **ZDR**, specific differential phase
**KDP**, differential phase **ΦDP**, and correlation coefficient **ρhv**.

The precipitation scan runs **10 elevations** (0.0°, 0.5°, 1.1°, 1.9°, 3.0°,
4.7°, 7.0°, 10.0°, 14.2°, 20.0°) to a **240 km** range on a **5-minute** cycle
(PRF 600 Hz, 0.8 µs pulse); a separate Doppler wind scan runs every 15 minutes
to 120 km. The national composites blend all five radars on a 961 × 813 grid.
(Only the composites are published openly — the per-radar volumes are not.)

---

## Products
All three are national composites, ODIM-agnostic **NetCDF** on a regular
lat/lon grid, produced every 5 minutes:

- **`refl2D`** — **CMax** (column-maximum reflectivity): the per-column maximum of the individual-radar column maxima. 2D, 1×1 km.
- **`refl2D_pscappi`** — **PseudoCAPPI**: a hybrid product taking reflectivity from the 1 km CAPPI level where the beam geometry allows, projecting the highest elevation (20°) near each radar and the lowest (0°) at long range, with terrain-blockage handling; the better near-surface precipitation estimate. 2D, 1×1 km.
- **`refl3D`** — **3D reflectivity**: interpolation of the individual-radar reflectivities onto a 3D grid. **16 vertical levels**, 1×1×1 km.

The reflectivity values are byte-encoded — decode with **`dBZ = raw/2 − 32`**
(carried in each variable's `units` attribute, e.g. `refl2D` = "CMax
reflectivity", `units = dBZ=raw/2-32`).

---

## Data availability
- **Is the data free?** Yes — no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** NetCDF, each file zip-compressed (`.nc.zip`). Geolocation is implicit — reconstruct the grid from the `La1`/`Lo1` corner and `Dx`/`Dy` spacing; there is no CF `grid_mapping` variable (see *Notes*).
- **Update cadence:** Every 5 minutes. Near-real-time (a few minutes' latency for measurement + compositing).
- **Primary access:** plain HTTPS directory tree (curl/wget) — https://odp.met.hu/weather/radar/composite/nc/
  - `nc/refl2D/`, `nc/refl2D_pscappi/`, `nc/refl3D/`
  - Filename: `radar_composite-<type>-<YYYYMMDD>_<HHMM>.nc.zip`, e.g. `radar_composite-refl2D-20260712_1700.nc.zip`. The timestamp is UTC and marks the **start** of the measurement.
- **New-data notifications:** None (no S3/SNS/API); poll the directory tree. (An OGC API is anticipated via the EU RODEO project — see *Scope note*.)
- **Other mirrors:** None known (single HTTPS origin).
- **Archive depth:** Short near-real-time rolling window — observed ≈3 days at time of verification. Not a deep archive.
- **Licence:** **CC BY-SA 4.0** (ShareAlike), attribution to HungaroMet. HungaroMet applies CC BY-SA to all its open data; general terms at https://odp.met.hu/ODP_General_Term_of_Use.pdf.

---

## Grid definition
- **Projection:** regular latitude/longitude (spherical / WGS84).
- **Dimensions:** 961 (lon) × 813 (lat); 3D adds 16 vertical levels.
- **Corner (`La1`,`Lo1`):** NW corner at **50.5°N, 13.5°E** — rows run **southward** (Δlat ≈ 0.008°) to 44.004°N; columns run eastward (Δlon ≈ 0.0125°) to 25.5°E.
- **Coverage:** 13.5°E – 25.5°E, 44.004°N – 50.5°N.
- **Resolution:** ~1×1 km (`refl2D`, `refl2D_pscappi`); ~1×1×1 km (`refl3D`).

---

## Scope note
- **Composites only.** HungaroMet publishes national composites here, not single-site polarimetric volumes; the volume/moment data is measured but not in the open feed.
- **Transitional toward API access (RODEO).** The data is already fully open (CC BY-SA 4.0); HungaroMet participates in the EU **RODEO** project (meteorological data as an EU High-Value Dataset), under which an OGC/download API is expected to supplement the current plain-HTTPS tree. No barrier today — noted only as a forthcoming access improvement.

---

## Notes
- **Observation, not forecast.** CMax, PseudoCAPPI, and 3D reflectivity composites — clean observational fit. The radar-based precipitation-accumulation product described in HungaroMet's radar documentation is a separate (gauge-corrected) product and was **not** found in this open NetCDF composite tree.
- **Relationship to MEANDER / OPERA.** This composite feed is the radar input to HungaroMet's **MEANDER** nowcasting system (`models/nowcasting_models/regional/hungary/meander.md`), and Hungary's contribution to the EUMETNET **OPERA** pan-European composite.
- **Geolocation gotcha.** Files carry only `La1`/`Lo1`/`Dx`/`Dy` scalars plus the data variable — no CF coordinate variables or `grid_mapping`. Build the lat/lon axes from the NW corner + spacing, and note the **north-to-south** row order (first grid point is the NW corner) before plotting, or the image will be flipped.
- **Reflectivity encoding.** Stored as scaled integers; apply `dBZ = raw/2 − 32` (per the variable `units` attribute).
- **Double compression.** Files are NetCDF already, then zipped (`.nc.zip`) — unzip before opening.
- **Data feed vs viewer.** The `png/` subtree (`refl2D`, `refl2D_pscappi`) and HungaroMet's public radar map at met.hu are rendered imagery; the `nc/` tree is the data feed.

---

## Recent version history
- **2025-02-21 — documentation fix:** western coverage bound corrected from 13.3°E to 13.5°E in the dataset description (grid unchanged; the NetCDF `Lo1` is 13.5°E).
- **2021 (autumn) — network expanded to five radars** with the commissioning of Hármas-hegy (Mecsek).

---

## Official documentation
- HungaroMet radar composite tree: https://odp.met.hu/weather/radar/composite/
- Dataset description — NetCDF composites (EN): https://odp.met.hu/weather/radar/composite/Description-radar_nc-en.pdf
- Dataset description — PNG composites (EN): https://odp.met.hu/weather/radar/composite/Description-radar_pic-en.pdf
- Radar network description (OMSZ/HungaroMet, HU): https://www.met.hu/downloads.php?fn=/ismertetok/radar_ismerteto.pdf
- HungaroMet Open Data general terms (CC BY-SA 4.0): https://odp.met.hu/ODP_General_Term_of_Use.pdf
