# KAUR Radar (Estonia — National Network: Volumes, Products & Composite)

## What this is
The Estonian Environment Agency (Keskkonnaagentuur, **KAUR**) operates Estonia's
national weather-radar network — two C-band dual-polarisation Doppler radars at
**Harku** (near Tallinn) and **Sürgavere** (central Estonia) — and publishes
their output openly through the KAIA open-data file store, in **ODIM HDF5**.

This entry covers the full observational output: the **single-site 3D
polarimetric volume data** (`VOL`) from each radar, three **single-radar
Cartesian products** — reflectivity CAPPI (`CAP`), echo-top height (`TOP`), and
radial-velocity PPI (`PPI`) — and a **national two-radar precipitation-rate
composite** (`comp`). All are freely downloadable, no account, CC-BY 4.0.

Estonia's radars are part of the EUMETNET OPERA cooperation; KAUR publishes in
the EU-directive ODIM HDF5 format specifically so the data is consistent with
other European countries' radar feeds. This national feed is the
full-resolution counterpart to the [OPERA pan-European composite](../eu/opera-composite.md).

---

## Who operates it
- **Operator:** Estonian Environment Agency (Keskkonnaagentuur, KAUR), through its national weather service, the Estonian Weather Service (Riigi Ilmateenistus, ilmateenistus.ee).
- **Country / region:** Estonia.
- **Data distributor:** KAIA — Keskkonnaportaal open-data file store (`avaandmed.keskkonnaportaal.ee`).
- **Contributing programme:** EUMETNET OPERA member network.

---

## Network composition
Two **C-band (λ = 5.33 cm) dual-polarisation Doppler** radars (manufacturer
Vaisala Oy), operating 24/7, timestamps in UTC. Radar separation 115 km; each
has a 250 km measurement radius. The two radars are broadly equivalent but not
identical — **Harku was modernised to a solid-state transmitter in 2024**, while
Sürgavere still runs its magnetron system:

| Site | ODIM NOD | WMO / RAD | Position | Antenna height | Transmitter / system | IRIS |
|---|---|---|---|---|---|---|
| **Harku** (Tallinn) | `eehar` | 26038 / EE40 | 59.3977°N, 24.6021°E | 70 m (tower 38 m) | **Solid-state**, Vaisala **WRS300** | 10.4 |
| **Sürgavere** | `eesur` (files use `eesyr`) | 26232 / EE41 | 58.4823°N, 25.5187°E | 157 m (tower 29 m) | **Magnetron**, Vaisala **WRS200** | 10.3 |

Both radars deliver the **full dual-pol moment set**: corrected reflectivity
**DBZH**, uncorrected **TH** (and **TV**/**DBZV** on some sweeps), radial
velocity **VRADH**, spectrum width **WRADH**, differential reflectivity **ZDR**,
specific differential phase **KDP**, differential phase **PHIDP**, correlation
coefficient **RHOHV**, and a **hydrometeor classification HCLASS** — plus quality
moments (SNR, SQIH, PMI, LOG, CSP). Distributing HCLASS and KDP directly is
notable (many networks publish only PHIDP). The volume scan runs **15 sweeps
from 0.5° to 45°** (0.5, 1.3, 2.0, 3.0, 4.5, 6.5, 9.0 + a Doppler task at 0.5,
2.0, 5.0, 10, 15, 25, 45°), **360 rays** (1° azimuth), **300 m** range gates
(Harku) / 200–300 m (Sürgavere), to 250 km. Melting layer ~3.5 km.

---

## Products
All files are ODIM HDF5 (`.h5`); the raw physical value is recovered as
`value = offset + gain × DN`, with `nodata`/`undetect` flags per ODIM. Published
files (raw and products) are available on a **5-minute** step.

**Single-site volume data** (`HAR.<YYYYMMDDhhmm>.VOL.h5`, `SUR.…VOL.h5`) — 3D
polar volumes, ODIM `PVOL` v2.3, the full moment set above across 15 sweeps.
Per-moment `gain`/`offset` live in each `datasetN/dataM/what` (e.g. TH:
gain 0.01, offset −327.68).

**Single-radar Cartesian products** (per radar; azimuthal-equidistant grid
centred on the radar, 720 × 720, ODIM v2.4):
- **Reflectivity — `eehar/eesyr.<…>.CAP.h5`** — ODIM `CVOL`, a **10-level
  pseudo-CAPPI** stack (`PCAPPI`, heights 500 m → 10 km), quantity **DBZH**,
  ~694 m cells (~500 km domain). `Z[dBZ] = 0.5·DN − 32`, nodata 255.
- **Echo-top — `eehar/eesyr.<…>.TOP.h5`** — ODIM `IMAGE`, product `ETOP` for a
  **20 dBZ** threshold, quantity **HGHT**, same ~694 m grid.
  `height[km] = 0.1·DN − 0.1`, nodata 255.
- **Radial wind velocity — `eehar/eesyr.<…>.PPI.h5`** — ODIM `IMAGE`, product
  `PPI` at **0.5° elevation**, quantity **VRADH**, ~344 m cells (~248 km
  domain). `v[m/s] = 0.37772·DN − 48.348` (Nyquist ≈ ±48 m/s), nodata 255.

**National composite** (`comp.<…>.CAP.h5`) — ODIM `COMP` v2.4, task `CCOMPEST`,
a **two-radar surface precipitation-rate mosaic** derived from the 500 m
pseudo-CAPPI, quantity **RATE** (`rate[mm/h] = DN`, float32, nodata 65535).
Mercator projection (`+proj=merc +a=6378137 +rf=298.257224`), 720 × 720 at
~806 × 776 m (~580 × 559 km), covering 19.99–30.12°E, 56.29–61.51°N. Radar-only
(`nodes = eesyr,eehar`) — no rain-gauge merge.

---

## Data availability
- **Is the data free?** Yes.
- **Is the data downloadable?** Yes.
- **Access tier:** Open ("Avalik" / Public) — no account.
- **Data formats:** ODIM HDF5 (`.h5`) — `PVOL` v2.3 for volumes; `CVOL`/`IMAGE`/`COMP` v2.4 for products.
- **Update cadence:** Every 5 minutes, 24/7, near-real-time. Timestamps are UTC (volumes to the minute; products to the second, with a trailing 4-digit sequence).
- **File naming:** `RRR(RR).YYYYMMDDhhmm(ss).TYPE.XXXX.h5` — prefix `HAR`/`eehar` (Harku), `SUR`/`eesyr` (Sürgavere), `comp` (composite); `TYPE` ∈ `VOL`/`CAP`/`TOP`/`PPI` (`CAP` = reflectivity for a radar prefix, precipitation composite for `comp`); `XXXX` is a sequence number present on products only; volumes omit the seconds and the sequence number. Typical sizes: VOL 20–33 MB, CAP ~0.3–0.9 MB, comp ~0.3 MB, PPI 45–130 KB, TOP ~22 KB.
- **Primary access (KAIA):**
  - Browsable file list: KAIA "Meteorological observations data → radar" views at https://avaandmed.keskkonnaportaal.ee/dhs/Active (per-product and an all-radar view).
  - Direct file-download pattern: `https://avaandmed.keskkonnaportaal.ee/dhs/Active/_layouts/RM/Content.aspx?ListId=228b4970-73de-44a4-83e1-9513be360001&ID=<documentId>&fileId=1` (the same KAIA records list `228b4970-…` also serves the SWAN-EST and NEMO-EST datasets, separated by view).
  - KAIA machine API (SharePoint RmApi): metadata query `POST …/_vti_bin/RmApi.svc/active/items/query`; zipped files `POST …/active/items/zipped-files`; body filters on `$contentType` (the radar `$contentType` value is not exposed on the public view page — TBD).
- **New-data notifications:** None documented (poll KAIA).
- **Archive depth:** The KAIA open feed is a **rolling near-real-time window** (depth not explicitly documented; other KAIA weather feeds retain ~1 month — TBD). KAUR states a **full raw (IRIS RAW) archive exists from 2010** (first radar data 2005), but the open ODIM HDF5 publication on KAIA is the recent rolling window; deep-archive access terms are not stated (TBD).
- **Licence:** **CC-BY 4.0** (https://creativecommons.org/licenses/by/4.0/), attribution to Keskkonnaagentuur / Estonian Environment Agency. This is the portal-wide policy for weather-domain open data (the ODIM files carry no per-file `license` attribute, so the licence reflects the data policy).

---

## Scope note
- **Clean observational fit.** Single-site polarimetric volumes, single-radar reflectivity/echo-top/velocity products, and a radar-only precipitation-rate composite — all ODIM HDF5, no rendered imagery and no nowcast/forecast products in this feed. (The 1.5-hour extrapolation nowcast shown on ilmateenistus.ee is a rendered web product, not part of this data feed; if KAUR ever publishes it as data it would belong in `nowcasting_models/`.)
- **Precipitation composite is radar-only.** Quantity `RATE` (mm/h) from the 500 m pseudo-CAPPI via Z–R; `nodes = eesyr,eehar` with no gauge merge — a pure radar QPE, comparable to SHMÚ's PAC01 rather than ČHMÚ's gauge-merged MERGE.

---

## Notes
- **Observation, not forecast.** All products here are observational/analysis.
- **Relationship to OPERA.** Estonia is a EUMETNET OPERA member; the Harku and Sürgavere radars contribute to the OPERA pan-European composite, and this KAUR feed is the national-resolution counterpart (same framing as the Czechia and Slovakia entries).
- **Relationship to NWP.** Estonia is a MetCoOp member; national radar data of this kind feeds the HARMONIE-AROME/[MEPS](../../../models/nwp_models/regional/norway/meps.md) assimilation ecosystem, and OPERA reflectivity is assimilated by several European limited-area models.
- **`eesur` vs `eesyr` naming.** Sürgavere's OPERA/WMO NOD is **`eesur`** (as recorded inside the raw `VOL` metadata), but the distributed Cartesian product files (and their ODIM `source` tag) use **`eesyr`**. Same radar (WMO 26232, RAD EE41) — just an inconsistency in the product-generation naming. Harku is consistently `eehar`.
- **ODIM HDF5.** Readable with `wradlib`, `xradar`, `h5py`, or Py-ART. Volumes are polar (azimuth × range in `/dataset*/where`); products are georeferenced Cartesian grids (corner coords + `projdef`/`xscale`/`yscale` in `/where`). KAUR's own processing example uses `wradlib.io.read_opera_hdf5` + xarray/cartopy.
- **Data feed vs viewer.** KAUR's public radar map at ilmateenistus.ee (last 3 h + 1.5 h extrapolation) is rendered; this KAIA HDF5 tree is the data.
- **`CAP` is overloaded.** For a radar prefix, `CAP` is a reflectivity pseudo-CAPPI **volume** (10 levels); for `comp`, `CAP` is the single-level precipitation-rate composite. Filename prefix disambiguates.

---

## Recent version history
- **2024 — Harku radar modernised.** Harku was upgraded to a solid-state Vaisala **WRS300** transmitter (from a magnetron WRS200), improving sensitivity and accuracy. The current volume scan runs 15 sweeps; KAUR's 2023 documentation still shows Harku's pre-upgrade configuration (magnetron WRS200, IRIS 9.0, 11 sweeps), and the composite grid has since changed from 1500 × 1500 @ ~359 m to 720 × 720 @ ~806 m — verify specs against live files, not the description page.

---

## Official documentation
- Dataset page (Meteoroloogiliste radarite andmestik): https://keskkonnaportaal.ee/et/avaandmed/meteoroloogiliste-radarite-andmestik
- Data description & processing guide (Radariandmete kirjeldus): https://keskkonnaportaal.ee/et/avaandmed/radariandmestik/radariandmete-kirjeldus
- KAIA open-data file store: https://avaandmed.keskkonnaportaal.ee/dhs/Active
- Access rights & licensing description: https://keskkonnaportaal.ee/et/avaandmed#Juurdepsuigusetekirjeldusandmestikejuures
- EUMETNET OPERA ODIM_H5 v2.3 spec (cited by KAUR): https://www.eumetnet.eu/wp-content/uploads/2019/01/ODIM_H5_v23.pdf
- FMI HDF5 radar example (referenced by KAUR): https://github.com/fmidev/opendata-resources/blob/master/examples/python/radar-rhi-from-hdf5.ipynb
