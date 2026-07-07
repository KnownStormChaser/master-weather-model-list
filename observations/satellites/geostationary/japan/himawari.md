# Himawari-8/9

## What this is
Himawari-8 and Himawari-9 are a pair of identical Japanese geostationary weather satellites operated by the Japan Meteorological Agency (JMA) at 140.7°E, providing continuous coverage of East Asia, the western and central Pacific, and parts of Australia and the Indian Ocean. Himawari-9 has been the operational satellite since December 13, 2022, with Himawari-8 as on-orbit backup.

The constellation provides full-disk imagery every 10 minutes, target-area imagery every 2.5 minutes, and Japan-area imagery every 2.5 minutes, with rapid-scan capability down to 30-second intervals over a 1000 km × 1000 km region. The Advanced Himawari Imager (AHI) is functionally similar to NOAA's ABI on the GOES-R series — both have 16 bands and similar resolutions — making the two systems broadly cross-comparable for hemispheric weather monitoring.

JMA produces and manages the data; NOAA redistributes it openly via AWS under an agreement with JMA, making Himawari one of the few non-American geostationary weather satellites with truly open S3 access.

---

## Who operates it
- **Operator:** Japan Meteorological Agency (JMA)
- **Country / region:** Japan
- **Data distributor (international):** NOAA (via AWS Open Data, under JMA agreement)
- **Spacecraft built by:** Mitsubishi Electric (with assistance from Boeing)
- **AHI instrument built by:** Exelis (now L3Harris)

---

## Constellation status

| Satellite | Role | Position | Notes |
|---|---|---|---|
| Himawari-9 | Operational | 140.7°E | Took over from Himawari-8 on December 13, 2022 |
| Himawari-8 | On-orbit backup | 140.7°E (co-located) | Operational July 2015 – December 2022 |

Coverage area extends from roughly 60°N to 60°S and 80°E to 160°W — covering Japan, eastern China, Korea, Southeast Asia, Australia, New Zealand, the Philippines, Indonesia, much of the Pacific Ocean, and the eastern Indian Ocean. This is the most active tropical cyclone basin globally, making Himawari operationally important for typhoon monitoring across the Western North Pacific.

---

## Instruments (per spacecraft)

### AHI (Advanced Himawari Imager)
- **Type:** Multispectral imager (functionally analogous to ABI on GOES-R)
- **Channels:** 16 (3 visible, 3 near-IR, 10 IR)
- **Spatial resolution at sub-satellite point:**
  - 0.5 km — band 3 (0.64 µm)
  - 1 km — bands 1, 2, 4 (0.47, 0.51, 0.86 µm)
  - 2 km — bands 5–16 (1.6 µm through 13.3 µm)
- **Temporal resolution:**
  - Full disk every 10 minutes
  - Japan area every 2.5 minutes
  - Target area every 2.5 minutes (typically positioned over developing tropical cyclones)
  - Landmark areas every 30 seconds (1000 km × 1000 km)

The AHI's spectral and spatial design closely matches the GOES-R ABI, which is a deliberate international design convergence — derived imagery products and algorithms are largely portable between the two systems.

---

## Data products
- **Level 1b:** Full-Disk calibrated radiances (AHI-L1b-FLDK), HSD format and NetCDF
- **Level 2:** Cloud properties, sea surface temperature, aerosol optical depth, true-color imagery, derived motion winds, and others (distributed through JMA channels rather than the AWS bucket)
- **Imagery:** Sample full-disk imagery and animations available via JMA and NOAA OSPO

The AWS bucket primarily hosts the Full-Disk L1b product. Higher-frequency target-area and rapid-scan products, plus derived L2 products, are typically obtained via JMA's HimawariCloud or CEReS at Chiba University.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Access tier:** Open S3 (no account, no registration) for L1b Full-Disk via NOAA distribution
- **Data formats:** HSD (Himawari Standard Data, native binary) and NetCDF
- **Primary access (AWS Open Data, all in `us-east-1`):**
  - Himawari-9 (operational): `s3://noaa-himawari9/` — https://noaa-himawari9.s3.amazonaws.com/index.html
  - Himawari-8 (backup): `s3://noaa-himawari8/` — https://noaa-himawari8.s3.amazonaws.com/index.html
  - CLI example: `aws s3 ls --no-sign-request s3://noaa-himawari9/`
- **New-data notifications (SNS, Lambda/SQS only):**
  - `arn:aws:sns:us-east-1:123901341784:NewHimawariNineObject`
  - `arn:aws:sns:us-east-1:123901341784:NewHimawari8Object`
- **JMA-direct access (registration required):** HimawariCloud — https://www.data.jma.go.jp/mscweb/en/himawari89/cloud_service/cloud_service.html
- **Other mirrors:**
  - CEReS at Chiba University (academic use, full archive)
  - Australian BOM (regional redistribution)
- **Archive depth:** L1b Full-Disk back to July 2015 on AWS
- **Licence:** Data produced and managed by JMA; NOAA distributes openly. NOAA and JMA request attribution; endorsement may not be implied; modified data may not be presented as original.

---

## Successor
The Himawari follow-on series is in planning by JMA, targeting launch in the late 2020s with operational service expected in the 2030s. The next generation will introduce hyperspectral infrared sounding alongside continued imager service, paralleling the international shift seen in MTG-S (EUMETSAT), GeoXO (NOAA), and FY-4 IRS (China).

---

## Notes
- The 2022 role swap from Himawari-8 to Himawari-9 was largely transparent to data users — the buckets and product structure remained the same, with `noaa-himawari9` simply becoming the primary source. Long-running pipelines should be aware that pre-December-2022 operational data is in the Himawari-8 bucket.
- The AHI/ABI design similarity means tools developed for GOES-R (e.g., `goes2go` Python package, RAMMB SLIDER) can often be adapted to Himawari with modest effort.
- For decoded imagery rather than raw HSD/NetCDF, RAMMB/CIRA SLIDER (https://rammb-slider.cira.colostate.edu/) and the Australian BOM's Himawari viewer are widely used.
- The Himawari name comes from the Japanese word for sunflower, referencing the satellite's continuous attention to the Earth disk below.

---

## Official documentation
- JMA Meteorological Satellite Center: https://www.data.jma.go.jp/mscweb/en/himawari89/
- AHI instrument specifications: https://www.data.jma.go.jp/mscweb/en/himawari89/space_segment/spsg_ahi.html
- HimawariCloud service: https://www.data.jma.go.jp/mscweb/en/himawari89/cloud_service/cloud_service.html
- AWS Open Data registry: https://registry.opendata.aws/noaa-himawari/
- NOAA OSPO Himawari imagery: https://www.ospo.noaa.gov/Products/imagery/himawari.html
- AHI on AWS technical document: https://github.com/awslabs/open-data-docs/blob/main/docs/noaa/noaa-himawari/2020July08_NOAA_Himawari.pdf
