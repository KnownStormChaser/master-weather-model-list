# Model Status Tracker

This page tracks major lifecycle events for models documented in this repository — upcoming implementations, scheduled retirements, experimental systems, and version upgrades.

The goal is to give users a single place to check "what is changing" without having to browse individual model entries. Each item links to the relevant entry where detailed information lives.

For UFS-related transitions specifically, see [UFS.md](./UFS.md), which provides the full programme context. UFS items are listed here briefly; UFS.md has the detailed narrative.

Last updated: August 2026.

---

## Imminent or recent changes

### NBM v5.0 — operational May 5, 2026
Major upgrade to the National Blend of Models with longer hourly guidance (36 h → 48 h), new products, removed Haines Index, and new inputs including ECAIFS and AIGFS.
- **Entry:** [NBM](./models/nwp_models/regional/usa/nbm.md)
- **Authority:** NWS SCN 26-24 (AAC revision, April 28, 2026)
- **Verification note:** Originally targeted April 15, 2026; rescheduled to April 23, then April 30. The April 30 attempt fell within a Critical Weather Day / Enhanced Caution Event window, triggering the SCN's contingency provision and pushing actual cutover to May 5, 2026.

### NBM v5.1 — planned, no formal schedule
The next National Blend of Models upgrade after v5.0. Planned scope: 12-hour winter products, fixes for "blocky" percentile and precipitation-type (ptype) fields, remaining post-v5.0 winter-weather guidance work, and a new Southwest Pacific domain (a coverage expansion rather than a methodology change). No NWS Service Change Notice or PNS has been issued and it does not appear on the NBM versions page — this is a planning signal only, and no implementation date is recorded here pending a formal notice.
- **Entry:** [NBM](./models/nwp_models/regional/usa/nbm.md)
- **Source:** NBM user webinar (April 15, 2026) and WPC/HMT "NBMv5 Winter Overview" (February 10, 2026) — presentation decks, not authoritative notices

### IFS Cycle 50r1, AIFS Single v2, AIFS ENS v2 — operational May 12, 2026
ECMWF's physics-based and AI forecast lines upgraded together on the same day, as planned. Cycle 50r1 brings fully coupled atmosphere–ocean–sea-ice data assimilation, the new NEMO4-SI3 ocean/sea-ice core, revised convection and cloud-microphysics (addressing stationary heavy rainfall), and over 40 new ocean and sea-ice variables — all with no change in horizontal or vertical resolution. AIFS Single v2 adds a 10 hPa pressure level, a new data-driven wave stream, and a snow cover parameter. AIFS ENS v2 introduces a new probabilistic wave ensemble stream and a multi-scale loss function. The separate HRES stream is discontinued: the ex-HRES data stream becomes the ENS Control (MARS users migrate `stream=enfo, type=cf` → `stream=oper, type=fc` and `stream=scda, type=fc` for 06/18 UTC). ECMWF's experimental external ML model suite (Aurora, FourCastNet, GraphCast, Pangu-Weather) has been discontinued in operations as of the same day.
- **Entries:** [IFS Open Data](./models/nwp_models/global/eu/ifs-open-data.md) · [ECMWF EPS](./models/ensemble_models/global/eu/ecmwf-eps.md) · [AIFS Single](./models/nwp_models/global/eu/aifs-single.md) · [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)
- **Authority:** ECMWF news release, [IFS Cycle 50r1 and AIFS v2 go live](https://www.ecmwf.int/en/about/media-centre/news/2026/ifs-cycle-50r1-aifsv2-live) (May 12, 2026); [Implementation of IFS Cycle 50r1](https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+50r1); ECMWF Newsletter 185, [Upgrade of IFS Cycle 50r1](https://www.ecmwf.int/en/newsletter/185/earth-system-science/upgrade-ifs-cycle-50r1)
- **Downstream note:** Individual model entries ([IFS](./models/nwp_models/global/eu/ifs.md), [IFS ENS](./models/ensemble_models/global/eu/ifs-ens.md), [AIFS Single](./models/nwp_models/global/eu/aifs-single.md), [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)) still describe these changes under "Upcoming changes" — they should be migrated to "Version history" / "Recent version history" sections in a follow-up pass.

### GDPS v10.0.0 — operational May 26, 2026 (12 UTC)
ECCC's operational global deterministic system becomes a hybrid physics–AI system: GEM's large-scale temperature and horizontal wind fields (250–850 hPa) are spectrally nudged toward the GEML v1.1 AI weather model. This is the operationalization of the configuration previously distributed as the experimental GDPS, which is now retired as a separate experimental product. Replaces v9.1.0.
- **Entry:** [GDPS](./models/nwp_models/global/canada/gem-global.md)
- **Authority:** ECCC technical note CMC-GDPS-EXP-10.0.0-2026; GDPS 10.0.0 fact sheet and technical specifications (May 2026)

### RRFSv1 and REFS — scheduled October 6, 2026 (12 UTC)
NOAA's next-generation convection-allowing system for North America is now formally scheduled. RRFS and REFS implement together, with legacy NAM, HREF, SREF, and HiresW (all domains except Guam) retiring on the same day. The pre-implementation real-time feed went live on NOMADS at the 12 UTC cycle on
**August 12, 2026**, one day after the SCN's "on or about August 11" date, and the AWS
prototype bucket stopped updating the same day (see *Format and distribution changes*). **See [UFS.md](./UFS.md) for the full UFS context including the wave of retirements that occurs on the same day.**
- **Entries:** [RRFS](./models/nwp_models/regional/usa/rrfs.md) · [REFS](./models/ensemble_models/regional/usa/refs.md)
- **Authority:** NWS SCN 26-48 (RRFS/REFS implementation) + companion SCN 26-47 (terminations), both updated July 6, 2026 (originally May 12, 2026)
- **Verification note:** Originally targeted early 2026, then August 31, 2026; slipped again to October 6, 2026 in the July 6, 2026 update (AAB), which also decoupled the real-time parallel feed to on or about August 11, 2026. The October 6 date is subject to the standard CWD/ECE contingency — if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday. NBM v5.0 was deferred under exactly this provision earlier in 2026, so the contingency is not theoretical.

### HARMONIE-AROME Cy43 → Cy46 (KNMI / UWC-West) — planned November 2026
UWC-West's shared HARMONIE-AROME configuration moves from Cycle 43 to Cycle 46, and
**Cy43 is discontinued at the same time** — this is a cutover, not a parallel run. The
headline change for data users is the encoding: Cy46 writes WMO-compliant **GRIB2**
directly, replacing Cy43's GRIB1 with KNMI local table 253 (see *Format and distribution
changes*). Scientifically the cycle itself brought little new; the improvements come from
UWC-West's own additions on top of it — shallow convection scheme changes, new
physiography (GLO-90 DEM at 150 m, updated ECOCLIMAP-SG maps), reduced evaporation and
roughness for low vegetation, ECUME6 air-sea fluxes, forecast SST as surface boundary
condition, and assimilation of T2m/RH2m plus low-peaking microwave channels.

A test-phase data stream for the **Dutch P1 package only** has been live on the KNMI Data
Platform since 6 August 2026 (`harmonie_arome_cy46_p1`, CC BY 4.0, same Open Data API and
MQTT access as the operational datasets). KNMI states the remaining packages will follow
during the testing phase. No separate entry is catalogued for the test stream; the
existing entries will be revised at cutover.

- **Entries:** [HARMONIE-AROME Netherlands (P1)](./models/nwp_models/regional/netherlands/harmonie-arome-netherlands.md) · [HARMONIE-AROME Europe/DINI (P3/P5)](./models/nwp_models/regional/netherlands/harmonie-knmi.md) · [HARMONIE-AROME North Sea Forcing (L20)](./models/nwp_models/regional/netherlands/harmonie-knmi-l20.md) · [HARMONIE-AROME EPS Netherlands (P2a)](./models/ensemble_models/regional/netherlands/harmonie-eps-knmi-nl.md) · [HARMONIE-AROME EPS Europe (P4a)](./models/ensemble_models/regional/netherlands/harmonie-eps-knmi-eu.md)
- **Authority:** KNMI Data Platform news, [Test data available for Harmonie 46](https://english.knmidata.nl/latest/news/2026/04/15/test-data-available-for-harmonie-46) (15 April 2026, revised transition schedule) and [Test data stream available for Harmonie 46](https://english.knmidata.nl/latest/news/2026/08/11/test-data-stream-available-for-harmonie-46) (11 August 2026); UWC-West "HARMONIE CY46 Evaluation" verification report and P1/P3 content manifests, https://surfdrive.surf.nl/s/4XGmiN9i68PPjZy; HIRLAM [Harmonie 46h1.1 release notes](https://github.com/Hirlam/Harmonie/wiki/Harmonie-46h1.1-release-notes)
- **Verification note:** KNMI's published schedule gives testing April–October 2026,
  transition November 2026, aftercare December 2026 – January 2027. **No more specific
  date than "November" has been published**, and the schedule has already slipped twice
  (operational implementation was originally autumn 2025, then April 2026). Treat November
  as directional. Whether the KNMI-operated Caribbean (BES) domains move on the same date
  is not stated in any published source. The verification report also lists DINI-EPS with
  updated SPP settings and a new, larger IG domain as outstanding tasks, so the EPS entries
  may need geometry changes beyond the encoding switch.

---

## Recently operational (AI weather systems)

### AIGFS, AIGEFS, HGEFS — operational December 17, 2025
NOAA's three AI-based global weather systems became operational on the same date, replacing EAGLE SOLO (deterministic) and EAGLE Ensemble. AIGFS and AIGEFS are standalone AI systems based on the GraphCast architecture; HGEFS pools the 31 GEFS physics members with the 31 AIGEFS AI members into a 62-member grand ensemble, though it distributes only the pooled mean and spread rather than the members themselves. None of the three replaces an operational physics-based system — they run alongside GFS and GEFS. All three are distributed on NOMADS only, with no FTP and no NODD/AWS presence.
- **Entries:** [AIGFS](./models/nwp_models/global/usa/aigfs.md) · [AIGEFS](./models/ensemble_models/global/usa/aigefs.md) · [HGEFS](./models/ensemble_models/global/usa/hgefs.md)
- **Authority:** NWS SCN 25-89 (implementation of AIGFS, AIGEFS and HGEFS), December 2025

### AIFS Single v2, AIFS ENS v2 — operational May 12, 2026
ECMWF's AI deterministic and ensemble forecast systems both upgraded to v2, jointly with IFS Cycle 50r1 (see above). v2 was fine-tuned specifically on Cycle 50r1 esuite analyses, adds a 10 hPa pressure level, and introduces ECMWF's first data-driven wave forecasts (deterministic and ensemble) alongside a new snow cover parameter. AIFS ENS v2 replaces v1's afCRPS loss with a multi-scale loss function.
- **Entries:** [AIFS Single](./models/nwp_models/global/eu/aifs-single.md) · [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)
- **Authority:** ECMWF news release, [IFS Cycle 50r1 and AIFS v2 go live](https://www.ecmwf.int/en/about/media-centre/news/2026/ifs-cycle-50r1-aifsv2-live) (May 12, 2026)

### AIGFS v1.1 — operational July 27, 2026
NOAA upgraded the deterministic AIGFS to v1.1 at the 12 UTC cycle. Three training changes: the grid-point MSE loss function was replaced with a spherical harmonic loss using wind speed and direction rather than u/v components; the model was fine-tuned on four years of GDAS analysis; and fine-tuning was extended to train autoregressively to a 72-hour lead time. Reported improvements to tropical cyclone intensity, forecast precipitation, and the blurring of fields at long lead times. This is the AIGFSdev2.1 spectral-loss line of work reaching operations.
- **Entry:** [AIGFS](./models/nwp_models/global/usa/aigfs.md)
- **Authority:** NWS SCN 26-68 (AIGFS v1.1 implementation), July 27, 2026
- **Verification note:** Product content was unchanged — grid, parameter set, cycle schedule, and forecast length all match v1.0, so this is a model-quality upgrade with no downstream ingest impact. **AIGEFS and HGEFS were not included**; the NOMADS paths still resolve to `aigefs/v1.0/` and `hgefs/v1.0/` as of 2026-08-06, so the deterministic and ensemble AI systems are no longer at matched model versions.

---

## Scheduled retirements (NWS SCN 26-47 + 26-48 / PNS 25-41)

These systems are scheduled for retirement on **October 6, 2026 at 12 UTC**, the same cycle that brings RRFSv1 and REFS into operations. The retirement set was originally proposed in NWS Public Information Statement 25-41 (June 26, 2025); the retirements are scheduled by NWS Service Change Notice 26-47 (termination) and RRFS/REFS implementation by companion SCN 26-48, both updated July 6, 2026 (second slip, from August 31 to October 6). Subject to the standard CWD/ECE contingency. **See [UFS.md](./UFS.md) for the consolidation context.**

- [NAM](./models/nwp_models/regional/usa/nam.md) — full retirement (12 km parent and all 3 km nests)
- [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md) — all convection-allowing nests
- [HREF](./models/ensemble_models/regional/usa/href.md) — replaced by REFS (extends 48 h → 60 h)
- [HiresW](./models/nwp_models/regional/usa/hiresw.md) — CONUS, Alaska, Hawaii, and Puerto Rico domains; see [Status and retirement](./models/nwp_models/regional/usa/hiresw.md#status-and-retirement) for the surviving Guam exception
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

### [GEML (ECCC)](./models/nwp_models/global/canada/gdps-geml.md)
Operational standalone AI global deterministic weather model — the AI component of GDPS v10.0.0 (operational May 26, 2026). Graph neural network derived from Google DeepMind's GraphCast architecture, retrained by ECCC on ERA5 reanalysis (1979–2016) and ECMWF HRES analyses (2016–2021). 0.25° (~28 km) resolution, 13 pressure levels, 10-day forecast 2× daily, initialized from GDPS analysis. Serves a dual role as both a standalone forecast product (distributed at https://dd.weather.gc.ca/today/model_gdps-geml/25km/) and the spectral nudging target for the operational [GDPS](./models/nwp_models/global/canada/gem-global.md) (since v10.0.0). Trained model weights are publicly released via HuggingFace at https://huggingface.co/ECCC-ASTD-MRD/geml — unusual openness compared to most operational AI weather systems.

### [CAPS (ECCC)](./models/nwp_models/regional/canada/caps.md)
Experimental coupled atmosphere-ocean-sea ice prediction system at ~3 km resolution covering a large pan-Arctic domain (northern Canada, Alaska, Greenland, Iceland, Scandinavia, Baltic countries, eastern Russia). GEM atmospheric component (configuration inherited from the decommissioned HRDPS-North) two-way coupled to NEMO v3.6 ocean and CICE v6.2.0 sea ice via the GOSSIP coupler at 5-minute exchange frequency. Provides forecast guidance to Canadian Storm Prediction Centres, DND, CCG, and DFO. Distributed via the standard MSC datamart but explicitly designated as experimental. Current version 3.0.0 implemented June 18, 2025.

### [HRDPS-West (ECCC)](./models/nwp_models/regional/canada/hrdps-west.md)
Experimental kilometric-scale (~1 km) deterministic NWP system covering Southern British Columbia. GEM v5.2.0 atmospheric model piloted by HRDPS national v7.0.0, with surface initial conditions from CaLDAS. Distributed via the MSC alpha datamart (separate from the standard operational MSC datamart). Intended to better resolve mesoscale features in BC's complex topography than the operational 2.5 km HRDPS. Current version 1.5.0 released November 6, 2024.

### GraphCastGFS (NOAA)
Experimental productionization of Google DeepMind's GraphCast architecture by NOAA, fine-tuned on GDAS+ERA5 data. Predecessor of the operational AIGFS, but continues to run experimentally alongside its operational descendant.

### FourCastNetGFS (NOAA)
Experimental productionization of NVIDIA's FourCastNet architecture using Spherical Fourier Neural Operators. No operational descendant announced as of April 2026.

---

## Format and distribution changes

### RRFS and REFS — distribution moved twice in three days, August 2026
The pre-implementation parallel feed began at the 12 UTC cycle on **August 12, 2026** on
NOMADS at `/pub/data/nccf/com/rrfs/para/` and `/pub/data/nccf/com/refs/para/`, one day
later than the "on or about August 11" date in SCN 26-48. The `s3://noaa-rrfs-pds`
prototype bucket stopped the same day — RRFS after 11 UTC, REFS after 06 UTC — leaving
NOMADS briefly as the only channel. On **August 13 at roughly 21:30 UTC**, NODD stood up
a replacement bucket, **`s3://noaa-rrfs-ops-pds`**, which backfilled what NOMADS still
held and has ingested in near real time since.

The replacement carries RRFS, REFS and fire-weather output under a flat
`{rrfs|refs|firewx}.YYYYMMDD/` layout mirroring NOMADS, rather than the prototype's
`rrfs_public/` ÷ `rrfs_a/` split. Files are byte-identical to NOMADS and land within
about a minute of it. It **restores the `.idx` sidecars and BUFR soundings** that NOMADS
does not carry, and holds more history than the two-day NOMADS `para` window.
**Individual ensemble members and native-level output were not restored** and remain
unavailable through any open channel.

- **Entries:** [RRFS](./models/nwp_models/regional/usa/rrfs.md) · [REFS](./models/ensemble_models/regional/usa/refs.md)
- **Authority:** NWS SCN 26-48 (parallel feed paths); direct verification of both channels
- **Verification note:** Cutover boundary confirmed by object listing on both sides — last
  old-bucket object 2026-08-12T12:58:58Z, first NOMADS files 13:49 UTC, first
  replacement-bucket object 2026-08-13T21:32:04Z. Byte-identity confirmed by MD5 on
  matched RRFS and REFS files from the 2026-08-14 12 UTC cycle. **The AWS Open Data
  Registry entry `noaa-rrfs` has not been updated** — it still lists only the frozen
  prototype bucket, still carries the "[Prototype]" title, and there is no registry page
  or `docs.opendata.aws` readme for the replacement. Post-implementation paths from
  October 6, 2026 remain `/rrfs/prod/` and `/refs/prod/`.

### IFS Cycle 50r2 — tentative Q4 2026
Complete migration of ECMWF IFS to GRIB2-only parameter representation. Affects Open Data users directly. Legacy GRIB1-style parameter references move to GRIB2-native identifiers; CCSDS compression required.
- **Entries:** [IFS Open Data](./models/nwp_models/global/eu/ifs-open-data.md) · [ECMWF EPS](./models/ensemble_models/global/eu/ecmwf-eps.md)
- **Authority:** ECMWF Migration to GRIB edition 2 page

### KNMI HARMONIE-AROME — GRIB1 to GRIB2 at the Cy46 cutover (planned November 2026)
Every KNMI HARMONIE dataset currently ships GRIB **edition 1** with KNMI local
`table2Version` 253, meaning parameters resolve as `unknown` under stock ecCodes and
require KNMI's published code table. Cy46 output is GRIB **edition 2** (master tables v35)
and decodes with stock ecCodes tables, removing the local-table dependency entirely.
Verified against the live `harmonie_arome_cy46_p1` test stream on 2026-08-15:

- **Grid, horizon and cadence are unchanged** — 390 × 390 `regular_ll`, 49.0–56.002°N /
  0.0–11.281°E, 0.029° × 0.018° increments, 0–60 h hourly (61 steps per run), hourly
  cycling. No regridding work needed downstream.
- **Parameter set loses exactly three fields**: the ISBA nature-tile soil temperatures
  (GRIB1 `indicatorOfParameter` 11 at levels 800/801/802, i.e. 0 cm / −7 cm / −50 cm).
  49 messages per step becomes 46. Every other field maps 1:1.
- **Forecast time is encoded in minutes**, not hours (`indicatorOfUnitOfTimeRange` 0), and
  filenames carry a five-digit `HHHMM` step field. Sub-hourly output is an outstanding task
  in the UWC-West report, not yet present in the stream — all 61 members are hourly.
- **Accumulation semantics are preserved** but now explicit: PDT 8 with
  `typeOfStatisticalProcessing` 1 for from-run-start accumulations (precipitation,
  radiation, fluxes) and 2 for past-hour maxima (10 m gusts, column-integrated graupel).
- **`generatingProcessIdentifier` remains 43** in Cy46 files, so the identification section
  cannot be used to distinguish the cycles — branch on GRIB edition or filename prefix.
- **The April 2026 static test samples do not match the live stream.** The samples added
  `min_visp` and downward long-wave `strd` and omitted the 10 m gust components; the live
  stream drops the two additions, restores the gusts, and swaps ceiling → cloud base and
  minimum-visibility → instantaneous visibility. Anyone who coded against the April ZIPs
  should re-check against the stream.
- **Instantaneous precipitation units are ambiguous.** GRIB2 codes 0/1/65, 0/1/66 and
  0/1/75 decode as rates (`rprate`, `sprate`, kg m⁻² s⁻¹), while KNMI's parameter table
  documents them as kg m⁻². Unresolved; raised as a question to KNMI.
- **Access is unchanged** — Open Data API plus MQTT notification, CC BY 4.0, API key
  required. Note the anonymous key rotates annually (the previous one expired 1 July 2026);
  entries should link the Developer Portal rather than embed a key value.

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
