# Yr / MET Norway Nordic Radar Nowcast

## What this model is
This is MET Norway's Nordic radar precipitation nowcast: a very-short-range precipitation-rate forecast produced by extrapolating a multi-radar rain-rate mosaic forward in time using optical flow. Motion vectors are estimated from recent radar mosaics and used to advect the observed precipitation field ahead in 5-minute steps out to nearly two hours. Each file is a single nowcast run — an analysis frame (lead +0) followed by 23 forward steps — distributed as CF-NetCDF on a 1 km Nordic Lambert Conformal Conic grid, with a new run produced every 5 minutes.

The underlying field is a pseudo-CAPPI (ground-level) rain-rate radar mosaic that is clutter-filtered, clutter- and beam-blockage-corrected, without vertical-profile-of-reflectivity correction. The optical-flow displacement vectors used to generate the forecast are stored inside each file.

---

## Who runs it
- **Organization:** MET Norway (Meteorologisk institutt)
- **Country / region:** Norway (operator); Nordic domain

---

## What area it covers
- **Coverage:** Nordic domain on a Lambert Conformal Conic grid (~1693 × 2133 km). Radar inputs are drawn from the Norwegian, Swedish, and Finnish networks (per the in-file mosaic node list; no Danish radars contribute to this mosaic). MET Norway's separate location-based nowcast *service* documents a domain also covering Denmark.
- **Domain details:** Lambert Conformal Conic (latitude of origin 63°N, central meridian 15°E, spherical Earth R = 6371 km); 1694 × 2134 grid at 1 km spacing (`nordiclcc-1000`). Precipitation-rate values are only present where radar coverage is of sufficient quality (~65% of the grid carries valid data; the remainder is fill).

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Observation extrapolation — optical-flow advection of the radar precipitation mosaic
- **Technique / algorithm:** Motion vectors estimated from recent radar mosaics advect the existing precipitation field forward. The reverse-displacement (x, y) fields are stored per file. Note: optical flow only *moves* existing precipitation — it does not model growth, decay, or convective initiation (Lagrangian persistence).
- **Underlying / driving model:** None in this gridded product (pure radar extrapolation). MEPS (2.5 km MetCoOp ensemble) serves as a fallback/background in MET Norway's location-based nowcast service, but is not part of this gridded radar-mosaic nowcast — see Notes.
- **Base observation field:** Pseudo-CAPPI ground-level rain-rate radar mosaic (clutter-filtered, clutter- and blockage-corrected, no VPR correction), from Norwegian + Swedish + Finnish radars
- **Probabilistic / ensemble:** No (deterministic, single-member)
- **Horizontal resolution:** 1 km
- **Vertical structure:** 2D surface field (precipitation rate)
- **Lead time:** +0 to +115 min (24 time steps per run, including the +0 analysis frame)
- **Update frequency:** New nowcast run every 5 min
- **Temporal output resolution:** 5 min
- **Latency:** ~4–5 min after reference time (inferred from catalog modification times — e.g. the 22:05Z run posted ~22:10Z; not an official spec)

---

## Inputs
- **Radar:** Norwegian, Swedish, and Finnish weather-radar networks, composited to a pseudo-CAPPI rain-rate mosaic on the Nordic 1 km grid
- [The gridded product is radar-only. MET Norway's location/point nowcast additionally blends webcams, citizen observations (Netatmo, Holfuy), professional stations, and MEPS — those enrichments live in the point API product, not in these gridded files.]

---

## What it provides
- `lwe_precipitation_rate` — liquid-water-equivalent precipitation rate (mm/h), 1 km, on the Nordic LCC grid
- A 0–115 min sequence at 5-min steps per file (24 steps)
- Embedded optical-flow displacement fields (`rev_u_displacement`, `rev_v_displacement`)

---

## Data availability
- **Is the data free?** Yes
- **License:** MET Norway open data — CC BY 4.0 / NLOD (same terms as the existing MET Norway radar entry). [Confirm on the `radarnowcasting` catalog metadata.]
- **Is the data downloadable?** Yes
- **Data formats:** CF-1.6 NetCDF (`.nc`), ~69 MB per file
- **File naming:** `yrwms-nordic.mos.pcappi-0-rr.noclass-clfilter-novpr-clcorr-block.nordiclcc-1000.<YYYYMMDD>T<HHMMSS>Z.nc`
- **Official download location:**
  https://thredds.met.no/thredds/catalog/radarnowcasting/catalog.html
  (per-file HTTP download and OPeNDAP access via the THREDDS server)

---

## Notes
- **Alternate access:** MET Norway also serves a location/point nowcast as JSON via the MET Weather API (`https://docs.api.met.no/doc/nowcast/datamodel.html`). It covers the same underlying system with additional non-precipitation parameters and MEPS fallback, but is a point/API product rather than gridded data — treat as a "see also," not the catalog entry.
- The optical-flow persistence limitation (no growth/decay/initiation) is a documented characteristic of the method.
- Confirmed as a nowcast (not a radar observation) by file inspection: each file carries a `forecast_reference_time`, a 24-step forward `time` axis (+0 to +115 min at 5-min spacing), and embedded advection displacement fields.

---

## Official documentation
- THREDDS catalog: https://thredds.met.no/thredds/catalog/radarnowcasting/catalog.html
- MET Weather API — Nowcast data model: https://docs.api.met.no/doc/nowcast/datamodel.html
