# FMI Radar (Finland — Composites, Single-Radar Products & Volume Data)

## What this is
The Finnish Meteorological Institute (FMI) publishes its national weather-radar
data as open data — in real time and, on AWS, as a deep archive. Three product
families are available: national **composites** over Finland, per-radar
**single-radar products**, and raw per-radar **polarimetric volume data**. It is
observational, updated every ~5 minutes (the most recent data is typically less
than 5 minutes old), and the primary access route is two anonymous AWS S3
buckets, with the FMI Open Data WFS/WMS as a secondary route.

Finland is a EUMETNET OPERA member — FMI's accumulation products also appear in
the OPERA national-products links — so this is the fuller FMI national feed,
complementary to the OPERA pan-European composite. Note the recency/archive split
between routes: the AWS buckets carry both a real-time tail and a multi-year
archive, whereas FMI's own WFS download service is limited to the past six days
(see Data availability).

---

## Who operates it
- **Operator:** Finnish Meteorological Institute (FMI / Ilmatieteen laitos) — fully automated network, designed, maintained, and operated by FMI.
- **Country / region:** Finland (composite over Finland; neighbouring countries' radars, via international exchange, improve coverage over sea areas).
- **Data distributor:** FMI Open Data — AWS S3 and the FMI WFS/WMS services.

---

## Network composition
12 dual-polarisation C-band Doppler radars (Vaisala), covering most of Finland
with annual availability above 98%. Each radar has a 4.5 m antenna, 0.95° beam
width, magnetron transmitter, 5600–5650 MHz (5.3 cm wavelength), 250 kW peak
power. A radar sees precipitation to ~250 km (summer rain) or ~120 km (winter
snow). Sites: Korppoo, Vihti, Anjalankoski, Kankaanpää, Kesälahti, Petäjävesi,
Kuopio, Vimpeli, Nurmes, Utajärvi, Luosto, Inari (S3 site codes are `fi` + an
abbreviation, e.g. `fivim` = Vimpeli, `fikuo` = Kuopio, `filuo` = Luosto). The
composite additionally ingests neighbouring countries' radars over sea areas, and
is delivered in the Finnish national projection ETRS89 / TM35FIN (EPSG:3067).

---

## Products
**Composite products** (GeoTIFF; areas `finrad`, plus a low-latency `finradfast` variant):
- Radar reflectivity (`dbz`) — `Z[dBZ] = 0.5 × pixel − 32`
- Rainfall intensity (`rr`) — `rr[mm/h] = 0.01 × pixel`
- Precipitation accumulation over 1 h (`rr1h`), 12 h (`rr12h`), and 24 h (`rr24h`) — `[mm] = 0.01 × pixel`

**Single-radar products** (GeoTIFF, per site) — product types `ppi` (measurement angle in degrees, e.g. 0.3/0.7/1.5/9.0), `cappi` (height in metres, e.g. 600), `etop` (dBZ threshold, e.g. 20/45/50):
- Radar reflectivity (`dbzh`), radial velocity (`vrad`), rain classification (`hclass`: 0 = no signal, 1 = non-met, 2 = rain, 3 = wet snow, 4 = dry snow, 5 = graupel, 6 = hail), cloud-top height (`etop_20`: `height[km] = 0.1 × pixel − 0.1`)

**Volume data** (ODIM HDF5, per site) — full polarimetric moment set: `DBZH`, corrected `DBZHC`, `DBZX`, `TH`, `TX`, differential reflectivity `ZDR`/`ZDRC`, specific/differential phase `KDP`/`PHIDP`, correlation `RHOHV`, `VRAD`/`VRADC`, spectral width `WRAD`, hydrometeor class `HCLASS`, plus `SNR` and `SQI`. Follows the OPERA ODIM HDF5 convention.

---

## Data availability
- **Is the data free?** Yes — no account (users must accept the CC BY 4.0 licence before using the interfaces).
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** GeoTIFF (data-valued, scaled integers — apply the per-parameter conversions above, or read `linearTransformationGain`/`linearTransformationOffset` from the WFS response) for composites and single-radar products; ODIM HDF5 for volumes.
- **Update cadence:** Real-time, ~5 minutes (most recent data typically < 5 min old).
- **Primary access (AWS S3, `eu-west-1`, no account):**
  - `s3://fmi-opendata-radar-geotiff` — composites and single-radar products (GeoTIFF). Browse: http://fmi-opendata-radar-geotiff.s3-website-eu-west-1.amazonaws.com/
  - `s3://fmi-opendata-radar-volume-hdf5` — single-radar polarimetric volumes (ODIM HDF5). Browse: http://fmi-opendata-radar-volume-hdf5.s3-website-eu-west-1.amazonaws.com/
  - Layout: `{year}/{month}/{day}/{site-or-area}/{filename}`
    - Single-radar: `{timestamp}_{site}_{product}_{parameter}_{quantity}_{qc|raw}.tif` — e.g. `2026/07/12/fivim/202607120000_fivim_ppi_0.3_dbzh_qc.tif`
    - Composite: `{timestamp}_composite_{product}_{parameter}_{quantity}_{area}_{qc|raw}.tif` — e.g. `2026/07/12/finrad/202607120345_composite_cappi_600_dbzh_finrad_qc.tif`
    - `qualitycontrol` flag: `qc` (anomaly detection/removal applied) or `raw`. Timestamp is `YYYYMMDDHHMM` (UTC).
  - CLI: `aws s3 ls --no-sign-request s3://fmi-opendata-radar-geotiff/2026/07/12/`
- **Secondary access (FMI Open Data WFS/WMS, `opendata.fmi.fi`):** stored queries `fmi::radar::composite::{dbz,rr,rr1h,rr12h,rr24h}` and `fmi::radar::single::{dbz,vrad,hclass,etop_20}`. The WFS returns GridSeriesObservation features whose `om:result` carries a GetMap download link (`openwms.fmi.fi`, `image/geotiff`). Rate limits: 20 000 download requests/day, 10 000 view requests/day, 600 requests per 5 minutes combined.
- **New-data notifications:** SNS `arn:aws:sns:eu-west-1:916174725480:fmi-opendata-radar-geotiff-object_created` and `…:fmi-opendata-radar-volume-hdf5-object_created`.
- **Archive depth:** On AWS, a deep archive plus the real-time tail — the GeoTIFF bucket runs from **August 2020** to present, and the HDF5 volume bucket from **2007** to present. The FMI WFS/WMS service, by contrast, retains only the **past 6 days**.
- **Licence:** CC BY 4.0 (must be accepted before using the open-data interfaces).

---

## Scope note
- **Observation, not forecast.** Reflectivity, rain rate, accumulation, single-radar moments, and volumes — all observational, and genuinely real-time (~5 min).
- **National composite, complementary to OPERA.** Finland is an OPERA member; FMI's accumulation products also appear in OPERA's national-products links. This AWS/WFS dataset is the fuller FMI national feed.
- **GeoTIFF is data, not imagery.** Values are scaled integers with published conversions (and per-file transformation parameters in the WFS response), not rendered pictures.

---

## Notes
- **AWS retention beats the documentation.** FMI's open-data manual describes a 6-day window, which applies to the WFS/WMS download service; the S3 buckets actually hold multi-year archives (GeoTIFF from 2020-08, HDF5 volumes from 2007). Prefer S3 for any historical work.
- **`qc` vs `raw`.** Each GeoTIFF is flagged for whether anomaly detection/removal was applied.
- **Two composites.** `finrad` is the standard national composite; `finradfast` is a lower-latency variant (confirm its exact definition in FMI documentation).
- **Volume HDF5 = OPERA ODIM.** Readable with `wradlib`, `xradar`, `h5py`; corrected and uncorrected moments are both provided (`DBZH`/`DBZHC`, `VRAD`/`VRADC`, `ZDR`/`ZDRC`).
- **Radar-system renewal underway.** FMI is introducing new radar-layer/level names and has already changed the composite filename order (the area code now appears later in the name than in the documented example); older layer names are being phased out. Track the FMI open-data changelog when scripting queries.
- **Data feed vs viewer.** An `openwms.fmi.fi` GetMap request in `image/geotiff` counts as data download; other WMS output is treated as rendered view usage.

---

## Recent version history
- **2026 — radar-system renewal:** new radar-layer/level names being introduced; composite GeoTIFF filename order updated (area code moved); old layer names to be phased out (autumn). API users should adjust queries.

---

## Official documentation
- AWS registry: https://registry.opendata.aws/fmi-radar/
- Radar data on AWS S3 (FMI): https://en.ilmatieteenlaitos.fi/radar-data-on-aws-s3
- FMI radar network: https://en.ilmatieteenlaitos.fi/fmi-radar-network
- Open data manual — radar data (WFS/WMS): https://en.ilmatieteenlaitos.fi/open-data-manual-radar-data
- FMI open-data licence (CC BY 4.0): https://en.ilmatieteenlaitos.fi/open-data-licence
- Example code (`fmidev/opendata-resources`): https://github.com/fmidev/opendata-resources
