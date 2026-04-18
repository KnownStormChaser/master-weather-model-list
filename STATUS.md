# Model Status Tracker

This page tracks major lifecycle events for models documented in this repository — upcoming implementations, scheduled retirements, experimental systems, and version upgrades.

The goal is to give users a single place to check "what is changing" without having to browse individual model entries. Each item links to the relevant entry where detailed information lives.

Last updated: April 2026.

---

## Imminent or recent changes

### NBM v5.0 — operational April 15, 2026
Major upgrade to the National Blend of Models with longer hourly guidance (36 h → 48 h), new products, removed Haines Index, and new inputs including ECAIFS and AIGFS.
- **Entry:** [NBM](./models/nwp_models/regional/usa/nbm.md)
- **Authority:** NWS SCN 26-24
- **Verification note:** Implementation date was April 15, 2026; verify cutover completed as scheduled if no Critical Weather Day delay was declared.

### IFS Cycle 50r1, AIFS Single v2, AIFS ENS v2 — scheduled May 12, 2026
ECMWF's physics-based and AI forecast lines upgrade on the same day. 50r1 brings NEMO4-SI3 ocean/sea-ice coupling and physics improvements with no grid change. AIFS v2 adds a 10 hPa pressure level, and AIFS ENS v2 adds a new wave stream.
- **Entries:** [IFS Open Data](./models/nwp_models/global/eu/ifs-open-data.md) · [ECMWF EPS](./models/ensemble_models/global/eu/ecmwf-eps.md) · [AIFS Single](./models/nwp_models/global/eu/aifs-single.md) · [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)
- **Authority:** ECMWF Implementation pages

### RRFSv1 and REFS — targeted early 2026 (delayed from initial target)
NOAA's next-generation convection-allowing system for North America. Delayed from the original "early 2026" target due to the government shutdown. Pre-operational as of April 2026.
- **Entries:** [RRFS](./models/nwp_models/regional/usa/rrfs.md) · [REFS](./models/ensemble_models/regional/usa/refs.md)
- **Authority:** NWS PNS 25-41

---

## Scheduled retirements (first wave — with RRFSv1)

These systems are covered under NWS Public Information Statement 25-41 (June 26, 2025) and are scheduled for retirement once RRFSv1 is fully operational.

### [NAM](./models/nwp_models/regional/usa/nam.md)
Full retirement including the 12 km parent domain and all 3 km nests (CONUS, AK, HI, PR, fire weather).

### [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md)
Retirement covers all convection-allowing nested domains.

### [HREF](./models/ensemble_models/regional/usa/href.md)
Full replacement by REFS, which extends forecasts from 48 h to 60 h.

### HiresW
   All HiresW domains retired except Guam. See [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) for the surviving domain.

### NARRE (not in repo)
Full retirement; replaced by REFS.

---

## Expected future retirements (second wave — with RRFSv2)

These systems are expected to be retired as part of the later RRFSv2 transition (MPAS dynamical core). No formal retirement notification has been issued as of April 2026.

### [HRRR](./models/nwp_models/regional/usa/hrrr.md)
Expected retirement with RRFSv2.

### [RAP](./models/nwp_models/regional/usa/rap.md)
Expected retirement alongside HRRR.

### SREF (not in repo)
SREF has already lost its largest downstream consumer — NBM v5.0 eliminated SREF as an input in April 2026. Expected retirement with RRFSv2.

### NAM 12 km
Parent domain expected to be retired alongside HRRR/RAP.

---

## Experimental and pre-operational systems

### [GDPS-SN (ECCC)](./models/nwp_models/global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn)
Experimental hybrid physics–AI configuration of GDPS. GEM model spectrally nudged toward ECCC's GEML AI model at large scales. Distributed as experimental data via MSC GeoMet and MSC Datamart. The April 2026 CMC H100 GPU supercomputer upgrade is the enabling infrastructure for eventual operationalization.

### [AIGFS (NOAA)](./models/nwp_models/global/usa/aigfs.md)
Experimental AI global deterministic model. Not a replacement for GFS.

### [AIGEFS (NOAA)](./models/ensemble_models/global/usa/aigefs.md)
Experimental AI global ensemble model. Not a replacement for GEFS.

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

**Scheduled retirements** are events with authoritative NWS/ECMWF/ECCC notifications confirming the change. These are locked in pending operational implementation.

**Expected future retirements** are events that are publicly signaled by operators (through roadmap documents, press releases, or downstream product announcements) but have not yet received formal retirement notifications. These are directionally certain but lack specific dates.

**Experimental and pre-operational systems** exist today but are not the operational primary system for their role. They may become operational, be superseded, or remain experimental indefinitely.

**Format and distribution changes** are events that don't change the underlying model but do change how users consume it.

---

## Contributing

If you notice a missing status change — a new SCN, a retirement notification, an operational implementation date — please add it to the appropriate section above with a link to the authoritative source.
