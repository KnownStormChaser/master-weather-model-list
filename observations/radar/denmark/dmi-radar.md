# DMI Weather Radar Network (Denmark)

## What this is
The Danish Meteorological Institute (DMI) operates a five-radar C-band network covering Denmark and its surrounding seas, and publishes the full observational output — national composite, per-radar pseudo-CAPPI, and raw per-radar polarimetric volumes — as ODIM HDF5 through an open, unauthenticated STAC / OGC API-Features service. All three product families are genuine gridded/polar binary data, not rendered imagery.

This is a single-operator national network. The distinguishing features are the depth of the open archive (a rolling **180 days**, unusually generous for a real-time radar feed — most European national feeds retain days) and the fact that **raw, unfiltered volume data** including the full dual-polarisation moment set is published operationally with no account, no key, and no approval gate. Denmark also runs two interleaved scan strategies — a long-range reflectivity scan and a short-range dedicated Doppler scan, alternating every 5 minutes — and both are exposed separately through a `scanType` query parameter.

---

## Who operates it
- **Operator / coordinating programme:** Danmarks Meteorologiske Institut (DMI), Ministry of Climate, Energy and Utilities
- **Country / region:** Denmark (domain extends over the North Sea, Baltic, southern Norway/Sweden and northern Germany)
- **Data distributor (if different):** DMI Open Data — Radar Data API (`opendataapi.dmi.dk/v1/radardata`)
- **Contributing networks / members:** n/a (single operator)

---

## Network composition
Five **Vaisala WRM200** C-band radars (wavelength 5.33 cm, beamwidth 0.95°, dual-polarisation). Site metadata read from the ODIM `what/source` and `where` groups of live volume files (29 July 2026):

| Site | API `stationId` | ODIM `what/source` | Lat (°N) | Lon (°E) | Antenna height | Active from |
|---|---|---|---|---|---|---|
| Stevns | `06177` | `WMO:06177,RAD:DN41,PLC:Stevns,NOD:dkste` | 55.32619 | 12.44928 | 53 m | 2001-04-01 |
| Rømø / Juvre | `60960` | `WMO:06096,RAD:DN42,PLC:Romo,NOD:dkrom` | 55.17311 | 8.55200 | 15 m | 1992-03-01 |
| Sindal | `06036` | `WMO:06036,RAD:DN43,PLC:Sindal,NOD:dksin` | 57.48931 | 10.13647 | 109 m | 1994-07-01 |
| Bornholm | `06194` | `WMO:06194,RAD:DN44,PLC:Bornholm,NOD:dkbor` | 55.11275 | 14.88752 | 171 m | 2002-03-05 |
| Samsø | `06133` | `WMO:06133,RAD:DN46,PLC:Samso,NOD:dksam` | 55.81190 | 10.58530 | 48 m | 2024-10-11 |

Notes on the table:
- **Coordinates above are the in-file (antenna) values.** DMI's published station table gives values differing by roughly 50–150 m (e.g. Stevns 55.32562 / 12.44817 vs 55.32619 / 12.44928). Prefer the in-file `where/lat`,`where/lon` for georeferencing.
- **`RAD:DN45` is absent** from the live network. DMI's station table lists a sixth radar, **Virring/Skanderborg** (`06103`, 56.02387 / 10.02517, active from 2008-11-01), with status *Inactive*; Samsø came online 2024-10-11. Whether DN45 was Virring's code is not stated in any source checked — **TBD**.
- No non-radar sensors are blended into any of these products (no gauge adjustment, no lightning, no NWP). This is radar-only.

### Scan strategy (from `how` and `dataset*/where`, live 29 July 2026)
Two interleaved volume scans, each 10 sweeps × 360 rays, 500 m range gates, nominal elevations 0.5°–15° (measured set ≈ 0.49, 0.65, 0.96, 1.47, 2.37, 4.80, 8.40, 9.97, 12.99, 15.00°):

| | `scanType=fullRange` | `scanType=doppler` |
|---|---|---|
| Minutes past hour | `:00, :10, … :50` | `:05, :15, … :55` |
| `how/task` | `REFLECTI` | `VELOCITY` |
| Range gates | 474–475 (varies by site) | 239 |
| Max range | 237.0–238.5 km (documented as 240 km) | 119.5 km (documented as 120 km) |
| `how/prf` | 625 Hz, `prffac` 1 | 1180 Hz, `prffac` 2 (dual-PRF) |
| Nyquist velocity (`how/NI`) | ±8.3 m s⁻¹ | ±47.2 m s⁻¹ |

---

## Products

### 1. Composite (`composite`)
National maximum-value reflectivity composite across all five radars.
- **Filename:** `dk.com.<YYYYMMDDHHMM>.500_max.h5`
- **Format:** ODIM HDF5 v2.0, `what/object = COMP`, single dataset, `quantity = DBZH`, `uint8`
- **Grid:** 1728 × 1984 (ny × nx) at 500 m = **864 × 992 km**
- **Projection:** polar stereographic — `+proj=stere +ellps=WGS84 +lat_0=56 +lon_0=10.5666 +lat_ts=56`
- **Cadence:** 5 minutes (288 files/day, verified 28 July 2026)
- **Compositing rule:** maximum value in areas of overlap
- Both scan types are written to the **same grid**; the Doppler-based composite simply covers less of it (a sample pair showed 46.2 % `nodata` for `fullRange` vs 78.8 % for `doppler`, consistent with the 240 km / 120 km range difference)
- Clutter-filtered (image-processing chain applied)

### 2. Pseudo-CAPPI (`pseudoCappi`)
Per-radar near-surface reflectivity, composed across the sweeps of a single volume, at a minimum height of 500 m.
- **Filename:** `<prefix><YYYYMMDD>_<HHMM>.Z_nn_S1.ps.500.wrk.h5` — prefixes observed: `ekxs` (Stevns), `eksn` (Sindal), `eksm` (Samsø), `ekrn` (Bornholm)
- **Format:** ODIM HDF5 v2.2, `what/object = IMAGE`, `product = PCAPPI`, `quantity = DBZH`, `uint8`
- **Grid:** 960 × 960 at 500 m = **480 × 480 km**, one grid per radar
- **Projection:** **gnomonic**, centred on each radar — `+proj=gnom +ellps=WGS84 +lon_0=<site lon> +lat_0=<site lat>` (note: a *different* projection family from the composite)
- **Cadence:** 10 minutes (≈575 files/day across four sites)
- **Only four of the five radars are published — Rømø is absent** (see Notes)
- Clutter-filtered

### 3. Volume (`volume`)
Raw, unfiltered per-radar polarimetric volumes — the most basic product.
- **Filename:** `dk<site>_<YYYYMMDDHHMM>.vol.h5` (`dkste`, `dkrom`, `dksin`, `dkbor`, `dksam`)
- **Format:** ODIM HDF5 v2.0 (`what/version = H5rad 2.0`), `what/object = PVOL`, 10 sweeps (`dataset1`…`dataset10`), `uint8`
- **Geometry:** 360 rays × 239 or 474/475 gates, `rscale` 500 m, `rstart` 0.5 km, `a1gate` 0
- **Moments (8, identical on every sweep):**

  | `quantity` | Meaning |
  |---|---|
  | `DBZH` | Horizontal reflectivity (corrected) |
  | `TH` | Horizontal total (uncorrected) reflectivity |
  | `VRAD` | Radial velocity |
  | `WRAD` | Spectrum width |
  | `ZDR` | Differential reflectivity |
  | `RHOHV` | Co-polar correlation coefficient |
  | `PHIDP` | Differential phase |
  | `LDR` | Linear depolarisation ratio |

  `KDP` is **not** distributed — derive it from `PHIDP` if needed.
- **Cadence:** 5 minutes per site (≈1438 files/day across five sites)
- **No clutter filtering** — clutter is present as recorded

### Value scaling (all products)
`value = raw × gain + offset`, with `nodata` and `undetect` sentinels read from the same `what` group.

- **`DBZH` / `TH`:** `gain = 0.5`, `offset = −32.0` → **dBZ = 0.5 × raw − 32.0**, spanning −31.5 … +95.0 dBZ over raw 1…254
- **`nodata = 255`** (outside coverage), **`undetect = 0`** (in coverage, no echo). Because `undetect` is 0, raw 0 must **not** be read as −32.0 dBZ; the usable scale starts at raw 1.
- **`VRAD` gain/offset vary by scan type and by file** — `fullRange` samples read `gain = 0.065576`, `offset = −8.328125`; `doppler` samples read `gain = 0.371421`, `offset = −47.1705`. Always read the per-dataset `what` attributes rather than hardcoding.
- **Z–R relation:** `how/zr-a = 200.0`, `how/zr-b = 1.6` (Marshall–Palmer)
- No clipping observed: composites from the convective afternoon of 17 July 2026 reached **68.5 dBZ** with a smooth distribution up to the maximum, and volume `DBZH` reached 67.5 dBZ.

---

## Data availability
- **Is the data free?** Yes — no account, no registration, no API key. (`api-key` is an *accepted* query parameter on every collection but is not required; all requests below were made anonymously.)
- **License:** **CC BY 4.0.** DMI holds the IP rights and licenses its open data under Creative Commons Attribution 4.0; attribution to DMI and a link to the licence are required, commercial use is permitted, and no share-alike obligation applies. The `/collections` response carries a machine-readable `rel="license"` link to DMI's terms-of-use page, which is the direct confirmation that the licence covers the radar service specifically. Licence terms last changed 30 November 2023.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** ODIM HDF5 (`application/x-hdf5`) — v2.0 for composite and volume, v2.2 for pseudo-CAPPI.
- **Update cadence:** composite 5 min; pseudo-CAPPI 10 min; volume 5 min per site.
- **Latency:** ~8–13 minutes measured. Files are published in pairs on a 10-minute batch: the `:00` `fullRange` and `:05` `doppler` products both appeared at ≈`:13`.
- **Primary access — Radar Data API** (STAC API-Features, base `https://opendataapi.dmi.dk/v1/radardata`):

  | Endpoint | Purpose |
  |---|---|
  | `/collections` | List the three collections; carries the `rel="license"` link |
  | `/collections/{composite\|pseudoCappi\|volume}/items` | Query features (one per file) |
  | `/collections/{collection}/items/{id}` | Single feature |
  | `/download/{file-name}` | Retrieve the HDF5 file |
  | `/conformance`, `/api` | Conformance classes; OpenAPI 3.0 definition |

- **Query parameters** (support differs per collection — verified by sending a bogus parameter and reading the 400 response):

  | Parameter | `composite` | `pseudoCappi` | `volume` |
  |---|---|---|---|
  | `datetime`, `bbox`, `bbox-crs`, `limit`, `offset`, `sortorder`, `api-key` | ✅ | ✅ | ✅ |
  | `scanType` (`doppler` \| `fullRange`) | ✅ | — (rejected) | ✅ |
  | `stationId` | — (rejected, 400) | ⚠️ accepted but **silently ignored** | ✅ works |

  `limit` defaults to 1000 and caps at 300000; `offset` caps at 1000000. `sortorder` accepts **only** `datetime,DESC` — any other value, including `datetime,ASC`, returns 400.
- **New-data notifications:** none. No SNS, webhook, or feed — poll the `items` endpoint. DMI's fair-use guidance asks users to poll at the product's actual update cycle rather than continuously, to filter server-side, and to cache; no numeric rate limit is published, but DMI reserves the right to throttle per-IP.
- **Other mirrors:** none known for the radar products. DMI's terms reference "Bulk Download Services" alongside the APIs, but no bulk radar channel is documented — **TBD**.
- **Archive depth:** **Rolling 180 days**, matching DMI's documentation and confirmed empirically with a sharp cutoff. Date-window probes on 29 July 2026 returned zero features for 2026-01-29 (181 days back) and non-zero for 2026-01-30 (180 days back) in all three collections. No deeper archive is offered through this service.
- **Approximate volume:** ~1.5 GB/day (volumes ≈1.0–1.15 MB each dominate; composites 37–166 kB, pseudo-CAPPI ≈20 kB), so roughly 270 GB for the full 180-day window.

---

## Example — fetch the latest composite

```bash
BASE=https://opendataapi.dmi.dk/v1/radardata

# Newest full-range composite
ID=$(curl -s "$BASE/collections/composite/items?limit=1&scanType=fullRange&sortorder=datetime,DESC" \
     | python3 -c 'import json,sys; print(json.load(sys.stdin)["features"][0]["id"])')

curl -sL "$BASE/download/$ID" -o "$ID"
```

```python
import h5py, numpy as np

with h5py.File(ID, "r") as f:
    raw = f["dataset1/data1/data"][:]          # (1728, 1984) uint8
    w   = f["what"].attrs                      # composite: gain/offset live in root /what
    gain, offset = float(w["gain"]), float(w["offset"])
    nodata, undetect = float(w["nodata"]), float(w["undetect"])

dbz = np.where((raw == nodata) | (raw == undetect), np.nan, raw * gain + offset)
```

Note the group layout differs between products: in the **composite** the scaling attributes sit in the root `/what` and `quantity` is an attribute directly on the `dataset1/data1` *group*; in **pseudo-CAPPI** they are in `dataset1/what`; in **volumes** they are in `dataset1/dataN/what`. A generic reader must handle all three.

---

## Scope note
Clean fit — no tension to flag. Open licence (CC BY 4.0), open access with no account or key, gridded/polar binary containers, permanent documented operational channel, and a real-time feed rather than an archive-only dataset.

---

## Notes

**Observation, not forecast.** All three product families are observational. No nowcast or extrapolation products are carried in this API, so — unlike the DWD and ČHMÚ trees — there is nothing here to split off to `nowcasting_models/`.

**Rømø's `stationId` is not its WMO number.** The API indexes Rømø as `60960`, which is DMI's own station-registry number and matches DMI's published station table. The ODIM `what/source` *inside* the file records `WMO:06096`. Querying `stationId=06096` (or `6096`) returns zero features; only `60960` works. The other four sites' `stationId` values coincide with their WMO numbers, so this one site is an easy trap when building a station map from file metadata.

**`stationId` is silently ignored on `pseudoCappi`.** The parameter is accepted without error but has no effect — `stationId=06194` returns all four stations interleaved. It filters correctly on `volume`, and is rejected outright (400) on `composite`. Filter pseudo-CAPPI client-side on `properties.stationId`, the filename prefix, or `how/nodes`.

**Rømø publishes no pseudo-CAPPI.** Only four sites appear in the `pseudoCappi` collection (`06036`, `06133`, `06177`, `06194`). Spot checks at 2026-02-01, 03-15, 05-01, 06-15 and 07-20 returned the same four throughout, so this is a persistent gap across the whole retention window, not an outage. Rømø *is* present in `volume` and contributes to the composite. Cause not documented — **TBD**.

**Only descending sort is available.** `sortorder=datetime,DESC` is the sole accepted value. To reach the oldest end of the archive, page with `datetime` intervals rather than sorting ascending.

**Composite georeferencing needs care — the corner metadata is partly unreliable.**
- `where` **omits `xsize`/`ysize`** (required by ODIM). Take the shape from the dataset itself: `(1728, 1984)`. Pseudo-CAPPI does carry both.
- The four corner lat/lons are **pixel centres**, not cell edges: the projected span between `LL` and `UR` is `(nx−1) × 500` and `(ny−1) × 500`, not `nx × 500`.
- `UL` is given as exactly `(3.0, 60.0)` — suspiciously round, and inconsistent with the grid, which implies `≈(2.99900, 60.00445)`.
- `LR_lat` is **bit-identical to `LL_lat`** (`52.29427206432812`), which is geometrically impossible for a rectangular grid in a stereographic projection. The grid implies `LR ≈ (18.90052, 52.15864)`, so the published `LR_lat` is wrong by ≈0.136° ≈ 15 km.
- `LL`, `UL` and `UR` are mutually consistent to ~0.01°. **Georeference from `LL` + `projdef` + array shape**, and do not trust `LR`.

**The API's STAC `bbox` understates the domain.** Features report `bbox = [4.379, 52.294, 20.735, 59.828]`, computed from `LL` and `UR` only. The true north-west extent reaches lon ≈2.999 and lat ≈60.004 at the `UL` corner, so `bbox` filtering may behave unexpectedly near the north-west of the composite.

**STAC conformance is partial.** `/conformance` declares OGC API-Features 1.0 (core, oas30, geojson) plus `api.stacspec.org/v1.0.0-rc.2/core`, but: Collection objects **omit `extent`** entirely (hence the empirical retention measurement above), items expose the download link under **`asset` (singular)** rather than STAC's standard `assets`, and `sortorder`/`stationId` are custom parameters in place of STAC's `sortby`. Generic STAC clients will not resolve the download hrefs without a shim.

**ODIM deviations — DMI documents this explicitly** ("our radar data files to some extent differs from the ODIM HDF5 standard in relation to metadata"). Observed:
- Composite `what/source = DMI-RADARGROUP` — not valid ODIM source syntax (no `WMO:`/`RAD:`/`NOD:` token).
- Pseudo-CAPPI `what/source = CTY:611` — country only. Identify the radar from `how/nodes` (`dkste`/`dkrom`/`dksin`/`dkbor`/`dksam`) or the filename prefix.
- Pseudo-CAPPI omits `prodpar`, so the CAPPI height is absent from the file; DMI's documentation gives the minimum height as 500 m.
- Volume files are properly formed, with full `WMO:/RAD:/PLC:/NOD:` source strings.

**Radial velocity from `fullRange` is close to useless.** Its ±8.3 m s⁻¹ Nyquist (single-PRF, 625 Hz) means pervasive folding. The dedicated `doppler` scan exists precisely for this and gives ±47.2 m s⁻¹ via dual-PRF. `VRAD` is nonetheless present in both.

**Clutter handling differs by product.** Volumes are raw as recorded — land and sea surface, buildings, wind turbines, birds, WiFi and solar interference all included. Composite and pseudo-CAPPI have passed a clutter-removal chain, which DMI notes is imperfect in both directions (residual clutter, and occasional removal of real precipitation).

**Reflectivity interpretation.** DMI's rule of thumb: ≈20 dBZ light precipitation (<1 mm/h), ≈35 dBZ moderate (≈5 mm/h), >45 dBZ heavy (>20 mm/h) or hail.

**Feed completeness.** Near-complete but not perfect: 28 July 2026 returned 288/288 composites, 575/576 pseudo-CAPPI, and 1438/1440 volumes.

**Data feed vs viewer.** DMI's public radar animation at `dmi.dk/radar` is rendered imagery and is **not** this feed. The API above is the data.

**Relationship to OPERA.** Denmark is a EUMETNET OPERA member and these radars contribute to the pan-European [OPERA](../eu/opera-composite.md) composite; this DMI feed is the national-resolution counterpart, and the only route to the raw Danish volumes.

**Relationship to NWP.** DMI runs a HARMONIE-AROME configuration (see [`harmonie-dmi`](../../../models/nwp_models/regional/denmark/harmonie-dmi.md)). Whether these radars are assimilated into that suite, and in what form, is not stated in the radar documentation — **TBD**.

**Reading the files.** Standard ODIM HDF5 tooling works: `h5py`, `wradlib`, `xradar`, `Py-ART`. Expect to handle the group-layout and `source` deviations noted above.

---

## Official documentation
- Radar data overview (network, products, formats, station table): https://www.dmi.dk/friedata/dokumentation/radar-data
- Radar Data API documentation (query parameters, response schema): https://www.dmi.dk/friedata/dokumentation/radar-data-api
- Swagger / interactive API explorer: https://opendataapi.dmi.dk/v1/radardata/swagger-ui/index.html
- Terms of use and licence (CC BY 4.0): https://www.dmi.dk/friedata/dokumentation/terms-of-use
- DMI Open Data portal: https://www.dmi.dk/frie-data
- Dataset metadata record (geodata-info.dk): https://geodata-info.dk/srv/dan/catalog.search#/metadata/a3cbb502-ec6f-4f37-ac21-bb90a16a9f6c
- ODIM HDF5 v2.0 (volume, composite): https://www.eumetnet.eu/wp-content/uploads/2019/05/OPERA-ODIM_H5-v2.01.pdf
- ODIM HDF5 v2.2 (pseudo-CAPPI): https://www.eumetnet.eu/wp-content/uploads/2019/05/OPERA-ODIM_H5-v2.2.pdf
- ODIM HDF5 v2.3 (describes v2.0 → v2.2 changes): https://www.eumetnet.eu/wp-content/uploads/2019/01/ODIM_H5_v23.pdf
- EUMETNET OPERA programme: https://www.eumetnet.eu/observations/weather-radar-network/
- Open Data contact: friedata_kontakt@dmi.dk

---

*Entry verified against live API responses and downloaded ODIM HDF5 files on 29 July 2026.*
