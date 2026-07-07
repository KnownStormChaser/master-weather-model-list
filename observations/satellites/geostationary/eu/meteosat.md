# Meteosat (MSG and MTG)

## What this is
The Meteosat series is EUMETSAT's family of geostationary weather satellites covering Europe, Africa, the Atlantic and Indian Oceans, and the Middle East — providing imagery used by national meteorological services across more than 30 European member states and beyond. As of 2026, six satellites are operational across two generations:

- **Meteosat Third Generation (MTG)** is in its initial deployment phase, with **Meteosat-12** (MTG-I1) providing the prime 0° service since December 2024 and **MTG-S1** (the first sounder, to be renamed Meteosat-13) launched in July 2025 and currently in commissioning.
- **Meteosat Second Generation (MSG)** continues to operate four spacecraft — Meteosat-9 (Indian Ocean Data Coverage at 45.5°E), Meteosat-10 (backup at 0°), and Meteosat-11 (Rapid Scanning Service at 9.5°E) — providing service continuity during the multi-year MTG transition.

The first MSG-to-MTG handover happened on 4 December 2024, when Meteosat-12 took over the prime 0° service from Meteosat-10. Full MTG deployment will continue through the late 2020s with MTG-I2 (launching 2026), MTG-S2, MTG-I3, and MTG-I4. The series is planned to operate into the 2040s.

EUMETSAT data is genuinely open but requires registration for the EUMETSAT Data Store — a different access tier than the no-registration AWS S3 buckets used by NOAA and the NOAA-distributed Himawari data.

---

## Who operates it
- **Operator:** EUMETSAT (European Organisation for the Exploitation of Meteorological Satellites)
- **Country / region:** Multinational European (EUMETSAT has 30 member states; headquartered in Darmstadt, Germany)
- **Spacecraft built by:** Various European industrial consortia. MSG: prime contractor Thales Alenia Space (formerly Alcatel Space). MTG: prime contractor Thales Alenia Space; OHB System for the platforms.
- **Programme partners:** ESA (European Space Agency) for development; EUMETSAT for operations

---

## Constellation status

| Satellite | Generation | Role | Position | Notes |
|---|---|---|---|---|
| Meteosat-12 | MTG (MTG-I1) | Prime 0° service | 0° | Operational since 4 December 2024; replaced Meteosat-10 in prime role |
| MTG-S1 | MTG (sounder) | Commissioning | 0° (planned) | Launched 1 July 2025; will be renamed Meteosat-13; first European GEO atmospheric sounder |
| Meteosat-9 | MSG | Indian Ocean Data Coverage (IODC) | 45.5°E | Took over IODC on 1 June 2022; covers Indian Ocean, eastern Africa, Middle East, Central Asia |
| Meteosat-10 | MSG | Backup at 0°, Data Collection Service | 0° | Provided prime service Mar 2021 – Dec 2024; now ready backup for Meteosat-12 |
| Meteosat-11 | MSG | Rapid Scanning Service (RSS) | 9.5°E | Scans northern third of the disc every 5 minutes; can support prime service |

The Meteosat constellation effectively provides three operationally distinct services from geostationary orbit:

- **Prime 0° service** — full Earth disc imagery covering Europe, Africa, the Atlantic, and parts of South America. Currently delivered by Meteosat-12 (MTG) every 10 minutes.
- **Rapid Scanning Service (RSS)** — high-frequency imagery of Europe and North Africa at 5-minute intervals from Meteosat-11 (MSG) at 9.5°E. RSS supports nowcasting of rapidly developing severe weather.
- **Indian Ocean Data Coverage (IODC)** — coverage of the Indian Ocean basin, eastern Africa, Middle East, and Central Asia from Meteosat-9 (MSG) at 45.5°E. Critical for tropical cyclone monitoring and contributes to the Indian Ocean Tsunami Warning System.

---

## Instruments

### MTG instruments (Meteosat-12, MTG-S1, future MTGs)

#### FCI (Flexible Combined Imager) — on MTG-I satellites
- **Type:** Multispectral imager
- **Channels:** 16 (8 visible/near-IR, 8 IR)
- **Spatial resolution:** 0.5 km (HRV channels), 1 km (other VNIR), 2 km (IR)
- **Temporal resolution:** Full disk every 10 minutes; rapid-scan high-resolution imagery every 2.5 minutes over Europe (RSS mode, available once MTG-I2 launches)
- **Compared to SEVIRI:** Twice the spatial resolution, 4× the channels, 30% faster repeat cycle

#### LI (Lightning Imager) — on MTG-I satellites
- **Type:** Optical lightning detector
- **Coverage:** Total lightning (cloud-to-cloud, cloud-to-ground, intracloud) across more than 86% of the visible Earth disc
- **Cameras:** Four optical cameras
- **Significance:** First lightning detection from European geostationary orbit

#### IRS (Infrared Sounder) — on MTG-S satellites
- **Type:** Hyperspectral infrared sounder
- **Spectral coverage:** 700–1210 cm⁻¹ and 1600–2175 cm⁻¹ (~1960 channels)
- **Spatial resolution:** 4 km at sub-satellite point
- **Use:** Vertical temperature and humidity profiles every 30 minutes — first European geostationary atmospheric sounder

#### Sentinel-4 / UVN — on MTG-S satellites
- **Type:** UV-visible-near-IR spectrometer (Copernicus contribution)
- **Use:** Atmospheric composition (NO₂, SO₂, ozone, formaldehyde, aerosols) over Europe at hourly cadence
- **Significance:** First Copernicus Sentinel mission flown on a geostationary satellite

### MSG instruments (Meteosat-9, -10, -11)

#### SEVIRI (Spinning Enhanced Visible and InfraRed Imager)
- **Type:** Multispectral imager
- **Channels:** 12 (1 high-resolution visible HRV, 3 visible/near-IR, 8 IR)
- **Spatial resolution:** 1 km (HRV), 3 km (other channels)
- **Temporal resolution:** Full disk every 15 minutes (prime service); northern third every 5 minutes (RSS)
- **Significance:** The workhorse imager of European weather forecasting since 2004

#### GERB (Geostationary Earth Radiation Budget)
- **Type:** Broadband scanning radiometer
- **Use:** Earth radiation budget — reflected shortwave and emitted longwave fluxes
- **On:** Meteosat-9, -10, -11

---

## Data products
- **Level 1.5 / Level 1c:** Calibrated, geometrically corrected radiances (SEVIRI, FCI)
- **Level 2 (operational meteorological products):**
  - Atmospheric Motion Vectors (AMV)
  - Cloud Mask, Cloud Type, Cloud Top Height
  - Land Surface Temperature, Sea Surface Temperature
  - Active Fire Monitoring
  - Volcanic ash, dust detection
  - Total precipitable water, ozone
  - Stability indices for nowcasting
- **Specialty products:** Lightning detection (LI), atmospheric soundings (IRS, once MTG-S1 commissioning completes), atmospheric composition (Sentinel-4)
- **MSG GERB data:** Earth radiation budget products
- **Long-term data records:** Climate-quality reprocessed datasets for MSG era (2004–present), maintained at EUMETSAT's data preservation facility

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Access tier:** Open with registration (free EUMETSAT user account required)
- **Data formats:** NetCDF (MTG, more recent products); native MSG formats and HRIT/LRIT for legacy SEVIRI products; BUFR for some Level 2 products
- **Primary access:**
  - **EUMETSAT Data Store:** https://data.eumetsat.int/ — primary catalog and download interface for all current and historical Meteosat data
  - **EUMETCast:** Direct broadcast service via DVB-S satellite distribution (requires receiving equipment)
  - **EUMETView:** Web-based image viewer at https://view.eumetsat.int/
- **Data Tailor service:** Allows server-side regridding, reprojection, and format conversion before download
- **Archive depth:** MFG (Meteosat-2 through -7): 1977–2017 (historical, available); MSG: 2004–present; MTG: from December 2022 onward
- **Licence:** EUMETSAT Data Policy — free for all uses including commercial, with attribution

---

## Successor
The MTG programme will continue with MTG-I2 (planned launch 2026, becoming Meteosat-14 after commissioning), MTG-I3 (around 2033, becoming Meteosat-15), MTG-S2 (around 10 years after MTG-S1, becoming Meteosat-16), and MTG-I4 (around 10 years after MTG-I3, becoming Meteosat-17). The full constellation is designed to operate into the 2040s and 2050s.

The transition from MSG to MTG is a multi-year process — MSG satellites will continue providing operational services in supporting roles (IODC, RSS, backup) for several more years even as MTG takes over the prime role.

---

## Notes
- **Registration is real but light.** Creating an EUMETSAT Data Store account takes minutes and asks for institutional affiliation, but there's no approval gate and no usage restrictions for openly distributed products. The bar is meaningfully lower than, for example, MOSDAC (India), but higher than the no-account-needed AWS access for GOES, Himawari, and JPSS.
- **EUMETCast is the operational data delivery method.** While the Data Store is the primary online channel for users worldwide, EUMETCast — DVB-S satellite broadcast — is how operational meteorological services in Europe and many partner countries actually receive Meteosat data in near real time. This is the same pattern used historically for direct broadcast from POES.
- **The MTG-S1 / Sentinel-4 dual identity.** MTG-S1 hosts both the EUMETSAT Infrared Sounder (IRS) and the EU's Copernicus Sentinel-4 atmospheric composition instrument (UVN). Data from the two instruments is distributed through different channels — IRS via the EUMETSAT Data Store, Sentinel-4 via the Copernicus Atmosphere Monitoring Service (CAMS) infrastructure.
- **Lightning Imager is genuinely new for European GEO.** GOES has had GLM since GOES-16 (2017); Meteosat is getting equivalent capability for the first time with MTG.
- **For decoded imagery without registration:** EUMETView (https://view.eumetsat.int/), the EUMETSAT image gallery, and various third-party viewers (Sat24, KachelmannWetter, MeteoFrance) provide quicklook imagery that doesn't require a Data Store account. These are useful for viewing but not for raw data analysis.
- **Historical Meteosat First Generation (MFG)** — Meteosat-1 through Meteosat-7 (1977–2017) are all retired but their data remains available through the EUMETSAT Data Store as an important climate data record. Meteosat-7 was the longest-serving MFG satellite, providing the IODC service from 2006 until being replaced by Meteosat-8 in 2017.

---

## Official documentation
- EUMETSAT homepage: https://www.eumetsat.int/
- Meteosat series overview: https://www.eumetsat.int/our-satellites/meteosat-series
- MTG programme: https://www.eumetsat.int/meteosat-third-generation
- MSG services: https://www.eumetsat.int/msg-services
- EUMETSAT Data Store: https://data.eumetsat.int/
- User Portal: https://user.eumetsat.int/
- EUMETView image viewer: https://view.eumetsat.int/
- WMO OSCAR (instrument specifications): https://space.oscar.wmo.int/satellites
