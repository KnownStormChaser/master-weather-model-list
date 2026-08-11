# WAM-IPE / WFS (Whole Atmosphere Model – Ionosphere Plasmasphere Electrodynamics Forecast System)

## What this model is
WAM-IPE is NOAA's operational coupled thermosphere–ionosphere forecast system. It predicts the terrestrial upper atmosphere: total electron content and plasma structure through the ionosphere and plasmasphere, and neutral density, temperature, composition and winds through the thermosphere — the quantities that govern GNSS positioning error, HF radio propagation, and satellite drag.

Two models are coupled. **WAM** is an extension of the Global Forecast System, a spectral hydrostatic dynamical core at T62 with an enthalpy thermodynamic variable, extended to 150 vertical levels on a hybrid pressure–sigma grid with a model top near 3 × 10⁻⁷ Pa (typically 400–600 km depending on solar activity). **IPE** supplies the plasma component, a time-dependent global 3-D model from 90 km to roughly 10,000 km. WAM's winds, temperature and composition are passed to IPE so the plasma responds to the neutral atmosphere below it.

It is developed by the Space Weather Prediction Center in partnership with the Environmental Modeling Center.

---

## Who runs it
- **Organization:** NOAA Space Weather Prediction Center (SWPC) with NCEP Environmental Modeling Center (EMC)
- **Country / region:** United States

---

## Scope qualification

- **Reference body:** **Earth.** WAM-IPE grids the Earth's own upper atmosphere on a geographic latitude–longitude mesh. Solar wind conditions enter only as scalar boundary forcing through a driver file; no heliospheric volume is computed or distributed.
- **Forecast or specification:** **Both, in two separately distributed configurations.** The forecast system (**WFS**) runs 4× daily and carries a genuine 48-hour lead time. The real-time nowcast system (**WRS**) reinitialises every six hours and produces an 8-hour window. Each output file records `run_type` in its global attributes, so the two are unambiguous once opened — but they share a directory tree and a filename structure, so they are easy to conflate before opening. See *Notes*.

---

## What area it covers
- **Coverage:** Global
- **Vertical extent (verified, and it differs by stream):**
  - IPE 3-D: **58 levels, 90 km to 2655 km**, non-uniform — 5 km spacing near the base widening to ~250 km at the top
  - WAM 3-D: **150 hybrid pressure–sigma levels**, with diagnostic geometric height ranging from roughly −0.3 km to 593 km in the sample decoded
  - AWS fixed-height product: **91 levels, 100 km to 1000 km at uniform 10 km spacing**
  - The declared `alt` dimension in the 2-D IPE files is 109 levels spanning 90–2000 km, but no 2-D variable uses it — see *Notes*.

---

## Basic details
- **Model type:** Physics-based coupled thermosphere–ionosphere model with lower-atmosphere data assimilation
- **Core model:** WAM (GFS-derived spectral core, T62, 150 levels) coupled to IPE
- **Data assimilation:** WDAS, the WAM data assimilation system. **Lower-atmosphere assimilation runs only on the 00 and 12 UT cycles**, to maintain stability of the coupled system — the 06 and 18 UT cycles inherit rather than reassimilate.
- **Forecast length (verified):** WFS produces a **51-hour window per cycle**: 3 hours of hindcast before the initialisation time plus 48 hours of forecast. Confirmed from file attributes on the 2026-08-10 18 UT cycle — `start_date` 20260810_150000, `init_date` 20260810_180000, `end_date` 20260812_180000 — and from the file count, 612 five-minute IPE steps.
- **Update frequency / cycles:** WFS 4× daily at 00, 06, 12, 18 UT; WRS reinitialised every six hours
- **Observed publication latency (verified):** on the 2026-08-10 18 UT WFS cycle, the driver file appeared at **21:01 UTC (T+3h01m)** and the last file of the cycle at **23:29 UTC (T+5h29m)**. Files are written progressively in forecast order over that window rather than released as a batch, so early lead times are usable well before the cycle completes.
- **Temporal output resolution (verified):** **5 minutes** for the 2-D streams, **10 minutes** for the 3-D streams
- **Model version:** v1.2, operational since July 2023 — introduced WRS into operations and improved the Kp-derived solar wind parameters used in the WFS forecast portion

---

## Reference frame and grid geometry
- **Horizontal coordinate system:** geographic latitude–longitude (`degrees_east` / `degrees_north`). No magnetic-coordinate conversion is needed to place output on Earth, which is unusual for this model class.
- **Grid dimensions (verified):** **90 × 91**
- **Horizontal resolution (verified):** **4° longitude × 2° latitude.** Longitude runs 0° to 356°; latitude runs −90° to +90° inclusive, pole to pole.
  - Note the SWPC description gives IPE as "approximately a 2-degree resolution", which matches the latitude spacing but not the longitude spacing. The distributed grid is anisotropic.
- **Vertical coordinate:** varies by stream (see *What area it covers*). The WAM 3-D files are on hybrid pressure–sigma levels with **`height` carried as a full 3-D diagnostic field in metres**, not as a coordinate axis — geometric altitude varies with location and time at a given model level, so these files cannot be sliced by altitude without interpolating first.
- **Conversion requirements:** none for horizontal placement.

---

## Drivers and inputs
Each cycle ships an `input_parameters.nc` driver file, which is the most useful single artefact for understanding what the run actually saw.

- **Contents (verified):** 16 scalar time series on a **1-minute** axis (`ifp_interval: 60`) — F10.7 and its 41-day average, Kp and its 24-hour average, Ap and its 24-hour average, northern and southern hemispheric power and their indices, and solar wind IMF total B, Bz, angle, velocity and density.
- **Length (verified):** **5220 steps (87 hours) for WFS**, 2640 steps (44 hours) for WRS. The driver file is substantially longer than the run window in both cases, because it carries a lead-in of past forcing before the model's own start time.
- **`skip` decodes the lead-in exactly.** The `skip: 2160` global attribute is 2160 minutes — **36 hours**. On the 2026-08-10 18 UT cycles, the WFS driver begins 2026-08-09 03:00 and 36 hours later is 2026-08-10 15:00, which is precisely that cycle's `start_date`; the WRS driver begins 2026-08-09 09:00 and 36 hours later is 2026-08-10 21:00, precisely its `init_date`. So the first 2160 rows are pre-run forcing history and the model window starts at row 2161.
- **Solar wind source:** real-time parameters ingested every 5 minutes from NOAA spacecraft at L1, which is what lets WRS capture sudden storm onset.
- **Forecast-portion forcing:** observed solar wind is used wherever available; beyond that, SWFO-issued forecast 3-hour Kp and daily F10.7 are ingested and used to *estimate* solar wind parameters.
- **The observed/estimated boundary is recorded explicitly.** Global attributes `final_swfo_f10_kp_date`, `final_imf_date` and `final_aurora_power_date` mark the last time each driver was observed rather than derived. This is genuinely useful — it lets a user establish, per cycle, exactly where the run stops being observation-driven. Few systems in this catalogue expose that boundary at all.
- **Lower atmospheric forcing:** the WAM component is driven from below by the GFS-derived analysis through WDAS on the 00 and 12 UT cycles.

---

## What it provides

Four output streams per cycle, plus the driver file.

**`ipe05` — 2-D ionosphere, 5-minute cadence** (~98 KB per file)
- `tec` — total electron content (TECu)
- `NmF2` — F2 peak density (m⁻³)
- `HmF2` — F2 peak height (km)

**`ipe10` — 3-D ionosphere, 10-minute cadence** (~22 MB per file)
- Ion densities: `O_plus_density`, `H_plus_density`, `He_plus_density`, `N_plus_density`, `NO_plus_density`, `O2_plus_density`, `N2_plus_density` (m⁻³)
- `ion_temperature`, `electron_temperature` (K)
- `eastward_exb_velocity`, `northward_exb_velocity`, `upward_exb_velocity` (m s⁻¹)

**`wam05` — 2-D thermosphere, 5-minute cadence** (~66 KB per file)
- `den400` — neutral density at 400 km (kg m⁻³)
- `ON2` — column-integrated O/N₂ ratio (dimensionless)

**`wam10` — 3-D thermosphere, 10-minute cadence** (~37 MB per file)
- `height` (m, diagnostic), `temp_neutral` (K)
- `O_Density`, `O2_Density`, `N2_Density` (m⁻³)
- `u_neutral`, `v_neutral`, `w_neutral` (m s⁻¹)

**AWS fixed-height product** — `den` only, neutral density (kg m⁻³) on 91 uniform 10 km levels from 100 to 1000 km, 10-minute cadence. This is a regridded derivative of `wam10`, not one of the four native streams.

---

## Data availability
- **Is the data free?** Yes — anonymous, no registration, no API key, on both channels
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD): open to the public and usable as desired. NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement or affiliation, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** Gridded fields
- **Data formats:** NetCDF throughout
- **Official download locations:**
  - **NOMADS (all four native streams):** https://nomads.ncep.noaa.gov/pub/data/nccf/com/wfs/prod/
    Structure: `{wfs|wrs}.YYYYMMDD/{00,06,12,18}/{wfs|wrs}.tCCz.{ipe05,ipe10,wam05,wam10}.YYYYMMDD_HHMMSS.nc` plus `.input_parameters.nc`. Verified 2026-08-10 18 UT: 1837 files in a WFS cycle directory (612 + 306 + 612 + 306 + 1), 289 in a WRS cycle.
  - **AWS NODD (fixed-height neutral density only):** `s3://noaa-nws-wam-ipe-pds`, us-east-1, anonymous
    Structure: `v1.2/{wfs|wrs}.YYYYMMDD/CC/wam_fixed_height.{wfs|wrs}.tCCz.wam10.YYYYMMDD_HHMMSS.nc`, 306 files per WFS cycle
- **Retention (verified):**
  - NOMADS carries roughly **two days** — only `20260810` and `20260811` were present on 2026-08-11
  - AWS holds **v1.1 from 2023-03-21 to 2023-08-02** and **v1.2 from 2023-07-01 to present**, current through 2026-08-11
- **Non-operational research outputs** sit in a top-level `NON-OPERATIONAL_RESEARCH/` prefix on AWS; the registry asks users to contact the WAM-IPE team before accessing them. Out of scope.
- **New-data notifications:** SNS topic `arn:aws:sns:us-east-1:709902155096:NewWIFSObject` (Lambda and SQS protocols only)

> **Verification status.** Grid dimensions, resolution, vertical coordinates, variable inventories, units, cadences, file counts, cycle timings, run-type attributes, driver-file contents and archive extents were all verified by downloading and decoding files from both NOMADS and AWS on 2026-08-11 with netCDF4. Nothing in this entry rests on documentation alone except the model's internal formulation and the assimilation cadence.

---

## Notes

- **The AWS bucket and NOMADS carry different products, and the AWS registry description understates the difference.** NOMADS serves all four native streams — 2-D and 3-D ionosphere and thermosphere. AWS serves **only** the derived fixed-height neutral density product, one variable on 91 uniform altitude levels, and nothing else: a full WFS cycle prefix on AWS contains 306 files, all `wam_fixed_height.*.wam10.*`. Anyone needing TEC, ion composition, or neutral winds must use NOMADS and accept its two-day retention; anyone needing a long archive of anything other than neutral density has no open channel at all.

- **The WRS directory label is three hours behind the actual initialisation.** Verified across all four cycles on 2026-08-10: the `00` directory contains a run initialised at 03:00 UT, `06` at 09:00, `12` at 15:00, and `18` at 21:00. Each runs 8 hours forward from there. The WFS directories, by contrast, are labelled with their true initialisation time and begin 3 hours *before* it. Treating the directory name as the initialisation time is correct for WFS and wrong by three hours for WRS.

- **WFS files begin before the initialisation time.** A WFS cycle's earliest file is stamped three hours before `init_date` — the 18 UT cycle's first IPE file is `20260810_150500`. These are hindcast steps, not forecast steps. Computing lead time as file timestamp minus directory date gives a negative number for the first 36 files of every cycle.

- **The 2-D files declare an unused vertical dimension.** Both `ipe05` and `wam05` carry an `alt` (109 levels, 90–2000 km) or third dimension of 150 in their dimension list, but every variable in them is 2-D on (lat, lon). The dimension is declared and populated as a coordinate variable but referenced by nothing. Harmless, but it will confuse dimension-driven readers that assume a declared axis is used.

- **WAM 3-D output is not on altitude levels.** `wam10` is on hybrid pressure–sigma levels with geometric `height` carried as a 3-D field. Constant-altitude slices require interpolation. This is precisely why the AWS fixed-height product exists, and why that product covers only neutral density — it is the field the satellite-drag community needs on fixed altitudes.

- **Assimilation is asymmetric across cycles.** Lower-atmosphere data assimilation runs only at 00 and 12 UT. The 06 and 18 UT cycles are therefore not equivalent products to their neighbours, which matters for anyone building a homogeneous time series from all four cycles.

- **The forecast is only as observation-driven as the driver file says.** Beyond the last observed solar wind measurement, the run uses solar wind parameters *estimated* from forecast Kp and F10.7. The `final_imf_date` attribute gives the exact changeover. In the 2026-08-10 18 UT WFS cycle sampled, `final_imf_date` was 20260810_205500 against a 48-hour forecast window — so the great majority of the forecast ran on estimated rather than measured forcing.

- **Non-operational disclaimer on the cloud channel.** The AWS registry states the bucket's files are provided strictly on a non-operational basis, with no guarantee of timely delivery or availability, and that temporal gaps may exist. NOMADS carries the operational feed.

- **Relationship to other systems.** WAM is a descendant of GFS and shares its spectral core; the GFS entry is the relevant cross-link for the dynamical core lineage. Within the space weather domain, WAM-IPE is currently the only system in this catalogue publishing gridded forecast output on open terms — the SWPC geoelectric and SECS products, GloTEC and D-RAP are all zero-lead specifications, and Ovation Prime's global grid is distributed as JSON rather than NetCDF.

- **AI relationship.** None identified.

---

## Recent version history
- **v1.2 — July 2023:** WRS implemented into operations; improvements to the Kp-derived solar wind parameters used by WFS forecasts.
- **v1.1 — March 2023 (AWS archive begins 2023-03-21):** superseded; AWS retains v1.1 data through 2023-08-02.

---

## Official documentation
- Product page: https://www.spaceweather.gov/products/wam-ipe
- AWS Open Data registry: https://registry.opendata.aws/noaa-nws-wam-ipe/
- NOMADS tree: https://nomads.ncep.noaa.gov/pub/data/nccf/com/wfs/prod/
- S3 bucket browser: https://noaa-nws-wam-ipe-pds.s3.amazonaws.com/index.html
- NODD programme: https://www.noaa.gov/nodd

### Contacts
- WAM-IPE team: Adam Kubaryk (adam.kubaryk@noaa.gov), Tzu-Wei Fang (tzu-wei.fang@noaa.gov)
- NODD: nodd@noaa.gov
