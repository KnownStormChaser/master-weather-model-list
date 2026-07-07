# <RADAR NETWORK OR COMPOSITE PRODUCT>

## What this is
<One or two paragraphs describing the network or composite: what it observes,
its operational role, and what makes it distinct. State clearly whether this is
a single-operator national network or a multi-national composite coordinated
across member services. If the product is a blended multi-sensor analysis
(radar + gauge + lightning + model), say so here — it is not raw radar.>

---

## Who operates it
- **Operator / coordinating programme:** <Agency or programme, e.g., NOAA/NSSL; EUMETNET OPERA>
- **Country / region:** <Country or multi-national>
- **Data distributor (if different):** <e.g., distributed via AWS Open Data; via the ORD API>
- **Contributing networks / members:** <For multi-national composites: member services and total radar count. Omit for single-operator networks.>

---

## Network composition
<Frequency bands (S / C / X), polarization (single- vs dual-pol), total radar
count, and coverage domain. For a national network, name the underlying radar
network (e.g., WSR-88D / NEXRAD). For a multi-national composite, note member
countries and the heterogeneity of the contributing hardware. If the product
blends non-radar sensors (rain gauges, lightning, satellite, NWP), list those
inputs here. A per-radar or per-member table is optional — include only if that
granularity is genuinely useful; otherwise keep this prose.>

---

## Products
<This is the core section. Group by product family and, for each, note grid
resolution, projection, vertical structure (single-level composite vs 3D
mosaic), update cadence, and latency where known.>

- **Composite / mosaic products:** <e.g., reflectivity mosaic, max reflectivity (CMAX), echo top, VIL — grid resolution, projection, and whether single-level or multi-level 3D>
- **Precipitation products:** <instantaneous surface rain rate, QPE, N-hour accumulation, and any gauge- or model-adjusted variants>
- **Single-site / volume data (if distributed separately):** <base moments — horizontal reflectivity, radial velocity, spectrum width — plus dual-pol variables (ZDR, RhoHV, PhiDP, KDP); note volume vs level-II/III equivalents>
- **Derived / severe-weather diagnostics:** <rotation tracks, hail size (MESH), flash-flood guidance inputs, etc.>

---

## Data availability
- **Is the data free?** Yes / Yes (registration required) / Partial
- **Is the data downloadable?** Yes / No
- **Access tier:** <Open S3 (no account) / Open with account / Open with restrictions — e.g., IP whitelisting on request>
- **Data formats:** <GRIB2 / ODIM HDF5 / BUFR / cloud-optimized GeoTIFF / NetCDF>
- **Update cadence:** <e.g., every 2 min (MRMS) / every 15 min (OPERA composites)>
- **Primary access:**
  - <S3 bucket(s) or API endpoint, with browse URL>
  - <AWS region / CLI example, or API request note>
- **New-data notifications:** <SNS topics or equivalent, if available>
- **Other mirrors:** <Additional hosts, if any>
- **Archive depth:** <Rolling cache window and long-term archive extent>
- **Licence:** <Specific terms — e.g., public domain (U.S. government work) / CC BY 4.0. Note any exceptions carried in per-product metadata.>

---

## Scope note (optional)
<Use only when the entry carries a tension worth flagging inline, consistent with
the flag-don't-silently-resolve principle. Two recurring cases for radar:
(1) open licence but gated access (e.g., IP whitelisting = more than simple
registration); (2) transitional licensing on a known trajectory toward fully
open (e.g., EU High-Value Dataset status, RODEO project). State the current
barrier and the trigger condition for revisiting it. Omit for clean-fit,
un-gated entries.>

---

## Notes
- **Observation, not forecast.** <A one-line reminder that this is an observational/analysis product, and — for multi-sensor mosaics — that it blends more than radar.>
- **Relationship to NWP.** <Which operational models assimilate this data, if relevant — closes the loop with the DA references elsewhere in the catalog.>
- **Data feed vs viewer.** <Name any rendered animation or viewer front-end and make explicit that it is NOT the downloadable feed.>
- **Single-site vs composite.** <If both are distributed, clarify which channel serves which.>
- <Latency quirks, projection gotchas, naming conventions, QC caveats.>

---

## Recent version history (optional)
<Include if the product line has documented algorithm or processing-chain
upgrades worth tracking (e.g., ODYSSEY → NIMBUS; MRMS version bumps).
Reverse-chronological, lightweight. Otherwise omit.>

---

## Official documentation
- <Programme URL>
- <Open data registry / API documentation URL>
- <Data model / format specification URL>
- <Reference paper>
