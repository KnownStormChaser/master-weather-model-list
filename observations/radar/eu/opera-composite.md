# OPERA Composite (EUMETNET Pan-European Radar Composite)

## What this is
OPERA (Operational Programme for the Exchange of Weather Radar Information) is
EUMETNET's weather-radar programme, coordinating radar cooperation across
European national meteorological services since 1999. Its central output is a
set of quality-controlled **Pan-European radar composites**, mosaicked from the
incoming polar scans and volumes of the members' national radar networks.

This entry focuses on the **OPERA composite products** — instantaneous maximum
reflectivity, instantaneous surface rain rate, and 1-hour rainfall accumulation
— covering the whole of Europe on a single grid. They are distributed through
the **Open Radar Data (ORD) API**, developed under the EU/EUMETNET RODEO project
to serve weather radar as an EU High-Value Dataset, and hosted on the European
Weather Cloud. As of 20 May 2026 the service is openly accessible with no
whitelisting. The ORD API also serves single-site volume data and links to
national composites for users who want less or more than the Pan-European
product (see Notes).

---

## Who operates it
- **Operator / coordinating programme:** EUMETNET OPERA. Composite generation is done centrally by the OPERA production systems (ODYSSEY, then CIRRUS and NIMBUS). The ORD API was built under the RODEO project, is hosted at the European Weather Cloud (EWC), and is served via the MeteoGate gateway.
- **Country / region:** Multinational European (EUMETNET; ~30 OPERA members).
- **Data distributor:** EUMETNET, via the ORD API (MeteoGate) and CloudFerro S3 storage.
- **Contributing networks / members:** ~30 OPERA members operating 200+ radars. Note that not all members share via ORD — non-EU members are not bound by the HVD regulation, and some EU members use their own national interfaces (or both).

---

## Network composition
OPERA aggregates ~200 national radars across ~30 member services, predominantly
C-band with some S- and X-band, spanning a wide range of hardware, ages, and
processing chains — one of the most heterogeneous radar bodies anywhere. The
composites are built from incoming polar scans and volumes of filtered
reflectivity; four quality filters (Saltikoff et al. 2019) are applied prior to
compositing, with national providers choosing whether the filters are applied to
their data. The composite grid covers all of Europe — area 3,800 × 4,400 km in a
Lambert Azimuthal Equal Area projection, with approximate corners (70°N 30°W),
(70°N 50°E), (32°N 15°W), (32°N 30°E) — at 2 km (ODYSSEY) or 1 km (CIRRUS,
maximum reflectivity).

---

## Products
Three composite products, distributed in ODIM HDF5 and cloud-optimized GeoTIFF
(older ODYSSEY production also in ODIM BUFR). ODIM standard names in parentheses.

- **Instantaneous Maximum Reflectivity, dBZ (`DBZH`):** each pixel holds the maximum of all contributing radars' polar-cell values at that location. Historically ODYSSEY at 2 × 2 km, every 15 min, issued ~15 min after data time; since July 2024 produced by **CIRRUS** at **1 × 1 km, 5-minute** cadence.
- **Instantaneous Surface Rain Rate, mm/h (`RATE`):** reflectivity converted to rainfall via the Marshall–Palmer relation. ODYSSEY (quality/distance/beam-altitude weighted average) through Oct 2024; **NIMBUS** since July 2024 (lowest-elevation-angle method).
- **One-Hour Rainfall Accumulation, mm (`ACRR`):** sum of the four preceding 15-minute rain-rate fields. ODYSSEY through Oct 2024; NIMBUS since July 2024.

Available both as a 24-hour rolling cache and a long-term archive back to 2012.

---

## Data availability
- **Is the data free?** Yes — openly accessible; as of 20 May 2026, no whitelisting required.
- **Is the data downloadable?** Yes.
- **Access tier:** Open. Anonymous S3 (`--no-sign-request`) and an anonymous rate-limited API; free API keys raise the rate limit.
- **Data formats:** ODIM HDF5 and cloud-optimized GeoTIFF (older ODYSSEY production also ODIM BUFR). ODIM data model v2.42.
- **Update cadence:** Maximum reflectivity every 5 min (CIRRUS, 1 km); rain rate on a 15-minute basis; accumulation hourly. (Archived ODYSSEY products are 15 min / 2 km.)
- **Primary access:**
  - **ORD API (OGC API – EDR) via MeteoGate:** https://api.meteogate.eu/eu-eumetnet-weather-radar — Swagger UI at https://api.meteogate.eu/eu-eumetnet-weather-radar/docs. For composites, query `location_id` = `0-20010-0-OPERA`, `method` = `comp`, `standard_name` = `DBZH` | `RATE` | `ACRR`. Free API keys at https://devportal.meteogate.eu/.
  - **S3 (CloudFerro / EWC, anonymous):** 24-hour cache bucket `openradar-24h`, endpoint `https://s3.waw3-1.cloudferro.com/`. Composites under `s3://openradar-24h/YYYY/MM/DD/OPERA/COMP/`.
    - List: `aws s3 ls --no-sign-request --endpoint-url https://s3.waw3-1.cloudferro.com/ s3://openradar-24h/2026/06/04/OPERA/COMP/`
    - File naming: `OPERA@<YYYYMMDDThhmm>@0@<VAR>.h5` (e.g. `OPERA@20260604T0220@0@DBZH.h5`)
    - Browse: https://s3.waw3-1.cloudferro.com/openradar-24h/
    - Long-term archive bucket: `openradar-archive` (population in progress — TBD).
- **New-data notifications:** MQTT (username `everyone`, port `8884`) — see the ORD subscribing documentation.
- **Archive depth:** Composites back to 2012 (long-term archive) plus a 24-hour rolling cache. The single-site volume archive is still being populated (TBD).
- **Licence:** CC BY 4.0. The composite products are the property of EUMETNET, which distributes them under CC BY 4.0. Attribution required.

---

## Scope note
- **Observation, not forecast.** The composites are purely observational (reflectivity, rain rate, accumulation) — a clean fit, with no bundled forecast/nowcast products.
- **Gated-access tension is resolved.** Earlier ORD access required IP whitelisting on request — more than simple registration, and a genuine scope concern. As of 20 May 2026, MeteoGate onboarding completed and whitelisting was removed; the composites are now openly accessible with anonymous S3 and a rate-limited anonymous API. No exception handling is needed — this is a clean-fit entry.
- **HVD basis.** European weather radar is an EU High-Value Dataset under Implementing Regulation (EU) 2023/138 and the Open Data Directive (EU) 2019/1024; the ORD API was built under RODEO to satisfy it. This is the same regulatory basis as the IMGW-PIB (Poland) entries.

---

## Notes
- **The other two ORD data families** (documented here rather than as separate entries, for users who want less or more than the Pan-European composite):
  - **Single-site volume radar data** — per-radar polar volumes: unfiltered reflectivity (`TH`), best-possible reflectivity (`DBZH`), radial velocity (`VRADH`). ODIM BUFR (older) and HDF5 (recent); 24-hour cache, archive TBD. No dual-polarisation variables yet (planned from ~2027). Scan strategies and processing are heterogeneous between members, and `VRADH` is not consistently dealiased. CC BY 4.0 **with per-provider exceptions noted in the metadata**. Query by `location_id` (e.g. `0-578-0-nohur` for Hurum, Norway), `method` = `scan`; radar codes are in the OPERA `OPERA_RADARS.csv`. Under S3: `s3://openradar-24h/YYYY/MM/DD/<CC>/<radar>/PVOL/`.
  - **National radar products** — links to national composites, rain-rate, accumulation, and echo-top products, provided as 24-hour download links to the national interfaces (HDF5 ODIM or CoG). Currently: FMI precipitation accumulations (1/3/6/24 h, CoG), KNMI 2D and 3D reflectivity composites (HDF5), Met Norway national reflectivity composites (CoG). For bulk or long time series, use the respective NMS's own data interface.
  - **OPERA Database** — a manually maintained radar-station inventory table (JSON, XLSX, CSV), updated at least twice a year.
- **Production lineage.** ODYSSEY (2012 – Oct 2024) → CIRRUS (maximum reflectivity, 1 km / 5 min, since July 2024) and NIMBUS (rain rate and accumulation, since July 2024). Worth noting that OPERA reflectivity appears across this catalog as a data-assimilation input — e.g. ALADIN-HR (Croatia) and CHMI-LAM assimilate OPERA reflectivity from the NIMBUS/ODYSSEY lines.
- **Data feed vs viewer.** The EUMETNET "OPERA radar animation" web loop is a rendered viewer, not the data feed; the gridded product is the ORD API / S3 feed above.
- **ORD serves a subset of members.** Because not all OPERA members share via ORD (non-EU members outside the HVD scope; some EU members using national interfaces), single-site coverage through ORD is a subset — though the composite itself still spans Europe.

---

## Recent version history
- **20 May 2026 — ORD API onboarded to MeteoGate; IP whitelisting removed.** Access is now open (anonymous S3, rate-limited anonymous API with free keys for higher limits). Access endpoints and the MQTT notification port changed with this transition.
- **July 2024 — new composite production lines.** CIRRUS (maximum reflectivity, 1 km / 5 min) and NIMBUS (rain rate, accumulation) began, succeeding ODYSSEY.
- **October 2024 — ODYSSEY production ended** (operational since 2011/2012).
- **2012 — Pan-European composite archive begins.**

---

## Official documentation
- OPERA / weather radar network: https://www.eumetnet.eu/observations/weather-radar-network/
- Open Radar Data documentation: https://eumetnet.github.io/openradardata-documentation/
- ORD API (MeteoGate) Swagger UI: https://api.meteogate.eu/eu-eumetnet-weather-radar/docs
- ORD S3 24-hour cache: https://s3.waw3-1.cloudferro.com/openradar-24h/
- HVD compliance summary: https://eumetnet.github.io/openradardata-documentation/ORD_HVD_summary/
- RODEO project: https://rodeo-project.eu/
- Huuskonen et al. (2014), *The Operational Weather Radar Network in Europe*, BAMS: https://doi.org/10.1175/BAMS-D-12-00216.1
- Saltikoff et al. (2019), OPERA quality control / compositing, Atmosphere: https://www.mdpi.com/478188
