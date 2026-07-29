# MeteoSwiss Radar (Switzerland — National Network: Precipitation, Hail & Polar Volumes)

## What this is
MeteoSwiss operates a five-radar C-band dual-polarisation network covering the whole of Switzerland and the surrounding Alpine region. Three of the five sites sit on high Alpine peaks — a deliberate design choice for a country where conventional valley-floor siting would leave large parts of the domain shadowed by terrain.

Two distinct open channels carry the data, and they are technically unrelated:
- **Gridded composite products** (precipitation and hail) are published through the Swiss federal geodata **STAC API** at `data.geo.admin.ch`, in ODIM HDF5 on a 1 km Swiss national grid, with a rolling 14-day window.
- **Single-site polar volume scans** are published through the **EUMETNET MeteoGate "Open Radar Data" (ODR) API**, an OGC API–EDR service, in ODIM HDF5 with a rolling ~24-hour window.

Two further product families listed in the MeteoSwiss open-data documentation — reflectivity-based products and convection products — are marked *"not yet realised"* and carry no data (see *Scope note*).

---

## Who operates it
- **Operator:** Federal Office of Meteorology and Climatology (MeteoSwiss / MeteoSchweiz / MétéoSuisse / MeteoSvizzera).
- **Country / region:** Switzerland.
- **Data distributor (composites):** Swiss Federal Spatial Data Infrastructure (FSDI), `data.geo.admin.ch`, via the STAC API.
- **Data distributor (polar volumes):** EUMETNET MeteoGate, Open Radar Data (ODR) API — the same channel that serves the OPERA member networks.

---

## Network composition
Five **C-band dual-polarisation Doppler** radars, all Leonardo/Selex GEMATRONIK `GEMA600` systems (verified from the ODIM `/how/system` attribute), wavelength **5.48–5.50 cm** (~5.47 GHz), **1.0° beamwidth**, 360 rays per sweep at 500 m gate spacing.

| Site | ODIM node / WIGOS ID | WMO | Position | Altitude | Status |
|---|---|---|---|---|---|
| **Albis** (near Zurich) | `chalb` / `0-756-0-chalb` | 06661 | 47.2843°N, 8.5120°E | 938 m | Upgraded 2012 |
| **La Dôle** (near Geneva) | `chdol` / `0-756-0-chdol` | 06699 | 46.4251°N, 6.0994°E | 1,682 m | Upgraded 2011 |
| **Monte Lema** (Ticino) | `chlem` / `0-756-0-chlem` | 06768 | 46.0408°N, 8.8332°E | 1,626 m | Upgraded 2011 |
| **Pointe de la Plaine Morte** (Valais) | `chppm` / `0-756-0-chppm` | 06726 | 46.3706°N, 7.4866°E | 2,937 m | New, from 2014 |
| **Weissfluhgipfel** (Graubünden) | `chwei` / `0-756-0-chwei` | 06776 | 46.8350°N, 9.7945°E | 2,850 m | New, from 2016 |

The two high-Alpine sites (Plaine Morte, Weissfluhgipfel) were added under the **Rad4Alp** programme, which also re-equipped the three legacy sites with dual polarisation. All five radars appear in the composite metadata as `nodes: WMO:06661,WMO:06699,WMO:06768,WMO:06726,WMO:06776` and as the internal radar string `ADLPW` (Albis, Dôle, Lema, Plaine Morte, Weissfluhgipfel).

The **CombiPrecip** product additionally blends the radar field with the MeteoSwiss automatic rain-gauge network — 267 gauges were used in the sample file inspected, with the included and excluded station lists written into the file metadata run by run.

---

## Products

### Gridded composites (STAC API)
All composite products share one grid, verified from the ODIM `/where` attributes:

- **710 × 640 pixels at 1 km**, Swiss oblique Mercator — `+proj=somerc +lat_0=46.95240555555556 +lon_0=7.439583333333333 +x_0=2600000 +y_0=1200000 +ellps=bessel` (**CH1903+ / LV95, EPSG:2056**)
- Corner coordinates 43.63°N/3.17°E (lower-left) to 49.36°N/12.46°E (upper-right) — the grid extends well beyond Switzerland into France, Germany, Austria and Italy
- Values are stored as **float64 physical units** with `gain = 1.0`, `offset = 0.0`; no integer scaling to undo. `nodata = NaN`, `undetect = 0.0`

| Product | Code | Quantity | Unit | Cadence | Window |
|---|---|---|---|---|---|
| **PRECIP** | `RZC` | `RATE` | mm/h | 5 min | 5-min instantaneous rain rate |
| **PRECIP-SV** | `TZC` | `RATE` | mm/h | 5 min | As PRECIP, plus side-view cross-sections |
| **CombiPrecip** | `CPC` | `ACRR` | mm | 5 min | 60-min accumulation, gauge-adjusted |
| **CombiPrecip reanalysis** | `CPC` | `ACRR` | mm | hourly, +8 days | 60-min accumulation, final gauge adjustment |
| **Probability of Hail** | `BZC` | `POH` | fraction 0–1 | 5 min | 5-min, plus 24-h aggregates |
| **Maximum Expected Severe Hail Size** | `MZC` | `MESH` | mm | 5 min | 5-min, plus 24-h aggregates |

- **PRECIP (RZC)** is the baseline surface rain-rate composite. The Z–R relationship is recorded in the file metadata as `usr_zrA = 316.00`, `usr_zrB = 1.50` (Z = 316·R^1.5), with a saturation ceiling documented at 118 mm/h (`usr_max_rainrate = 120.00`).
- **PRECIP-SV (TZC)** carries the same rain-rate field but on a **786 × 716** array rather than 710 × 640 — the extra 76 rows and columns are the vertical side-view strips appended along the x- and y-axes. Anyone treating RZC and TZC as interchangeable grids will get a shape mismatch.
- **CombiPrecip (CPC)** merges radar with the gauge network in real time. The filename embeds a **quality code 0–9** (9 best) that reflects the gauge-adjustment quality for that hour.
- **POH / MESHS** are computed from the echo-top-at-45-dBZ field (`EZC`) combined with the **freezing-level height forecast from ICON-CH1-EPS** (`nwp_processing: model:ICONCH1EPS;mode:fcst`, reading the `HZT` freezing-level file). They are radar-plus-NWP diagnostics, not pure radar observations. MESHS is integer-valued in mm and reports only values **≥ 20 mm** (2 cm) by design.
- **Daily hail aggregates:** timestamps `2400` (00–24 UTC) and `3000` (06–06 UTC) are 24-hour maxima rather than 5-minute fields. A full day carries 580 hail assets: 2 × 288 five-minute files plus 2 × 2 daily aggregates.

### Single-site polar volumes (ODR API)
Each radar publishes **elevation sweeps in ODIM HDF5**, one file per elevation containing all three moments together (`DBZH_TH_VRADH`), on the same **5-minute** cycle as the composites.

- **Moments:** `DBZH` (corrected horizontal reflectivity), `TH` (uncorrected/total horizontal reflectivity), `VRADH` (radial velocity). Reflectivity is `uint8` with `gain = 0.5`, `offset = −32.0`; radial velocity has a per-sweep gain with an observed Nyquist of ±8.22 m/s at the lowest elevation.
- **Geometry:** 360 rays × 324–492 bins, `rscale = 500 m` (maximum range 162–246 km depending on elevation), `rstart = 0`, with per-ray `startazA`/`stopazA` azimuth arrays.
- **Elevations available:** **−0.2°, 0.4°, 1.0°, 1.6°, 2.5°** — five sweeps per site. The operational MeteoSwiss volume scan runs a considerably deeper elevation set; the ODR feed exposes only the lowest five.
- **Not published:** despite all five radars being dual-polarisation, the ODR feed carries **no polarimetric moments** for Switzerland — no `ZDR`, `RHOHV`, `PHIDP`, `LDR`. Other ODR members do publish these, so the omission is a Swiss distribution choice rather than an API limitation.

---

## Data availability
- **Is the data free?** Yes.
- **Is the data downloadable?** Yes.
- **Access tier:** **Open, no account, no key, no registration.** Composite assets are plain unsigned HTTPS URLs under `data.geo.admin.ch` (unlike the MeteoSwiss NWP collections, which use pre-signed CSCS object-store URLs). The ODR API is likewise anonymous.
- **Data formats:** **ODIM HDF5** throughout — `ODIM_H5/V2_4` for the composites (`H5rad 2.3` for CombiPrecip), `ODIM_H5/V2_3` for the polar volumes. Typical file size well under 1 MB for composites, ~58 kB per polar sweep.
- **Update cadence:** 5 minutes for all real-time products; hourly for the CombiPrecip reanalysis (published 8 days later).
- **Latency** (measured across a full day of assets, 28 July 2026): PRECIP and PRECIP-SV median **~1.6 min** after valid time (p90 2.3 min); CombiPrecip median **~5.9 min** (p90 6.8 min).
- **Primary access — composites (STAC):**
```
  https://data.geo.admin.ch/api/stac/v1/collections/ch.meteoschweiz.ogd-radar-precip/items
  https://data.geo.admin.ch/api/stac/v1/collections/ch.meteoschweiz.ogd-radar-hail/items
```
  Browse UI: `https://data.geo.admin.ch/browser/index.html#/collections/ch.meteoschweiz.ogd-radar-precip`
- **Primary access — polar volumes (ODR / OGC API–EDR):**
```
  https://api.meteogate.eu/eu-eumetnet-weather-radar/collections/observations/locations/0-756-0-chalb?parameter-name=DBZH:scan&level=-0.2&format=ODIM
```
  The response is CoverageJSON; the actual HDF5 files are the `application/x-odim` entries in its `links` array, hosted at `https://s3.waw3-1.cloudferro.com/openradar-24h/{YYYY}/{MM}/{DD}/CH/{node}/SCAN/{node}@{YYYYMMDD}T{HHMM}@{elev}@DBZH_TH_VRADH.h5`.
- **New-data notifications:** ODR provides MQTT over WebSocket — `wss://radar.meteogate.eu/ordmqtt` (CoverageJSON) and `wss://radar.meteogate.eu/wis2mqtt` (WIS2 GeoJSON). No equivalent for the STAC composites.
- **Archive depth:** **14 days rolling** for the STAC composites; **~24 hours rolling** for ODR polar volumes (the bucket is literally named `openradar-24h`). Longer archives are available from MeteoSwiss **on request via the contact form** — an approval-gated channel, not an open one.
- **Licence:** **CC BY 4.0** on both channels (STAC collections declare `CC-BY`; ODR declares the CC BY 4.0 URL per item). Attribution string requested by MeteoSwiss: *"Source: MeteoSwiss"*.

---

## Scope note
Two of the five radar product families in the MeteoSwiss open-data documentation are **not yet realised** and publish nothing:
- **Reflectivity-based radar products** (`d2`) — marked "(not yet realised)". This is the notable gap: there is currently **no open gridded reflectivity composite** (CAPPI, column-max, echo top) for Switzerland through the national channel. Reflectivity is only available as single-site polar sweeps via ODR, or indirectly through the [OPERA pan-European composite](../eu/opera-composite.md), to which Switzerland contributes.
- **Convection radar products** (`d4`) — marked "(not yet realised)".

Both are worth re-checking periodically; the placeholder pages imply planned releases. The historical archive beyond the rolling windows is request-gated and therefore out of scope under the repository's no-approval-gates rule, but the real-time feeds themselves are fully open.

---

## Notes
- **Observation, not forecast.** PRECIP and the polar volumes are observational. **CombiPrecip is a multi-sensor analysis** (radar + rain gauges), and **POH / MESHS are radar-plus-NWP diagnostics** that ingest an ICON-CH1-EPS freezing-level forecast — none of the three is raw radar.
- **POH is a 0–1 fraction, not a percentage.** The MeteoSwiss documentation describes POH as "values ranging from 0 to 100%" and its metadata table gives the unit as `%`, but the ODIM files store values stepping from 0.00 to 1.00 in 0.01 increments, with an **empty** `unit` attribute. Multiply by 100 for percent. Verified 29 July 2026.
- **MESHS is in millimetres, not centimetres.** The `ch.meteoschweiz.ogd-radar-hail` STAC collection description says "maximum expected severe hail size in cm"; the ODIM `unit` attribute is `mm` and observed values run 20–56 in integer steps. The documentation page's own metadata table (mm) is the correct one.
- **The STAC bounding box understates the grid.** Both radar collections advertise `[5.96, 45.82, 10.49, 47.81]` — roughly Switzerland — but the actual composite grid spans 43.6–49.4°N and 2.7–12.5°E. Do not use the collection bbox to size a subsetting request.
- **One STAC item per calendar day.** Each item holds ~864 assets (3 × 288 five-minute files) for precipitation and ~580 for hail, with the *following* day's item created empty in advance and filled through the day. The 14-day window therefore shows 16 items.
- **The 8-day CombiPrecip reanalysis overwrite is observable.** Hourly CPC assets from 15 July 2026 carry `created: 2026-07-15` and `updated: 2026-07-23` — exactly eight days later. Where the reanalysis changes the quality code, a **second file with a different name is created rather than overwriting**, which is why a reanalysed day carries 294 CPC assets instead of 288. Any archiving pipeline that keys on filename alone will end up with both versions; keyed on timestamp alone, it will silently mix real-time and reanalysed values.
- **Hail products are seasonal.** POH and MESHS are computed only between 1 April and 30 September. Files published outside that window exist but are empty.
- **Relationship to NWP.** These radars feed MeteoSwiss's own [ICON-CH1/2-EPS](../../../models/nwp_models/regional/switzerland/icon-ch-eps.md) through latent heat nudging in the KENDA assimilation cycle — and the hail products consume an ICON-CH1-EPS forecast in return, making this an unusually tight two-way coupling between the radar and NWP entries in this catalog.
- **Data feed vs viewer.** The MeteoSwiss "Precipitation (Radar)" web and app displays are rendered viewers; the gridded feed is the ODIM HDF5 described here.
- **Single-site vs composite.** Polar volumes come only from ODR; composites come only from the FSDI STAC API. Neither channel carries the other's products.
- **ODR metadata quirk.** Every ODR item reports `unit: "%"` regardless of moment — DBZH, TH and VRADH all show it. Read units from the ODIM file, not the API metadata.
- **Alpine siting trade-off.** Peak-mounted radars at 2,850–2,937 m see over the terrain but overshoot low-level precipitation in the valleys; the composite chain applies per-radar visibility and vertical-profile corrections (`usr_visib_lower/upper`, `usr_vpr_exp_decay`, ground-level bias maps) whose parameters are written into every file's `how/MeteoSwiss` group.

---

## Recent version history
### June 2026 — CombiPrecip v4.0.1
Files carry `COMBIPRECIP-V4.0.1_2026-06-03` in the product-parameters group.

### 2016 — Weissfluhgipfel enters operation
Fifth radar, completing the Rad4Alp network expansion.

### 2014 — Pointe de la Plaine Morte enters operation
Fourth radar; first of the two new inner-Alpine sites.

### 2011–2012 — Rad4Alp dual-polarisation upgrade
Monte Lema and La Dôle (2011) and Albis (2012) re-equipped with dual-polarisation hardware.

---

## Official documentation
- Swiss weather radar network overview: https://www.meteoswiss.admin.ch/weather/measurement-systems/atmosphere/weather-radar-network.html
- Precipitation radar products (open data): https://opendatadocs.meteoswiss.ch/d-radar-data/d1-precipitation-radar-products
- Hail radar products (open data): https://opendatadocs.meteoswiss.ch/d-radar-data/d3-hail-radar-products
- Polar 3D radar products (open data): https://opendatadocs.meteoswiss.ch/d-radar-data/d5-polar-3d-radar-products
- EUMETNET Open Radar Data documentation: https://eumetnet.github.io/openradardata-documentation/
- ODR API reference (OpenAPI): https://api.meteogate.eu/eu-eumetnet-weather-radar/docs
- Terms of use (CC BY 4.0): https://opendatadocs.meteoswiss.ch/general/terms-of-use
- Radar FAQ: https://www.meteoswiss.admin.ch/weather/measurement-systems/atmosphere/weather-radar-network/faq-radar.html
- Weather Radar in Complex Orography (MeteoSwiss report): https://www.meteoswiss.admin.ch/dam/jcr:99f6865b-11ee-463b-814e-723d823dc5fd/weather_radar_in_complex_orography.pdf
- Operational Use of Radar for Precipitation Measurements in Switzerland: https://www.meteoswiss.admin.ch/dam/jcr:600197d5-fe54-495c-a6f6-5418147f301b/meteoswiss_operational_use_of_radar.pdf
