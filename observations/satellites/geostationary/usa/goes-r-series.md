# GOES-R Series (GOES-16, -17, -18, -19)

## What this is
The GOES-R Series is NOAA's current generation of geostationary weather satellites covering the Western Hemisphere. Four spacecraft are on orbit: GOES-19 (operational GOES East), GOES-18 (operational GOES West), GOES-16 (on-orbit standby), and GOES-17 (on-orbit storage at ~105°W).

The constellation provides continuous full-disk imagery every 10 minutes, CONUS sector imagery every 5 minutes, mesoscale sector imagery every 30–60 seconds, total lightning detection, and space weather monitoring. GOES-R Series data is one of the lowest-latency open weather satellite feeds available — typically appearing on AWS within 1–2 minutes of acquisition.

---

## Who operates it
- **Operator:** NOAA / NESDIS
- **Country / region:** United States
- **Spacecraft built by:** Lockheed Martin
- **Instruments built by:** Various (ABI by L3Harris, GLM by Lockheed Martin)

---

## Constellation status

| Satellite | Role | Position | Notes |
|---|---|---|---|
| GOES-19 | Operational GOES East | 75.2°W | Replaced GOES-16 on April 7, 2025 |
| GOES-18 | Operational GOES West | 137.2°W | Replaced GOES-17 on January 10, 2023 |
| GOES-16 | On-orbit standby | ~104.7°W | Drifted to storage after GOES-19 took over |
| GOES-17 | On-orbit storage | ~105°W | Offline since GOES-18 took over GOES West |

GOES East covers North/Central/South America, the Caribbean, and the Atlantic Ocean to West Africa. GOES West covers the eastern Pacific, Hawaii, Alaska, and the western contiguous United States.

---

## Instruments (per spacecraft)

### ABI (Advanced Baseline Imager)
- **Type:** Multispectral imager
- **Channels:** 16 (2 visible, 4 near-IR, 10 IR)
- **Spatial resolution:** 0.5 km (0.64 µm visible), 1 km (other VNIR), 2 km (IR)
- **Temporal resolution:** Full disk every 10 min; CONUS every 5 min; mesoscale sectors every 30–60 s

### GLM (Geostationary Lightning Mapper)
- **Type:** Optical lightning detector covering total lightning (in-cloud, cloud-to-ground, cloud-to-cloud)
- **Spatial resolution:** ~8 km
- **Temporal resolution:** Continuous

### Space weather instruments
- **SUVI** — Solar Ultraviolet Imager
- **EXIS** — Extreme Ultraviolet and X-ray Irradiance Sensors
- **SEISS** — Space Environment In-Situ Suite (energetic particles, magnetospheric)
- **Magnetometer**

---

## Data products
- **Level 1b:** Calibrated, navigated radiances for all ABI channels (NetCDF-4)
- **Level 2 (selected):** Cloud and Moisture Imagery, Cloud Top Properties, Aerosol Detection, Fire/Hot Spot Characterization, Derived Motion Winds, Sea Surface Temperature, Total Precipitable Water, Hurricane Intensity Estimation, Rainfall Rate, Volcanic Ash, Land Surface Temperature, Snow Cover
- **GLM:** Lightning events, groups, and flashes
- **SUVI:** Solar EUV imagery (NetCDF and FITS)
- **Reprocessed L1b:** A reprocessed GOES-16 ABI L1b dataset spanning early 2018 through end of 2024 is available, with improved radiometric and geometric calibration over the operational products

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Access tier:** Open S3 (no account, no registration)
- **Data formats:** NetCDF-4 (CF conventions); SUVI also in FITS
- **Primary access (AWS Open Data, all in `us-east-1`):**
  - GOES-19: `s3://noaa-goes19/` — https://noaa-goes19.s3.amazonaws.com/index.html
  - GOES-18: `s3://noaa-goes18/` — https://noaa-goes18.s3.amazonaws.com/index.html
  - GOES-17: `s3://noaa-goes17/` (archive only — no new data)
  - GOES-16: `s3://noaa-goes16/`
  - CLI example: `aws s3 ls --no-sign-request s3://noaa-goes19/`
- **New-data notifications (SNS, Lambda/SQS only):**
  - `arn:aws:sns:us-east-1:123901341784:NewGOES19Object`
  - `arn:aws:sns:us-east-1:123901341784:NewGOES18Object`
  - (and equivalent for -16/-17)
- **Other mirrors:** Google Cloud (`gs://gcp-public-data-goes-*`), Microsoft Planetary Computer
- **Full-archive search:** NOAA CLASS — https://www.aev.class.noaa.gov/
- **Archive depth:** GOES-16 from December 2017; GOES-19 from April 2025
- **Licence:** NOAA public domain — open use, attribution requested, no implied endorsement

---

## Successor
The GeoXO (Geostationary Extended Observations) program will begin replacing the GOES-R Series in the early 2030s. GeoXO is planned to introduce a hyperspectral infrared sounder, atmospheric composition instrument, and ocean color imager alongside the next-generation imager and lightning mapper.

---

## Notes
- For decoded imagery rather than raw NetCDF, RAMMB/CIRA SLIDER (https://rammb-slider.cira.colostate.edu/) and College of DuPage NEXLAB are widely used third-party services.
- The Beginner's Guide to GOES-R Series Data is a useful starting point: https://noaa-goes16.s3.amazonaws.com/Beginners_Guide_to_GOES-R_Series_Data.pdf
- Satellite role assignments rotate over time as new spacecraft commission and old ones retire — this entry should be updated when GOES-19 eventually moves to standby and GOES-T/GOES-U successors take over.

---

## Official documentation
- GOES-R Program: https://www.goes-r.gov/
- AWS Open Data registry: https://registry.opendata.aws/noaa-goes/
- NODD documentation: https://github.com/NOAA-Big-Data-Program/nodd-data-docs/tree/main/GOES
- NCEI GOES-R Series: https://www.ncei.noaa.gov/products/satellite/goes-r-series
