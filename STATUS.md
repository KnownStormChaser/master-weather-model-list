# Model Status Tracker

This page tracks major lifecycle events for models documented in this repository — upcoming implementations, scheduled retirements, experimental systems, and version upgrades.

The goal is to give users a single place to check "what is changing" without having to browse individual model entries. Each item links to the relevant entry where detailed information lives.

For UFS-related transitions specifically, see [UFS.md](./UFS.md), which provides the full programme context. UFS items are listed here briefly; UFS.md has the detailed narrative.

Last updated: May 2026.

---

## Imminent or recent changes

### NBM v5.0 — operational May 5, 2026
Major upgrade to the National Blend of Models with longer hourly guidance (36 h → 48 h), new products, removed Haines Index, and new inputs including ECAIFS and AIGFS.
- **Entry:** [NBM](./models/nwp_models/regional/usa/nbm.md)
- **Authority:** NWS SCN 26-24 (AAC revision, April 28, 2026)
- **Verification note:** Originally targeted April 15, 2026; rescheduled to April 23, then April 30. The April 30 attempt fell within a Critical Weather Day / Enhanced Caution Event window, triggering the SCN's contingency provision and pushing actual cutover to May 5, 2026.

### IFS Cycle 50r1, AIFS Single v2, AIFS ENS v2 — scheduled May 12, 2026
ECMWF's physics-based and AI forecast lines upgrade on the same day. 50r1 brings NEMO4-SI3 ocean/sea-ice coupling and physics improvements with no grid change. AIFS v2 adds a 10 hPa pressure level, and AIFS ENS v2 adds a new wave stream.
- **Entries:** [IFS Open Data](./models/nwp_models/global/eu/ifs-open-data.md) · [ECMWF EPS](./models/ensemble_models/global/eu/ecmwf-eps.md) · [AIFS Single](./models/nwp_models/global/eu/aifs-single.md) · [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)
- **Authority:** ECMWF Implementation pages

### RRFSv1 and REFS — scheduled August 31, 2026 (12 UTC)
NOAA's next-generation convection-allowing system for North America is now formally scheduled. RRFS and REFS implement together, with legacy NAM, HREF, SREF, and HiresW (all domains except Guam) retiring on the same day. A pre-implementation real-time feed is expected on NOMADS on or about June 9, 2026. **See [UFS.md](./UFS.md) for the full UFS context including the wave of retirements that occurs on the same day.**
- **Entries:** [RRFS](./models/nwp_models/regional/usa/rrfs.md) · [REFS](./models/ensemble_models/regional/usa/refs.md)
- **Authority:** NWS SCN 26-48 (May 12, 2026); product retirements detailed in companion SCN 26-47
- **Verification note:** Originally targeted early 2026; slipped through pre-operational evaluation. The August 31 date is subject to the standard CWD/ECE contingency — if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday. NBM v5.0 was deferred under exactly this provision earlier in 2026, so the contingency is not theoretical.

### GDPS-EXP — expected to replace operational GDPS in 2026
ECCC distributes GDPS Experimental Version 9.0.9 (implemented March 5, 2025) at https://dd.weather.gc.ca/today/model_gdps/, and per ECCC's own statement at that endpoint, the experimental system is "expected to replace in 2026, the current Global Deterministic Prediction System (GDPS) data." This is a directional signal rather than a fixed implementation date — no formal Service Change Notice has been issued.

---

## Recently operational (AI weather systems)

### AIGFS, AIGEFS, HGEFS — operational December 17, 2025
NOAA's three AI-based global weather systems became operational on the same date, replacing EAGLE SOLO (deterministic) and EAGLE Ensemble. AIGFS and AIGEFS are standalone AI systems based on the GraphCast architecture; HGEFS is a "grand ensemble" combining 31 GEFS physics members with 31 AIGEFS AI members. None of the three replaces an operational physics-based system — they run alongside GFS and GEFS.
- **Entries:** [AIGFS](./models/nwp_models/global/usa/aigfs.md) · [AIGEFS](./models/ensemble_models/global/usa/aigefs.md) · [HGEFS](./models/ensemble_models/global/usa/hgefs.md)
- **Authority:** NOAA press release on AI-driven global weather models, December 2025

---

## Scheduled retirements (NWS SCN 26-48 / PNS 25-41)

These systems are scheduled for retirement on **August 31, 2026 at 12 UTC**, the same cycle that brings RRFSv1 and REFS into operations. The retirement set was originally proposed in NWS Public Information Statement 25-41 (June 26, 2025) and formally scheduled in NWS Service Change Notice 26-48 (May 12, 2026), with the detailed product retirement list in companion SCN 26-47. Subject to the standard CWD/ECE contingency. **See [UFS.md](./UFS.md) for the consolidation context.**

- [NAM](./models/nwp_models/regional/usa/nam.md) — full retirement (12 km parent and all 3 km nests)
- [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md) — all convection-allowing nests
- [HREF](./models/ensemble_models/regional/usa/href.md) — replaced by REFS (extends 48 h → 60 h)
- HiresW — CONUS, Alaska, Hawaii, and Puerto Rico domains; see [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) for the surviving Guam exception
- SREF (not in repo) — replaced by REFS; the SCN-confirmed retirement supersedes the earlier expectation that SREF would persist into the second wave under RRFSv2
- NARRE (not in repo) — replaced by REFS
- NAM MOS (not in repo) — retired alongside NAM

---

## Expected future retirements

These systems are publicly signaled but have **no formal retirement notification** as of May 2026. They are expected to be retired with RRFSv2 (MPAS-based). **See [UFS.md](./UFS.md) for the second-wave retirement context.**

- [HRRR](./models/nwp_models/regional/usa/hrrr.md) — expected with RRFSv2
- [RAP](./models/nwp_models/regional/usa/rap.md) — expected with RRFSv2
- NAM 12 km parent — expected with HRRR/RAP

---

## Experimental and pre-operational systems

### [GDPS-EXP (ECCC)](./models/nwp_models/global/canada/gdps-exp.md)
Experimental hybrid physics–AI successor candidate to operational GDPS. GEM v5.2 atmospheric model spectrally nudged toward GEML at large scales (>2750 km) in the mid-troposphere (250–850 hPa). 15 km, 84 levels, 10-day forecast 2× daily. Beyond the AI integration, v9.0.9 also brings substantial DA, sea ice, and physics updates relative to operational GDPS 9.0.0. Distributed via the standard MSC datamart at https://dd.weather.gc.ca/today/model_gdps/. Per ECCC's own statement at that endpoint, expected to replace operational GDPS in 2026 — see "Imminent or recent changes" above. Current version 9.0.9 implemented March 5, 2025.

### [GEML (ECCC)](./models/nwp_models/global/canada/gdps-geml.md)
Experimental standalone AI global deterministic weather model. Graph neural network derived from Google DeepMind's GraphCast architecture, retrained by ECCC on ERA5 reanalysis (1979–2016) and ECMWF HRES analyses (2016–2021). 0.25° (~28 km) resolution, 13 pressure levels, 10-day forecast 2× daily, initialized from GDPS analysis. Serves a dual role as both a standalone forecast product (distributed at https://dd.weather.gc.ca/today/model_gdps-geml/25km/) and the spectral nudging target for GDPS-EXP. Trained model weights are publicly released via HuggingFace at https://huggingface.co/ECCC-ASTD-MRD/geml — unusual openness compared to most operational AI weather systems.

### [CAPS (ECCC)](./models/nwp_models/regional/canada/caps.md)
Experimental coupled atmosphere-ocean-sea ice prediction system at ~3 km resolution covering a large pan-Arctic domain (northern Canada, Alaska, Greenland, Iceland, Scandinavia, Baltic countries, eastern Russia). GEM atmospheric component (configuration inherited from the decommissioned HRDPS-North) two-way coupled to NEMO v3.6 ocean and CICE v6.2.0 sea ice via the GOSSIP coupler at 5-minute exchange frequency. Provides forecast guidance to Canadian Storm Prediction Centres, DND, CCG, and DFO. Distributed via the standard MSC datamart but explicitly designated as experimental. Current version 3.0.0 implemented June 18, 2025.

### [HRDPS-West (ECCC)](./models/nwp_models/regional/canada/hrdps-west.md)
Experimental kilometric-scale (~1 km) deterministic NWP system covering Southern British Columbia. GEM v5.2.0 atmospheric model piloted by HRDPS national v7.0.0, with surface initial conditions from CaLDAS. Distributed via the MSC alpha datamart (separate from the standard operational MSC datamart). Intended to better resolve mesoscale features in BC's complex topography than the operational 2.5 km HRDPS. Current version 1.5.0 released November 6, 2024.

### [GraphCastGFS (NOAA)](./models/nwp_models/global/usa/graphcastgfs.md)
Experimental productionization of Google DeepMind's GraphCast architecture by NOAA, fine-tuned on GDAS+ERA5 data. Predecessor of the operational AIGFS, but continues to run experimentally alongside its operational descendant.

### [FourCastNetGFS (NOAA)](./models/nwp_models/global/usa/fourcastnetgfs.md)
Experimental productionization of NVIDIA's FourCastNet architecture using Spherical Fourier Neural Operators. No operational descendant announced as of April 2026.

---

## Format and distribution changes

### IFS Cycle 50r2 — tentative Q4 2026
Complete migration of ECMWF IFS to GRIB2-only parameter representation. Affects Open Data users directly. Legacy GRIB1-style parameter references move to GRIB2-native identifiers; CCSDS compression required.
- **Entries:** [IFS Open Data](./models/nwp_models/global/eu/ifs-open-data.md) · [ECMWF EPS](./models/ensemble_models/global/eu/ecmwf-eps.md)
- **Authority:** ECMWF Migration to GRIB edition 2 page

### MOGREPS-G parameter and forecast-length changes — late January 2026
Extended forecast length from 198 h to 246 h; added parameters and levels; renamed `height_asl_on_pressure_levels` to `geopotential_height_on_pressure_levels`. Already effective.
- **Entry:** [MOGREPS-G](./models/ensemble_models/global/uk/mogreps-g.md)

---

## How to read this page

**Imminent or recent changes** are events that have happened in the last few months or will happen in the next few months. These need the most active attention from catalog users.

**Recently operational (AI weather systems)** highlights AI-based forecast systems that have transitioned from experimental to operational status. Listed separately because the AI lifecycle is moving fast and is worth tracking distinctly.

**Scheduled retirements** are events with authoritative NWS/ECMWF/ECCC notifications confirming the change. These are locked in pending operational implementation.

**Expected future retirements** are events that are publicly signaled by operators (through roadmap documents, press releases, or downstream product announcements) but have not yet received formal retirement notifications. These are directionally certain but lack specific dates.

**Experimental and pre-operational systems** exist today but are not the operational primary system for their role. They may become operational, be superseded, or remain experimental indefinitely.

**Format and distribution changes** are events that don't change the underlying model but do change how users consume it.

---

## Contributing

If you notice a missing status change — a new SCN, a retirement notification, an operational implementation date — please add it to the appropriate section above with a link to the authoritative source. For UFS-related changes, please also update [UFS.md](./UFS.md) so the two files stay synchronized.
