# Model Status Tracker

This page tracks major lifecycle events for models documented in this repository — upcoming implementations, scheduled retirements, experimental systems, and version upgrades.

The goal is to give users a single place to check "what is changing" without having to browse individual model entries. Each item links to the relevant entry where detailed information lives.

For UFS-related transitions specifically, see [UFS.md](./UFS.md), which provides the full programme context. UFS items are listed here briefly; UFS.md has the detailed narrative.

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

### RRFSv1 and REFS — targeted early 2026 (delayed)
NOAA's next-generation convection-allowing system for North America. Pre-operational as of April 2026. **See [UFS.md](./UFS.md) for the full UFS context including the wave of retirements that follows.**
- **Entries:** [RRFS](./models/nwp_models/regional/usa/rrfs.md) · [REFS](./models/ensemble_models/regional/usa/refs.md)
- **Authority:** NWS PNS 25-41

### GFSv17 and GDASv17 — proposed for October 2026
NOAA/NWS published PNS 26-29 (April 15, 2026) proposing an upgrade of the operational GFS and GDAS from v16 to v17 in October 2026. A companion notice, PNS 26-30 (April 16, 2026), covers the legacy products slated for removal as part of the cutover.

This is a **proposal open for public comment**, not a scheduled implementation. Comments on the science changes are accepted through May 15, 2026; comments on product removals through May 16, 2026.

- **Entry:** [GFS](./models/nwp_models/global/usa/gfs.md)
- **Authority:** NWS PNS 26-29, PNS 26-30
- **Full UFS context:** See [UFS.md](./UFS.md) — GFSv17 is the largest single UFS transition planned and represents the move from atmosphere-centric to fully coupled Earth-system modeling.

### RTOFS Global v3.0 — proposed (March 2026)
NOAA published PNS 26-17 on March 12, 2026, proposing replacement of HYCOM with MOM6 and CICE4 with CICE6 in operational RTOFS. Public comment closed April 15, 2026; not yet operationally scheduled.
- **Entry:** [RTOFS Global](./models/ocean_models/global/us/rtofs-global.md)
- **Authority:** NWS PNS 26-17
- **Full UFS context:** See [UFS.md](./UFS.md) — RTOFS v3.0 aligns RTOFS with the UFS ocean and ice model stack used elsewhere.

---

## Scheduled retirements (NWS PNS 25-41)

These systems are covered under NWS Public Information Statement 25-41 (June 26, 2025) and are scheduled for retirement once RRFSv1 is fully operational. **See [UFS.md](./UFS.md) for the consolidation context.**

- [NAM](./models/nwp_models/regional/usa/nam.md) — full retirement (12 km parent and all 3 km nests)
- [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md) — all convection-allowing nests
- [HREF](./models/ensemble_models/regional/usa/href.md) — replaced by REFS (extends 48h → 60h)
- HiresW — all domains except Guam; see [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) for the surviving exception
- NARRE (not in repo) — replaced by REFS

---

## Expected future retirements

These systems are publicly signaled but have **no formal retirement notification** as of April 2026. They are expected to be retired with RRFSv2 (MPAS-based). **See [UFS.md](./UFS.md) for the second-wave retirement context.**

- [HRRR](./models/nwp_models/regional/usa/hrrr.md) — expected with RRFSv2
- [RAP](./models/nwp_models/regional/usa/rap.md) — expected with RRFSv2
- NAM 12 km parent — expected with HRRR/RAP
- SREF (not in repo) — already lost largest consumer (NBM v5.0 eliminated SREF as input); expected with RRFSv2

---

## Experimental and pre-operational systems

### [GDPS-SN (ECCC)](./models/nwp_models/global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn)
Experimental hybrid physics–AI configuration of GDPS. GEM model spectrally nudged toward ECCC's GEML AI model at large scales. Distributed as experimental data via MSC GeoMet and MSC Datamart. The April 2026 CMC H100 GPU supercomputer upgrade is the enabling infrastructure for eventual operationalization.

### [CAPS (ECCC)](./models/nwp_models/regional/canada/caps.md)
Experimental coupled atmosphere-ocean-sea ice prediction system at ~3 km resolution covering a large pan-Arctic domain (northern Canada, Alaska, Greenland, Iceland, Scandinavia, Baltic countries, eastern Russia). GEM atmospheric component (configuration inherited from the decommissioned HRDPS-North) two-way coupled to NEMO v3.6 ocean and CICE v6.2.0 sea ice via the GOSSIP coupler at 5-minute exchange frequency. Provides forecast guidance to Canadian Storm Prediction Centres, DND, CCG, and DFO. Distributed via the standard MSC datamart but explicitly designated as experimental. Current version 3.0.0 implemented June 18, 2025.

### [HRDPS-West (ECCC)](./models/nwp_models/regional/canada/hrdps-west.md)
Experimental kilometric-scale (~1 km) deterministic NWP system covering Southern British Columbia. GEM v5.2.0 atmospheric model piloted by HRDPS national v7.0.0, with surface initial conditions from CaLDAS. Distributed via the MSC alpha datamart (separate from the standard operational MSC datamart). Intended to better resolve mesoscale features in BC's complex topography than the operational 2.5 km HRDPS. Current version 1.5.0 released November 6, 2024.

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

If you notice a missing status change — a new SCN, a retirement notification, an operational implementation date — please add it to the appropriate section above with a link to the authoritative source. For UFS-related changes, please also update [UFS.md](./UFS.md) so the two files stay synchronized.
