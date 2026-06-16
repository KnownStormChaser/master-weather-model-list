# DAFS (Domestic Aviation Forecast System)

## What this model is
The Domestic Aviation Forecast System (DAFS) is NCEP's operational aviation-hazard post-processing system. It is **not a free-standing NWP model**: it has no dynamical core of its own and runs no atmospheric simulation. Instead, the Unified Post Processor (UPP) applies NCAR's In-Flight Icing (IFI v2.0) and Graphical Turbulence Guidance (GTG v4.0) algorithms to [HRRR](./hrrr.md) forecast output to generate 3 km gridded icing and turbulence guidance. Its forecast skill is therefore inherited entirely from HRRR. DAFS was developed in response to FAA and NTSB safety recommendations and, at its March 30, 2026 implementation, upgraded operational IFI/GTG from the legacy 13 km RAP-based products to 3 km HRRR-based products.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP Central Operations; aviation algorithms developed by NCAR; aviation product responsibility under the Aviation Weather Center)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** CONUS and Alaska (IFI); CONUS only (GTG)
- **Domain details:** 3 km grids matching the corresponding HRRR domains. IFI v2.0 is produced for both the CONUS and Alaska domains; GTG v4.0 is produced for the CONUS domain only.

---

## Basic details
- **Model type:** Deterministic aviation-hazard post-processing / diagnostic system (UPP, HRRR-derived) — not an independent NWP integration
- **Model system / core:** Unified Post Processor (UPP) applying NCAR IFI v2.0 and GTG v4.0 algorithms; source model is HRRR
- **Dynamical formulation:** Not applicable — post-processor with no dynamical core; underlying dynamics are HRRR's (non-hydrostatic)
- **Convection-allowing:** Inherited from HRRR (3 km, convection-allowing)
- **Horizontal resolution:** 3 km (upgraded from the legacy 13 km RAP-based IFI/GTG)
- **Vertical levels:**
  - IFI: 60 flight levels every 500 ft from FL010 to FL300
  - GTG: 51 flight levels every 1000 ft from FL010 to FL500, plus 2D maximum-on-flight-level and 3D column-maximum products (level count per the NOMADS product description; not independently confirmed in the SCN or NCO inventory — TBD)
- **Forecast length:** 18 h
  - IFI: F001–F018
  - GTG: F000–F018
- **Update frequency / cycles:**
  - CONUS: hourly (24 cycles/day, t00z–t23z) — IFI and GTG
  - Alaska: every 3 hours (8 cycles/day: 00/03/06/09/12/15/18/21 UTC) — IFI only
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** None of its own. DAFS performs no analysis; it post-processes HRRR forecast fields. Initialization and DA are inherited from HRRR (HRRRDAS ensemble analysis over CONUS; RAP-based initialization over Alaska — see the [HRRR](./hrrr.md) entry).

---

## Source model
- **Source fields:** [HRRR](./hrrr.md) — CONUS and Alaska 3 km deterministic forecast output
- DAFS consumes HRRR output and derives aviation-hazard parameters via UPP; it does not ingest external boundary conditions.

---

## What it provides
HRRR-derived aviation-hazard guidance:

**IFI v2.0 (In-Flight Icing)** — CONUS and Alaska:
- Icing probability
- Icing severity (GRIB2 Code Table 4.228, parameter number 37, mnemonic `icesev`)
- Supercooled large droplets (SLD)

**GTG v4.0 (Graphical Turbulence Guidance)** — CONUS only:
- Clear-air turbulence (CAT)
- Mountain-wave turbulence (MWT)
- Convectively induced turbulence (CIT)
- 2D maximum turbulence on individual flight levels (max EDR; GRIB2 parameter 30, `EDPARM`)
- 3D column-maximum turbulence (column-max EDR; GRIB2 fixed surface type 10, Entire Atmosphere)

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, each file accompanied by a `.idx` index sidecar (enabling byte-range subsetting). 3 km NOMADS files use Complex3 packing with bitmap.
- **Official download location:**
  - NOMADS (HTTPS): https://nomads.ncep.noaa.gov/pub/data/nccf/com/dafs/prod/
  - FTP: ftp://ftp.ncep.noaa.gov/data/nccf/com/dafs/prod/
- **File naming:**
  - `dafs.tCCz.gtg.3km.conus.fFFF.grib2` (GTG, CONUS)
  - `dafs.tCCz.ifi.3km.conus.fFFF.grib2` (IFI, CONUS)
  - `dafs.tCCz.ifi.3km.ak.fFFF.grib2` (IFI, Alaska)

  where `CC` is the cycle hour and `FFF` is the three-digit forecast hour.
- **Approximate file sizes:** GTG CONUS ~150–170 MB/hour (~2.7 GB/cycle); IFI CONUS ~27–30 MB/hour (~0.5 GB/cycle); IFI Alaska ~24–26 MB/hour.
- **Access route notes:** NOMADS is the sole open distribution channel — no AWS Open Data / Big Data Program mirror has been announced. Retention is a short rolling window of roughly two days (two date directories kept at a time), so users needing a longer archive must capture files in near-real-time.

---

## Notes
- **It is a post-processor, not an NWP model.** DAFS is included here as a distinct, named operational system (its own `com/dafs/` production stream and SCN), analogous to how [NBM](./nbm.md) is catalogued despite running no simulation of its own. All of its forecast skill derives from [HRRR](./hrrr.md); the aviation-hazard fields it produces were historically distributed as embedded fields within RAP/HRRR output before being consolidated into a standalone system.
- **Version naming.** The *system* is DAFS v1.0, but its components carry their own versions: IFI **v2.0** and GTG **v4.0**. Stating "DAFS v1.0" without the component versions can cause confusion.
- **Replaces legacy RAP-based products.** At go-live (March 30, 2026), the previous 13 km RAP-based IFI v1.0 and GTG v3.0 were removed from NWS TGFTP. Users were directed to the 3 km DAFS GRIB2 files on NOMADS.
- **NBM-hosted IFI discontinued.** The older AWC IFI product that was carried inside NBM core blend files is being removed (per SCN 26-09); the replacement is DAFS, not a new NBM field.
- **Asymmetric coverage and cadence.** IFI covers CONUS (hourly) and Alaska (3-hourly: 00/03/06/09/12/15/18/21 UTC); GTG covers CONUS only (hourly). The entry should not be read as implying uniform domain/cadence across both product families.
- **Source-document discrepancy.** The plain-language NOMADS product description swaps the two forecast-hour ranges (it lists IFI as f000–f018 and GTG as f001–f018) and reads as though GTG covers Alaska. The verified ranges — IFI F001–F018 (CONUS + AK) and GTG F000–F018 (CONUS only) — are confirmed by the SCN, the NCEP NCO product inventory, and the live NOMADS file listing; the values above follow those.

---

## Recent version history

### DAFS v1.0 — operational March 30, 2026 (current)
First operational implementation (NWS SCN 25-80; implementation date amended to March 30, 2026 in the updated notice). Introduced 3 km HRRR-based IFI v2.0 and GTG v4.0, replacing the legacy 13 km RAP-based IFI v1.0 and GTG v3.0. Headline science changes:
- **IFI v2.0:** explicit Liquid Water Content replaces Total Water Content for the core icing calculation (cloud-boundary identification still uses TWC); explicit model supercooled rain is treated as implying SLD.
- **GTG v4.0:** improved low-level turbulence diagnostic; updated and expanded CAT and MWT diagnostics; new convectively induced turbulence (CIT) parameter; diagnostics/calibrations distinguished by layer (PBL stable/unstable, troposphere, stratosphere).

---

## Official documentation
- NWS Service Change Notice 25-80 (updated): https://www.weather.gov/media/notification/pdf_2026/scn25-80_Update_DAFS_aad.pdf
- NCEP NCO DAFS product inventory: https://www.nco.ncep.noaa.gov/pmb/products/dafs/
- NOAA story — aviation forecast improvements: https://www.noaa.gov/stories/noaa-improves-aviation-forecasts-to-bolster-us-air-travel-efficiency-safety
- UCAR/NCAR news — new aviation weather system: https://news.ucar.edu/133066/new-aviation-weather-system-improves-us-air-travel-efficiency-and-safety
