# MetOp (First and Second Generation)

## What this is
MetOp (Meteorological Operational satellites) is EUMETSAT's series of polar-orbiting weather satellites in the mid-morning sun-synchronous orbit, providing global atmospheric, oceanic, and land observations that complement the afternoon-orbit JPSS constellation. Together, MetOp and JPSS form the **Initial Joint Polar System (IJPS)** — a long-standing US/European agreement under which EUMETSAT covers the morning orbit and NOAA covers the afternoon orbit, with reciprocal data exchange.

As of 2026, MetOp is in transition between two generations:

- **First Generation (MetOp-A/B/C):** MetOp-B (operational since 2012) and MetOp-C (operational since 2018) continue to provide the primary morning-orbit service. MetOp-A was retired in November 2021 after 15 years on orbit.
- **Second Generation (MetOp-SG / EPS-SG):** MetOp-SGA1 launched on 13 August 2025 and is currently in commissioning, with operational status expected in 2026. Five more MetOp-SG satellites — three MetOp-SGA imaging/sounding satellites and three MetOp-SGB microwave/radar satellites — are scheduled across the late 2020s, 2030s, and 2040s.

MetOp's data — particularly IASI hyperspectral infrared soundings, ASCAT scatterometer winds, and GRAS GPS radio occultation profiles — are among the most operationally impactful satellite observations assimilated into global numerical weather prediction worldwide. ECMWF and other major NWP centres rate IASI as one of the highest-impact single observation types in their data assimilation systems.

EUMETSAT data is open with free registration via the EUMETSAT Data Store — the same access tier as Meteosat.

---

## Who operates it
- **Operator:** EUMETSAT
- **Country / region:** Multinational European (EUMETSAT has 30 member states; headquartered in Darmstadt, Germany)
- **Spacecraft built by:** Astrium / Airbus Defence and Space (MetOp first generation); Airbus Defence and Space (MetOp-SG)
- **Programme partners:** ESA for development; CNES contributes IASI/IASI-NG; NOAA contributes instruments under IJPS arrangement

---

## Constellation status

| Satellite | Generation | Role | Status | Notes |
|---|---|---|---|---|
| MetOp-SGA1 | Second | Commissioning | Launched 13 August 2025 | First MetOp-SG; carries Sentinel-5; operational status expected 2026 |
| MetOp-SGB1 | Second | Pre-launch | Planned 2026 | Microwave imaging and scatterometer |
| MetOp-C | First | Operational | Launched 7 November 2018 | Mid-morning orbit |
| MetOp-B | First | Operational | Launched 17 September 2012 | Mid-morning orbit; primary first-generation operational satellite |
| MetOp-A | First | Retired | Operational 2006–2021 | Decommissioned 30 November 2021 after 15 years |

All MetOp satellites fly in sun-synchronous orbits at approximately 817–848 km altitude, crossing the equator at 09:30 local solar time (descending node). The 09:30 local time is offset from JPSS's 13:30 local time by 4 hours, providing complementary global coverage with morning observations from MetOp and afternoon observations from JPSS.

---

## Instruments

### MetOp First Generation (MetOp-B, MetOp-C)

#### IASI (Infrared Atmospheric Sounding Interferometer)
- **Type:** Hyperspectral infrared Fourier transform spectrometer
- **Channels:** 8,461 spectral channels
- **Spectral range:** 3.62 to 15.5 µm
- **Spatial resolution:** ~12 km at nadir
- **Use:** Vertical temperature and humidity profiles; trace gas retrievals (CO, O₃, CH₄, N₂O, SO₂, HNO₃); high-impact assimilation in NWP

#### ASCAT (Advanced Scatterometer)
- **Type:** C-band radar scatterometer
- **Spatial resolution:** 12.5 km and 25 km swath products
- **Coverage:** Two 550 km swaths
- **Use:** Sea surface wind speed and direction; soil moisture; sea ice

#### GRAS (GNSS Receiver for Atmospheric Sounding)
- **Type:** GPS radio occultation receiver
- **Use:** High-precision atmospheric refractivity profiles; assimilated as a key bias-anchor observation in NWP

#### AVHRR/3 (Advanced Very High Resolution Radiometer)
- **Type:** 6-channel imaging radiometer
- **Spatial resolution:** ~1 km
- **Use:** Cloud, sea surface temperature, vegetation, snow/ice — continues the AVHRR record back to 1979

#### Other first-generation instruments
- **AMSU-A** (NOAA-supplied): Microwave temperature sounder
- **MHS** (NOAA-supplied): Microwave humidity sounder
- **HIRS/4** (on MetOp-A only, retired): Infrared sounder
- **GOME-2:** Atmospheric ozone and trace gas spectrometer
- **A-DCS / ARGOS:** Data Collection System for in-situ platforms (buoys, drifters)
- **SEM-2** (NOAA-supplied): Space Environment Monitor

### MetOp Second Generation (MetOp-SG)

The MetOp-SG payload is split between two complementary satellite types:

#### MetOp-SGA satellites (sounding/imaging)
- **METimage:** High-resolution multispectral imager (20 channels, 500 m resolution) — successor to AVHRR
- **IASI-NG:** Next-generation hyperspectral infrared sounder (twice the spectral resolution and signal-to-noise of IASI)
- **MWS (Microwave Sounder):** Successor to AMSU-A and MHS combined, with twice the spatial resolution
- **3MI (Multi-viewing, Multi-channel, Multi-polarization Imager):** Aerosol characterization with polarimetric capability
- **RO (Radio Occultation):** Successor to GRAS
- **Sentinel-5 (UVNS):** Copernicus atmospheric composition spectrometer — trace gases, air quality

#### MetOp-SGB satellites (microwave imaging/scatterometry)
- **MWI (Microwave Imager):** Conical-scan microwave imager for precipitation, cloud, surface
- **ICI (Ice Cloud Imager):** Sub-millimetre/microwave imager for ice cloud microphysics
- **SCA (Scatterometer):** Successor to ASCAT, improved resolution
- **A-DCS / ARGOS-4:** Continues the Data Collection System

---

## Data products
- **Level 1B/1C:** Calibrated, navigated radiances and brightness temperatures
- **Level 2:**
  - IASI: Atmospheric temperature/humidity profiles, ozone, CO, CH₄, N₂O, surface temperature, cloud properties
  - ASCAT: Sea surface wind vectors, soil moisture, sea ice extent
  - GRAS: Bending angle and refractivity profiles
  - AVHRR: Cloud, SST, vegetation indices, fires
  - GOME-2: Ozone profiles, NO₂, SO₂, BrO, formaldehyde, aerosols
- **Near-real-time data:** Available within ~70 minutes of observation via EUMETCast for operational NWP use
- **Climate data records:** Long-term reprocessed datasets maintained for IASI, ASCAT, GOME-2, AVHRR

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Access tier:** Open with registration (free EUMETSAT user account required)
- **Data formats:** NetCDF (more recent products); native EPS formats; BUFR for some Level 2 products
- **Primary access:**
  - **EUMETSAT Data Store:** https://data.eumetsat.int/
  - **EUMETCast:** Operational near-real-time DVB-S broadcast (requires receiving equipment)
  - **NOAA mirroring:** Some MetOp products are also redistributed via NOAA CLASS under the IJPS agreement
- **Data Tailor service:** Server-side regridding, subsetting, and format conversion
- **Archive depth:** MetOp-A: 2006–2021; MetOp-B: 2012–present; MetOp-C: 2018–present; MetOp-SGA1: from commissioning (expected 2026)
- **Licence:** EUMETSAT Data Policy — free for all uses including commercial, with attribution

---

## Successor
The full MetOp-SG programme comprises six satellites in three pairs (A1/B1, A2/B2, A3/B3), each pair providing approximately 7.5 years of operational service. Total programme coverage extends into the late 2040s. The IJPS arrangement with NOAA continues, with EUMETSAT covering the morning orbit and NOAA's JPSS-3, JPSS-4 (NOAA-22), and follow-ons covering the afternoon orbit.

A planned tandem mission between MetOp-SGA1 and MetOp-C will allow cross-calibration between the first- and second-generation instruments before MetOp-C is eventually retired.

---

## Notes
- **IASI is genuinely high-impact.** ECMWF impact studies have repeatedly shown IASI to be among the highest-impact single observation types for global NWP — comparable to or exceeding individual radiosonde networks. This makes MetOp's data assimilation contribution to operational NWP disproportionate to the satellite count.
- **ASCAT scatterometer winds are operationally critical for marine forecasting.** The 12.5 km / 25 km wind vector products are widely used in tropical cyclone analysis, marine warnings, and ocean wave model forcing.
- **The IJPS arrangement is the most successful long-running US/European satellite cooperation.** Morning orbit (MetOp) plus afternoon orbit (JPSS) plus reciprocal data sharing has provided continuous global polar coverage since the 1970s under various predecessor agreements. MetOp-SG and JPSS-4/follow-ons continue this through the 2040s.
- **Direct readout (DR/DB).** MetOp satellites broadcast continuously via X-band high-rate data; many countries operate local receiving stations for low-latency regional applications. This complements the centralized EUMETCast distribution.
- **Sentinel-5 on MetOp-SGA.** Like Sentinel-4 on MTG-S, Sentinel-5 is a Copernicus instrument (EU programme) flown on an EUMETSAT satellite — the data is distributed through the Copernicus Atmosphere Monitoring Service rather than the EUMETSAT Data Store, despite being on the same spacecraft.
- **"MetOp" vs "Metop" capitalization.** EUMETSAT now uses "Metop" (lowercase 'o') in branding; older documentation and many third-party sources still use "MetOp." Both refer to the same satellites.

---

## Official documentation
- EUMETSAT homepage: https://www.eumetsat.int/
- MetOp series: https://www.eumetsat.int/our-satellites/metop-series
- MetOp Second Generation: https://www.eumetsat.int/metop-sg
- IASI: https://www.eumetsat.int/our-satellites/metop-series/iasi
- EUMETSAT Data Store: https://data.eumetsat.int/
- User Portal: https://user.eumetsat.int/
- IJPS programme: https://www.eumetsat.int/eumetsat-polar-system
- WMO OSCAR (instrument specifications): https://space.oscar.wmo.int/satellites
