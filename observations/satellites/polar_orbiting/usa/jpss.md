# JPSS (Joint Polar Satellite System)

## What this is
The Joint Polar Satellite System (JPSS) is NOAA's series of polar-orbiting weather satellites in the early-afternoon sun-synchronous orbit, providing twice-daily global coverage of atmospheric, oceanic, and terrestrial conditions. The constellation currently consists of three operational satellites — NOAA-21 (primary), NOAA-20 (secondary), and Suomi NPP (tertiary) — flying in a coordinated formation that delivers VIIRS imagery for most of the globe at least six times per day, with mid- and high-latitude locations sensed substantially more often through swath overlap.

JPSS observations are foundational inputs to numerical weather prediction worldwide. ATMS microwave soundings and CrIS hyperspectral infrared soundings provide the bulk of the satellite data assimilated into operational global NWP models including GFS, IFS, GEM, and others; VIIRS imagery supports nowcasting, fire detection, and aerosol monitoring. The constellation is planned to provide continuous polar coverage through 2038, with JPSS-3 and JPSS-4 (the latter to become NOAA-22 in orbit) as follow-on spacecraft.

NOAA distributes JPSS data openly via AWS through per-satellite S3 buckets, plus a separate development bucket. This is the most data-intensive of the AWS-hosted weather satellite feeds — the constellation generates several terabytes per day across all four instruments.

---

## Who operates it
- **Operator:** NOAA / NESDIS (with NASA as development partner)
- **Country / region:** United States
- **Spacecraft built by:** Ball Aerospace (Suomi NPP, NOAA-20); Northrop Grumman / Orbital ATK (NOAA-21, JPSS-3, JPSS-4)
- **International data-sharing agreements:** EUMETSAT, JAXA, and others provide reciprocal data access

---

## Constellation status

| Satellite | Role | Launched | Notes |
|---|---|---|---|
| NOAA-21 | Primary | November 10, 2022 | Promoted to primary in early 2024; quarter orbit ahead of Suomi NPP |
| NOAA-20 | Secondary | November 18, 2017 | Quarter orbit behind Suomi NPP; formerly JPSS-1 |
| Suomi NPP | Tertiary | October 28, 2011 | Originally a NASA/NOAA technology pathfinder; primary 2014–2018; 14+ years on orbit |
| JPSS-3 | Upcoming | Planned | To become NOAA-22-class follow-on |
| JPSS-4 | Upcoming | Planned 2027 | To be renamed NOAA-22 in orbit; will add the Libera energy-budget instrument |

All three operational satellites fly in the same sun-synchronous orbit at ~824 km altitude, crossing the equator at 13:30 local solar time (ascending node). The 50-minute orbital spacing between satellites — quarter-orbit phasing — gives users six VIIRS overpasses globally per day, with substantially more frequent observation at mid- and high latitudes due to swath overlap. Parts of Alaska are sensed 16–20 times per day.

---

## Instruments (per spacecraft)

All three operational satellites carry the same core four instruments. NOAA-21 omits the energy-budget instrument that flew on Suomi NPP and NOAA-20.

### VIIRS (Visible Infrared Imaging Radiometer Suite)
- **Type:** Multispectral imager
- **Channels:** 22 bands (16 moderate-resolution "M-bands", 5 imagery "I-bands", 1 Day-Night Band)
- **Spatial resolution:** 750 m (M-bands), 375 m (I-bands), 750 m (Day-Night Band)
- **Spectral range:** 0.4 to 12.5 µm
- **Swath:** ~3000 km
- **Distinctive capability:** The Day-Night Band detects very low light levels — moonlit clouds, city lights, fires, lightning, gas flares — at near-constant resolution across the swath

### CrIS (Cross-track Infrared Sounder)
- **Type:** Hyperspectral infrared sounder
- **Channels:** 2,211 spectral channels in three bands (longwave, midwave, shortwave IR)
- **Spectral range:** 3.92 to 15.38 µm
- **Spatial resolution:** ~14 km at nadir
- **Use:** Vertical temperature and water vapor profiles assimilated into operational NWP models

### ATMS (Advanced Technology Microwave Sounder)
- **Type:** Cross-track microwave radiometer
- **Channels:** 22 (temperature and humidity sounding, plus surface and precipitation channels)
- **Spectral range:** 23 to 183 GHz
- **Spatial resolution:** 16–75 km depending on channel
- **Use:** All-weather temperature and humidity sounding (sees through clouds); a primary input to global NWP data assimilation

### OMPS (Ozone Mapping and Profiler Suite)
- **Type:** UV/visible spectrometer suite
- **Components:** Nadir Mapper, Nadir Profiler, Limb Profiler (Suomi NPP and NOAA-21 only)
- **Spectral range:** 250–380 nm
- **Use:** Total column ozone, ozone profiles, sulfur dioxide, volcanic ash detection

### CERES (Clouds and Earth's Radiant Energy System)
- **On:** Suomi NPP and NOAA-20 only (not NOAA-21)
- **Type:** Broadband scanning radiometer
- **Use:** Earth radiation budget — reflected shortwave and emitted longwave fluxes

### Libera
- **On:** JPSS-4 (future)
- **Type:** Successor energy-budget instrument
- **Use:** Continues the CERES-class measurement record with improved capabilities

---

## Data products
- **Level 1 (SDR — Sensor Data Records):** Calibrated radiances from all instruments
- **Level 2 (EDR — Environmental Data Records):**
  - VIIRS: Cloud properties, sea surface temperature, land surface temperature, vegetation indices, active fires, aerosol optical depth, true-color imagery, snow cover, ice concentration, ocean color
  - CrIS / NUCAPS: Atmospheric temperature and moisture profiles, trace gases
  - ATMS: Microwave temperature and humidity sounding products
  - OMPS: Total column ozone, ozone profiles, SO₂, volcanic ash
- **Near-real-time products:** Active fire detection, flood mapping, blended hydrological products
- **Direct-broadcast data:** Available via X-band downlink to local antennas (HRD — High Rate Data)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Access tier:** Open S3 (no account, no registration)
- **Data formats:** NetCDF-4 and HDF5
- **Primary access (AWS Open Data, all in `us-east-1`):**
  - NOAA-21: `s3://noaa-nesdis-n21-pds/` — https://noaa-nesdis-n21-pds.s3.amazonaws.com/index.html
  - NOAA-20: `s3://noaa-nesdis-n20-pds/` — https://noaa-nesdis-n20-pds.s3.amazonaws.com/index.html
  - Suomi NPP: `s3://noaa-nesdis-snpp-pds/` — https://noaa-nesdis-snpp-pds.s3.amazonaws.com/index.html
  - JPSS Development bucket: `s3://noaa-jpss/` — https://noaa-jpss.s3.amazonaws.com/index.html
  - CLI example: `aws s3 ls --no-sign-request s3://noaa-nesdis-n21-pds/`
- **New-data notifications (SNS, Lambda/SQS only):**
  - `arn:aws:sns:us-east-1:709902155096:NewNOAA21Object`
  - `arn:aws:sns:us-east-1:709902155096:NewNOAA20Object`
  - `arn:aws:sns:us-east-1:709902155096:NewSNPPObject`
  - `arn:aws:sns:us-east-1:709902155096:NewJPSSObject`
- **Full-archive search:** NOAA CLASS — https://www.aev.class.noaa.gov/
- **Direct-broadcast data:** Locally received via X-band antennas; processing tools available through the Community Satellite Processing Package (CSPP) at SSEC/Wisconsin
- **Licence:** NOAA public domain — open use, attribution requested, no implied endorsement

---

## Successor
JPSS-3 and JPSS-4 are in development as direct continuations of the constellation. JPSS-4 is targeted for launch readiness in 2027 and will be renamed NOAA-22 in orbit; it will add the Libera energy-budget instrument alongside the four standard JPSS instruments. JPSS-3 follows in a similar configuration. Together, JPSS-3 and JPSS-4 are intended to extend operational continuity of the afternoon polar orbit through 2038.

The longer-term successor architecture beyond JPSS-4 has not been finalized publicly.

---

## Notes
- **Operational role rotation matters.** The primary satellite gets first-priority data processing and spacecraft maneuver scheduling. Pre-2024 operational data is in the Suomi NPP and NOAA-20 buckets; post-2024 primary data is in NOAA-21. Long-term studies should be aware that "operational primary" has shifted across all three buckets over time.
- **Sub-orbit coordination.** The quarter-orbit phasing (NOAA-21 ahead of Suomi NPP, NOAA-20 behind) is a deliberate operational design — it minimizes observation gaps and provides redundancy if any single satellite has an outage.
- **Suomi NPP longevity.** Originally designed for a 5-year mission, Suomi NPP has been operating successfully for 14+ years. JPSS-3 and JPSS-4 launch timing assumes Suomi NPP may not be available indefinitely, but as of 2026 it remains a contributing tertiary satellite.
- **VIIRS vs MODIS.** VIIRS is the operational successor to NASA's MODIS (Aqua/Terra), with broadly similar spectral coverage but improved spatial resolution, near-constant ground sampling across the swath, and a unique Day-Night Band. The two instruments are sometimes treated as a continuous record for climate applications.
- **For decoded imagery:** RAMMB/CIRA SLIDER, NASA Worldview, and the JPSS Data Explorer (https://coliveir-aer.github.io/jpss-data-explorer/) provide quicklook visualization. The NASA SPoRT VIIRS product page is particularly useful for fire and convection applications.
- **Python tooling:** The JPSS NODD GitHub organization (https://github.com/jpss-nodd) provides scripts and notebooks for downloading and working with JPSS data on AWS.

---

## Official documentation
- JPSS program: https://www.jpss.noaa.gov/
- NESDIS JPSS overview: https://www.nesdis.noaa.gov/our-satellites/currently-flying/joint-polar-satellite-system
- AWS Open Data registry: https://registry.opendata.aws/noaa-jpss/
- NODD documentation: https://github.com/NOAA-Big-Data-Program/bdp-data-docs/tree/main/JPSS
- JPSS spacecraft status: https://www.ospo.noaa.gov/operations/jpss/status.html
- JPSS training resources: https://www.jpss.noaa.gov/training_resources.html
