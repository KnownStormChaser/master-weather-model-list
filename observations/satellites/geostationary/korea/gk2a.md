# GEO-KOMPSAT-2A (GK-2A)

## What this is
GEO-KOMPSAT-2A (GK-2A) is a South Korean geostationary meteorological satellite operated by the Korea Meteorological Administration (KMA) at 128.2°E, providing continuous coverage of the Asia-Pacific region — the Korean Peninsula, East and Southeast Asia, Australia, and the western Pacific. It carries two payloads: the Advanced Meteorological Imager (AMI), a 16-channel imager functionally comparable to NOAA's ABI and JMA's AHI, and the Korea Space wEather Monitor (KSEM), a space-environment instrument suite. GK-2A took over the meteorological mission of the earlier COMS satellite.

Launched on 5 December 2018 and operational since 25 July 2019, GK-2A provides full-disk imagery every 10 minutes and rapid-scan regional imagery every 2 minutes. KMA produces and manages the data; NOAA redistributes a subset openly via AWS under the NOAA Open Data Dissemination (NODD) programme, in coordination with KMA — making GK-2A one of the few non-American geostationary weather satellites with no-account S3 access, alongside JMA's Himawari.

---

## Who operates it
- **Operator:** Korea Meteorological Administration (KMA) / National Meteorological Satellite Center (NMSC)
- **Country / region:** South Korea
- **Data distributor (international):** NOAA (via AWS Open Data / NODD, in coordination with KMA)
- **Satellite operation / control:** Korea Aerospace Research Institute (KARI), Daejeon
- **Spacecraft built by:** KARI (with international partners)

---

## Constellation status

| Satellite | Role | Position / Orbit | Notes |
|---|---|---|---|
| GEO-KOMPSAT-2A (GK-2A) | Operational | 128.2°E geostationary | Launched 5 Dec 2018; official service from 25 Jul 2019; 10-year design life |

GK-2A is a single operational spacecraft, not a multi-satellite series. Its sibling in the GEO-KOMPSAT-2 programme, **GEO-KOMPSAT-2B (GK-2B)**, is a separate ocean-colour and atmospheric-environment mission (GOCI-II and GEMS payloads) and is **out of scope for this weather-satellite section** — it carries no meteorological imager and is not a GK-2A backup.

Coverage spans roughly 60°N–60°S centred on 128.2°E, overlapping substantially with Himawari (140.7°E) to the east — the two provide complementary views of the Western North Pacific, the most active tropical cyclone basin globally.

---

## Instruments (per spacecraft)

### AMI (Advanced Meteorological Imager)
- **Type:** Multispectral imager (functionally analogous to ABI on GOES-R and AHI on Himawari)
- **Channels:** 16 — 4 visible/near-visible, plus 12 near-IR through thermal-IR
  - Visible/near-visible: VI004 (0.47 µm), VI005 (0.51 µm), VI006 (0.64 µm), VI008 (0.86 µm)
  - Near-IR: NR013 (1.38 µm), NR016 (1.61 µm)
  - Shortwave IR: SW038 (3.83 µm)
  - Water vapour: WV063 (6.24 µm), WV069 (6.95 µm), WV073 (7.34 µm)
  - Thermal IR: IR087 (8.59 µm), IR096 (9.63 µm), IR105 (10.40 µm), IR112 (11.21 µm), IR123 (12.36 µm), IR133 (13.31 µm)
- **Spatial resolution at nadir:** 0.5 km (VI006, 0.64 µm); 1 km (other three visible bands); 2 km (all near-IR and IR bands)
- **Temporal resolution:**
  - Full disk every 10 minutes
  - Extended Local Area (ELA, ~3800 × 2400 km) every 2 minutes
  - Local Area / target area (LA, ~1000 × 1000 km) every 2 minutes — flexibly positioned over developing severe weather, or off-Earth (e.g., Moon calibration)

### KSEM (Korea Space wEather Monitor)
- **Type:** Space-environment instrument suite (space weather)
- **Components:**
  - **Particle Detector (PD):** electrons and protons, 100 keV – 2 MeV, six view directions
  - **Magnetometer (MG / SOSMAG):** three-axis field, ±350 nT range on a 1 m deployable boom
  - **Charging Monitor (CM):** direct measurement of satellite internal charging
- **Temporal resolution:** 1-minute and 5-minute cadences (Level 1)

---

## Data products
- **AMI Level 1B:** Calibrated, navigated radiances/brightness temperatures for all 16 channels, per observation area (Full Disk, Local Area). NetCDF-4.
- **KSEM Level 1:** Particle flux (proton/electron), magnetometer, and charging-monitor data. NetCDF-4 and ASCII (`.txt`).
- **Level 2 — NOT on the AWS bucket:** The 52 AMI derived meteorological products (23 primary + 29 secondary — cloud, aerosol, fog, SST, rainfall, AMV, etc.) and the five KSEM Level 2 space-weather indices (MPF, GEF/GEP, SC, KIP, DIP) are produced by NMSC but are **only available through KMA/NMSC channels** (see Data availability → L2 access).

---

## Data availability
- **Is the data free?** Yes (L1B/L1 with no account; L2 free with registration)
- **Is the data downloadable?** Yes
- **Access tier:** Open S3 (no account, no registration) for AMI Level 1B and KSEM Level 1 via NOAA/NODD
- **Data formats:** NetCDF-4 (`.nc`) for AMI L1B and KSEM L1; KSEM L1 additionally as ASCII (`.txt`)
- **Primary access (AWS Open Data, `us-east-1`):**
  - `s3://noaa-gk2a-pds/` — browse: https://noaa-gk2a-pds.s3.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://noaa-gk2a-pds/`
  - Bucket layout (verified against the live listing):
    - `AMI/L1B/{FD,LA}/YYYYMM/DD/HH/…` — AMI L1B organized by scan area, then time
    - `GK2A/LE1B/{channel}/{area}/…` — the same AMI L1B organized by channel (all 16) then area (FD, LA, ELA, EA, KO, TP)
    - `GK2A/LV1/{CM,MG,PD-E,PD-P}-{1M,5M}/NA/data/YYYYMMDD/…` — KSEM Level 1
    - `GK2A_LA_TargetList.txt` — rapid-scan (LA) target coordinates
  - Example key: `AMI/L1B/FD/202302/16/00/gk2a_ami_le1b_ir087_fd020ge_202302160000.nc`
- **New-data notifications (SNS, Lambda/SQS protocols only):**
  - `arn:aws:sns:us-east-1:709902155096:NewGK2AObject`
- **L2 and full-archive access (KMA/NMSC, registration required):**
  - Web-based data service: http://datasvc.nmsc.kma.go.kr/datasvc/html/data/listData.do
  - Real-time FTP Service (RFS) for NMHSs, DCPC (WIS), and Open-API — all via NMSC, registration/approval required
  - NMSC portal: https://nmsc.kma.go.kr/enhome/html/base/cmm/selectPage.do?page=satellite.gk2a.intro
- **Archive depth:** AWS archive begins ~**February 2023** (earliest verified AMI FD file 2023-02-16). The satellite has been operational since July 2019, so **pre-2023 data is not on AWS** — it is available only through NMSC.
- **Licence:** Data produced and owned by KMA; NOAA distributes openly through NODD ("open to the public and can be used as desired"). NOAA requests attribution; endorsement or affiliation may not be implied; modified data may not be presented as original, unaltered NOAA data.

---

## Successor
KMA has publicly discussed a follow-on geostationary meteorological programme (a GEO-KOMPSAT-3 / next-generation line) to eventually succeed GK-2A near the end of its operational life, paralleling the international shift toward geostationary hyperspectral sounding seen in MTG-S (EUMETSAT), GeoXO (NOAA), and FY-4 IRS (China). Firm dates and payload details are **not established in the open sources verified here** — treat this as anticipated rather than confirmed.

---

## Notes
- **AWS carries L1B + KSEM L1 only, not L2.** The AWS Open Data registry describes the bucket simply as "GK2A Imagery," but the live bucket also carries the full KSEM Level 1 space-weather tree. Neither the 52 AMI derived products nor the KSEM Level 2 indices are on AWS — those remain on NMSC behind free registration. This mirrors the Himawari pattern (L1b open on AWS; L2 via the operator's own channels).
- **Two parallel AMI organizations in the bucket.** The same AMI L1B is exposed both by-area (`AMI/L1B/`) and by-channel (`GK2A/LE1B/`). The by-channel tree exposes additional area codes (ELA, EA, KO, TP) beyond the FD/LA split of the by-area tree.
- **File naming.** AMI keys follow KMA's naming rule: `gk2a_ami_le1b_<channel>_<area><res><proj>_<YYYYMMDDhhmm>.nc` (e.g., `…_ir087_fd020ge_202302160000.nc`, where `020` = 2 km resolution in 100 m units and `ge` = geostationary projection). KSEM keys follow `gk2a_ksem_<sensor>_<cadence>_le1_<YYYYMMDD>.{nc,txt}`.
- **Cross-comparability.** AMI's spectral/spatial design closely tracks GOES-R ABI and Himawari AHI, so imagery tooling and RGB recipes (true-colour, air-mass, dust, etc.) developed for those instruments are largely portable to GK-2A.
- **Space-weather context.** GK-2A at 128.2°E sits nearly opposite GOES-East (75°W); combined with GOES-West (135°W), the three extend geostationary monitoring of solar-wind dynamic pressure across the day (KMA reference: Oh et al., *J. Astron. Space Sci.* 35(3), 2018, https://doi.org/10.5140/JASS.2018.35.3.175).
- **Data feed vs viewer.** The NMSC user-customized image processing tool and web imagery are front-ends, not the downloadable feed — the raw NetCDF is the AWS bucket (L1B/L1) or the NMSC data service (all levels).

---

## Official documentation
- NMSC GK-2A introduction: https://nmsc.kma.go.kr/enhome/html/base/cmm/selectPage.do?page=satellite.gk2a.intro
- GK-2A Fact Sheet: https://nmsc.kma.go.kr/enhome/html/base/cmm/selectPage.do?page=satellite.gk2a.fact
- AWS Open Data registry: https://registry.opendata.aws/noaa-gk2a-pds/
- GK-2A L1B data user manual: http://nmsc.kma.go.kr/enhome/html/base/bbs/selectBbs.do?bbsCd=00069&bbsUsq=200021
- NMSC web-based data service: http://datasvc.nmsc.kma.go.kr/datasvc/html/data/listData.do
