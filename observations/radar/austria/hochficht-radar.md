# Hochficht Radar (Austria — GeoSphere Austria × Meteopress Single-Site C-Band Volumes)

## What this is
The **Hochficht** weather radar is a C-band dual-polarisation Doppler radar on a
tower in the Hochficht ski area in the Mühlviertel (Upper Austria), close to the
Austria–Czechia–Germany tri-border. It is operated as a **cooperation project
between GeoSphere Austria and the Czech radar manufacturer Meteopress**, and
GeoSphere publishes its **full 3D polarimetric volume scans** openly through the
GeoSphere Austria Data Hub, in ODIM HDF5, every 5 minutes, with no account.

This entry is unusual for this section in two ways, both deliberate. First, it
documents a **single radar rather than a national network or composite** — no
composite, mosaic, QPE, or derived product is published from it, and it is the
only radar dataset on the GeoSphere Data Hub. Second, it is **not Austria's
national radar network**: that network is operated by Austro Control (see *Scope
note*) and is not published as open data. Hochficht is a separate installation
that happens to have an open feed, so it is the only route to Austrian radar
data at native polar resolution.

The distinguishing technical feature is the transmitter. Meteopress radars use a
**solid-state power amplifier (SSPA)** rather than a magnetron, which shows up
directly in the distributed files as very long transmitted pulses recovered by
pulse compression — see *Products*.

---

## Who operates it
- **Operator:** GeoSphere Austria (Bundesanstalt für Geologie, Geophysik, Klimatologie und Meteorologie) jointly with **Meteopress** (Czech radar manufacturer and meteorological company). The radar was built with GeoSphere's predecessor ZAMG around 2021.
- **Country / region:** Austria (Upper Austria / Mühlviertel), with coverage extending into Czechia and Germany.
- **Data distributor:** GeoSphere Austria Data Hub (`data.hub.geosphere.at` for metadata; `public.hub.geosphere.at` for the files).
- **Note on the national network:** Austria's operational national radar network is **not** operated by GeoSphere — see *Scope note*.

---

## Network composition
A **single C-band dual-polarisation Doppler radar**, fully solid-state. All
values below are read from the distributed ODIM files unless marked otherwise.

| Property | Value | Source |
|---|---|---|
| Position | **48.73688 °N, 13.92089 °E** | `/where` (lat, lon) |
| Antenna height | **1333 m** AMSL | `/where/height` |
| Wavelength | **5.3296 cm** (≈ 5.625 GHz, C-band) | `/how/wavelength` |
| Beamwidth | **1.04°** horizontal and vertical | `/how/beamwH`, `beamwV` |
| ODIM source string | `RAD:hochficht` | `/what/source` |
| Transmitter | Solid-state power amplifier (SSPA), GaN | GeoSphere / Meteopress docs |
| Antenna | 3 m reflector, all electronics in the pedestal | Meteopress product docs |

The exposed summit siting at 1333 m gives long unobstructed sight lines over
Upper Austria and the adjacent regions of South Bohemia (CZ) and Bavaria (DE).
It also forces an unusual scan strategy: the volume includes a **negative
elevation of −0.2°**, needed to see precipitation in the valleys *below* the
antenna. On a 4/3-Earth beam model the −0.2° beam centre stays near 1300 m AMSL
out to ~50 km and only passes 3 km AMSL beyond ~200 km, whereas the 1.4° sweep
is already at ~2.7 km AMSL at 50 km.

**No NOD or WMO code.** The ODIM `source` attribute carries only
`RAD:hochficht` — there is no `NOD:`, `WMO:`, `RAD:` (national) or `PLC:`
identifier of the kind other European feeds supply. Software that keys radar
identity off `NOD` will not resolve this site; match on the literal string or on
the coordinates.

---

## Products
One product only: **3D polarimetric polar volumes** (ODIM `PVOL`, `H5rad 2.3`),
one file per 5-minute cycle. There are no Cartesian products, no composite, no
CAPPI/echo-top/QPE derivatives, and no nowcast fields.

### Scan strategy (verified identical across the archive)
**12 sweeps, scanned top-down** from 18.0° to −0.2°. Range increases as
elevation decreases, because the pulse repetition frequency is lowered on the
low tilts to extend unambiguous range. All sweeps: **360 rays** (1° azimuth),
**250 m** range gates, `rstart` 0.25 km.

| Sweep | Elevation | `nbins` | Max range | High/low PRF (Hz) | Nyquist (m s⁻¹) | Pulse (µs) | rpm |
|---|---|---|---|---|---|---|---|
| `dataset1` | 18.0° | 570 | 142.75 km | 1500 / 1000 | 39.97 | 24.5 | 4.33 |
| `dataset2` | 12.0° | 570 | 142.75 km | 1500 / 1000 | 39.97 | 24.5 | 4.33 |
| `dataset3` | 7.8° | 727 | 182.00 km | 1000 / 750 | 39.97 | 97.0 | 3.50 |
| `dataset4` | 6.0° | 727 | 182.00 km | 1000 / 750 | 39.97 | 97.0 | 3.50 |
| `dataset5` | 4.7° | 760 | 190.25 km | 960 / 720 | 38.37 | 97.0 | 3.33 |
| `dataset6` | 3.5° | 796 | 199.25 km | 920 / 690 | 36.77 | 97.0 | 3.17 |
| `dataset7` | 2.4° | 835 | 209.00 km | 880 / 660 | 35.18 | 97.0 | 3.00 |
| `dataset8` | 1.4° | 926 | 231.75 km | 800 / 600 | 31.98 | 97.0 | 2.67 |
| `dataset9` | 0.8° | 963 | 241.00 km | 772 / 579 | 30.86 | 97.0 | 2.33 |
| `dataset10` | 0.4° | 1219 | 305.00 km | 580 / 464 | 30.91 | 97.0 | 2.00 |
| `dataset11` | 0.1° | 1265 | 316.50 km | 560 / 448 | 29.85 | 97.0 | 2.00 |
| `dataset12` | −0.2° | 1355 | 339.00 km | 525 / 420 | 27.98 | 97.0 | 1.83 |

Dual-PRF is used throughout (3:2 at the top, 5:4 at the bottom); the **lower**
PRF sets the maximum range, the pair sets the extended Nyquist velocity.

**Pulse compression.** The `how/pulsewidth` values of **24.5 µs and 97 µs** are
the *transmitted* pulse lengths, not the effective range resolution — a 97 µs
pulse is ~14.5 km long in space, versus the 250 m gates actually delivered, so
a compression ratio near 60. This is the expected signature of a solid-state
transmitter, which compensates for low peak power with long coded pulses.
*(Inference from the Meteopress design documentation, which states pulse
compression is used; not independently confirmed as a published spec.)*

A full volume takes a very consistent **4 min 37 s** (e.g. `03:25:02 → 03:29:40`
for the 0325 file), leaving ~23 s of margin in the 5-minute cycle.

### Moments (8 per sweep, all 12 sweeps)
`TH`, `KDP`, `PHIDP`, `DBZH`, `VRADH`, `ZDR`, `WRADH`, `RHOHV` — in that HDF5
order (`data1`…`data8`). Note that **`DBZH` is not `data1`**; index by the
`what/quantity` attribute, not by position.

- **`TH`** — uncorrected (total power) horizontal reflectivity, dBZ
- **`DBZH`** — corrected/QC'd horizontal reflectivity, dBZ
- **`VRADH`** — radial velocity, m s⁻¹ (dual-PRF dealiased)
- **`WRADH`** — spectrum width, m s⁻¹
- **`ZDR`** — differential reflectivity, dB
- **`PHIDP`** — differential phase, degrees
- **`KDP`** — specific differential phase, ° km⁻¹ — **published directly**, which is notable; many networks compute KDP but distribute only PHIDP
- **`RHOHV`** — co-polar correlation coefficient

`TV` / `DBZV` / `SQI` / `SNR` / `HCLASS` are **not** distributed.

### Encoding — read this before decoding
All moments are **`uint8`**, gzip-compressed and chunked, recovered as
`value = offset + gain × DN`. Scaling is stable across the archive. For the
lowest sweep:

| Quantity | gain | offset | Approx. physical range |
|---|---|---|---|
| `TH` | 0.5019763 | −32.50198 | −32.0 … +95.5 dBZ |
| `DBZH` | 0.5000076 | −32.50001 | −32.0 … +95.0 dBZ |
| `VRADH` | 0.2359519 | −30.08387 | −29.8 … +30.1 m s⁻¹ |
| `WRADH` | 0.1179759 | −0.1179759 | 0.1 … 29.9 m s⁻¹ |
| `ZDR` | 0.1259862 | −16.12599 | −16.0 … +16.0 dB |
| `PHIDP` | 1.4229249 | −1.4229249 | 1.4 … 361.4° |
| `KDP` | 0.0393707 | −2.0393707 | −2.0 … +8.0 ° km⁻¹ |
| `RHOHV` | 0.0043478 | −0.0043478 | 0.004 … 1.109 |

> ⚠ **Non-standard flag values: `nodata = 0`, `undetect = 1`.** Valid data
> occupies **DN 2–255**. Most European ODIM feeds use `nodata = 255` and
> `undetect = 0`, so code that hard-codes the common convention will silently
> mis-decode this feed. Worse, applying the scaling to an unmasked DN 0 yields
> the offset value (≈ −32.5 dBZ for `DBZH`) — a *plausible-looking* weak echo
> rather than an obvious sentinel. Always mask on the per-moment
> `what/nodata` and `what/undetect` attributes.

### Other per-sweep metadata worth knowing
- **`how/astart = -0.5`** with per-ray `startazA` / `stopazA` arrays: ray *i*
  spans azimuth (*i* − 0.5°, *i* + 0.5°), i.e. **ray centres sit on integer
  degrees**, ray 0 centred on true north.
- **`how/startazT` / `stopazT`** give per-ray Unix timestamps — usable for
  precise ray-level georeferencing of fast-moving echoes.
- **`how/a1gate`** (index of the first ray radiated) varies per sweep, 16–311.
- `how/scan_index`, `scan_count`, and a `tag` string (`el0_1`, `el-0_2`, …)
  identify sweeps within the scan plan.

### QC behaviour (worth calibrating expectations against)
On a quiet night sample (2026-07-29 03:25 UTC, 0.1° sweep), **35.0 %** of gates
carried a valid `TH` signal — with values to 72.9 dBZ, i.e. ground clutter —
while only **0.04 %** survived into `DBZH`. The clutter/QC filtering is
aggressive, as it has to be for a mountain-top radar with a sub-horizontal
sweep over complex Alpine and Bohemian Forest terrain. During precipitation
(2026-07-27 00:00 UTC) `DBZH` valid coverage rose to **3.1 %** of all gates with
a maximum of 54.5 dBZ. Use `TH` if you want to do your own clutter handling;
use `DBZH` if you want the vendor's.

---

## Data availability
- **Is the data free?** Yes — no account, no registration, no API key.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** ODIM HDF5 (`.hdf`), `PVOL`, `H5rad 2.3`. Internal datasets gzip-compressed and chunked.
- **Update cadence:** Every 5 minutes, 288 files/day, timestamps UTC.
- **Latency:** **~6 min 15 s** from nominal cycle time to object availability — measured across the full 616-object archive: min 370 s, median 374 s, mean 375 s, max 401 s. Since the volume scan itself ends 4 min 37 s after the nominal time, publication follows scan completion by roughly 1.6 minutes. Unusually tight and predictable for a radar feed.
- **File naming:** `WXRHOF_<YYYYMMDDHHMM>.hdf` (`WXR` = weather radar, `HOF` = Hochficht). The timestamp is the **nominal cycle start**, not the scan end.
- **File size:** 1.76–4.42 MB, mean **2.19 MB** (larger in precipitation). ≈ **630 MB/day**; the full rolling archive is ~1.35 GB.
- **Primary access — direct file URL:**
```
  https://public.hub.geosphere.at/datahub/resources/radar_volumen_hochficht-v1-5min/filelisting/WXRHOF_<YYYYMMDDHHMM>.hdf
```
  Supports HTTP range requests.
- **Programmatic listing (undocumented but public).** The browse page is a Vue
  front-end over an S3-compatible object store; plain `GET` on the directory
  path returns `AccessDenied`, but **`ListObjectsV2` on the bucket root works
  anonymously**:
```bash
  curl -s "https://public.hub.geosphere.at/datahub/?list-type=2&max-keys=1000\
&delimiter=/&prefix=resources/radar_volumen_hochficht-v1-5min/filelisting/"
```
  Returns standard `ListBucketResult` XML (`Key`, `LastModified`, `ETag`,
  `Size`); paginate with `continuation-token` from `NextContinuationToken`. Bucket
  is `datahub`, key root is `resources/`.
- **Human browse UI:** https://public.hub.geosphere.at/public/datahub.html?id=radar_volumen_hochficht-v1-5min/filelisting
- **Dataset API:** **Not available for this dataset.** Unlike GeoSphere's NWP, ensemble, and WRF-Chem datasets, the CKAN record lists exactly one resource (bulk HTTP file listing); `dataset.api.hub.geosphere.at` does not serve it. Bulk HTTP is the only channel.
- **New-data notifications:** None (no SNS/webhook). Poll on the ~6¼-minute latency.
- **Other mirrors:** None known.
- **Archive depth:** **Rolling ~3 UTC calendar days**, deleted at whole-day granularity — so the effective depth oscillates between ~2 and ~3 days. Measured 2026-07-29 03:36 UTC: 616 objects spanning 2026-07-27 00:00 → 2026-07-29 03:30 (51.5 h). **There is no long-term archive**; anything you want to keep must be harvested continuously.
- **Completeness:** 616 of 619 expected slots present (**99.5 %**) over the measured window. All 3 gaps were single missing cycles. Two of the three were the **23:55 file on consecutive days** (2026-07-27 and 2026-07-28), suggesting a systematic UTC-midnight rollover artifact rather than random dropouts — worth handling explicitly in a harvester.
- **Licence:** **Creative Commons Attribution 4.0 International (CC BY 4.0)** — `license_id: CC-BY-4.0`, https://creativecommons.org/licenses/by/4.0/legalcode. Attribution to GeoSphere Austria required. *(Metadata inconsistency: the CKAN record also carries `"isopen": false` alongside the CC-BY-4.0 licence ID and `"restricted": "False"`. CC BY 4.0 is unambiguously an open licence; the flag appears to be a portal artifact, but it is noted here rather than silently ignored.)*
- **DOI:** https://doi.org/10.60669/3p7s-wg05
- **Version:** 1. Created 2025-01-10, published 2025-02-10, record last modified 2026-05-21.

---

## Scope note
Three tensions are flagged rather than resolved silently.

**1. Quasi-operational, and explicitly labelled as in testing.** GeoSphere
describes the radar as running *"im quasi-operationellen Betrieb"*
(quasi-operational), and the dataset page carries a standing banner:
*"Achtung! Dieser Datensatz befindet sich derzeit noch im Testbetrieb."* /
*"Attention! This dataset is still in the testing phase."* This catalog normally
reserves entries for permanent operational channels. It is included because the
feed has been published under a DOI and a CC BY licence since February 2025, has
been running continuously with 99.5 % cycle completeness and sub-7-minute
latency, and is the **only** open Austrian radar feed at native resolution.
**Revisit if** the testing-phase banner is replaced by a discontinuation notice,
or if the cadence/completeness degrades materially.

**2. Not Austria's national radar network.** The Austrian operational network is
run by **Austro Control** (the civil aviation authority): five C-band
dual-polarisation sites — **Rauchenwarth, Feldkirchen, Zirbitzkogel,
Patscherkofel, Valluga** — polarimetric since 2011, 5-minute volume cycle,
providing full national coverage. **None of that data is published as open
data**, and there is no open Austrian national composite. Austro Control
contributes to EUMETNET OPERA, so Austrian national radar reaches the public
only indirectly, via the [OPERA pan-European composite](../eu/opera-composite.md),
at OPERA's reduced resolution. Hochficht is a separate installation and should
not be read as a proxy for national coverage. **Revisit if** Austro Control
opens a national feed.

**3. Single radar, not a network or composite.** This section's convention is
one entry per network or composite product rather than per individual radar.
Hochficht is a documented exception: it is the entire published dataset, there
is no network to aggregate it into, and no derived or composite product is
distributed from it.

---

## Notes
- **Observation, not forecast.** Raw polar volumes only — no extrapolation, no
  blending, no model input. GeoSphere's radar-derived nowcast products are a
  separate system; see [INCA](../../../models/nowcasting_models/regional/austria/inca.md),
  which ingests the **Austro Control** composite (max-CAPPI) rather than this
  radar.
- **Relationship to NWP.** No published statement that Hochficht volumes are
  assimilated by [AROME Austria](../../../models/nwp_models/regional/austria/arome-austria.md)
  or [C-LAEF](../../../models/ensemble_models/regional/austria/c-laef.md). **TBD** —
  do not assume it from the cooperation.
- **Coverage in practice.** GeoSphere gives the coverage bounding box as
  **46.94–50.534 °N, 11.196–16.646 °E**, which is exactly a 200 km radius about
  the site: large parts of Upper Austria, plus South Bohemia (CZ) and eastern
  Bavaria (DE).
- **⚠ Documented range (200 km) vs. distributed range (up to 339 km).**
  GeoSphere documents a *"maximale Reichweite von 200 km"*, and the published
  bounding box is computed from that. The **files go substantially further**:
  only the 3.5° sweep is close to 200 km; the four lowest sweeps reach
  209–339 km. Treat 200 km as the **quality-assured** range and anything beyond
  it as unvalidated overshoot — but be aware the arrays are larger than the
  bounding box implies, and clip deliberately rather than trusting the metadata
  extent.
- **Data feed vs viewer.** A **webcam** at the site
  (https://www.meteopress.sk/kamery/cam001090/) is linked from the dataset page.
  It is a camera, not the data feed. Meteopress also runs public radar-imagery
  viewers; those are rendered products and out of scope.
- **Single-site only.** No composite channel exists for this radar. If you need
  a Cartesian grid, you must interpolate the polar volumes yourself
  (`wradlib`, `Py-ART`, `xradar`).
- **Reading the files.** Standard ODIM readers work
  (`xradar.io.open_odim_datatree`, `pyart.aux_io.read_odim_h5`,
  `wradlib.io.read_opera_hdf5`), but **verify the `nodata`/`undetect` handling**
  given the inverted flag convention above.
- **Sibling Meteopress installations.** Meteopress operates further radars in
  Czechia (Znojmo, Plzeň, Jihlava, Brandýs nad Labem) and at least one other in
  Austria (**Reichersberg**). **None of these are published on the GeoSphere Data
  Hub**, and no open feed for them has been identified. **TBD** — worth
  re-probing if the hub adds radar datasets.
- **Only radar dataset on the hub.** A CKAN `package_search` for *radar*,
  *volumen*, and *hochficht* returns exactly one dataset (verified 2026-07-29).
  There is no composite, QPE, or second-site dataset to look for.

---

## Official documentation
- Dataset landing page: https://data.hub.geosphere.at/en/dataset/radar_volumen_hochficht-v1-5min
- Bulk file listing (browse UI): https://public.hub.geosphere.at/public/datahub.html?id=radar_volumen_hochficht-v1-5min/filelisting
- CKAN metadata API: https://data.hub.geosphere.at/api/3/action/package_show?id=radar_volumen_hochficht-v1-5min
- DOI: https://doi.org/10.60669/3p7s-wg05
- Licence (CC BY 4.0): https://creativecommons.org/licenses/by/4.0/legalcode
- GeoSphere Austria Data Hub: https://data.hub.geosphere.at/
- Meteopress C-band radar product page: https://www.meteopress.com/product/c%E2%80%91band-radar/
- Austro Control weather radar (the national network, for contrast): https://www.austrocontrol.at/en/weather/weather_for_all/weather_radar
- ODIM_H5 format specification (EUMETNET OPERA): https://www.eumetnet.eu/activities/observations-programme/current-activities/opera/
