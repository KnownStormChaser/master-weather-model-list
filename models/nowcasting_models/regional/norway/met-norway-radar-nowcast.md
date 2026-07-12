# Yr/MET Norway Nordic Radar Nowcast

## What this model is
MET Norway's Nordic radar precipitation nowcast is a very-short-range precipitation-rate forecast produced by extrapolating weather-radar observations forward in time. An optical-flow algorithm estimates motion vectors from the two most recent radar mosaics and advects the observed precipitation field ahead, out to about two hours. Where radar coverage is insufficient, the nowcast falls back on the MetCoOp Ensemble Prediction System (MEPS, 2.5 km) as its background field. The gridded product is distributed as CF-NetCDF on a 1 km Nordic Lambert Conformal Conic grid, updated every 5 minutes.

The underlying field is a pseudo-CAPPI (ground-level) rain-rate radar mosaic, clutter-filtered, clutter- and beam-blockage-corrected, without vertical-profile-of-reflectivity correction.

[LOAD-BEARING CAVEAT — verify before filing: the distributed filenames decode to a radar rain-rate *mosaic* (observation) rather than a forecast field. Confirm that each `.nc` file contains a `time` dimension with forward lead steps (nowcast). If each file holds only a single valid time, this is an observed radar composite and belongs under `observations/radar/`, not here.]

---

## Who runs it
- **Organization:** MET Norway (Meteorologisk institutt)
- **Country / region:** Norway (operator); Nordic domain

---

## What area it covers
- **Coverage:** Nordic region — Norway, Sweden, Finland, and Denmark (nowcast domain per MET's API documentation). Radar inputs are drawn from the Norwegian, Swedish, and Finnish networks.
- **Domain details:** Nordic Lambert Conformal Conic projection, 1 km grid spacing (`nordiclcc-1000`). Precipitation-rate values are only meaningful where radar coverage is of sufficient quality.

---

## Basic details
- **System type:** Nowcasting
- **Nowcasting method:** Observation extrapolation — optical-flow advection of the radar precipitation field, with MEPS (2.5 km) as background/fallback where radar coverage is poor
- **Technique / algorithm:** Motion vectors estimated from the two most recent radar mosaics; existing precipitation is advected forward. Note: the optical-flow step only *moves* existing precipitation — it does not model growth, decay, or convective initiation (Lagrangian persistence).
- **Underlying / driving model:** MEPS (MetCoOp Ensemble Prediction System, 2.5 km) as the fallback/background field. [Cross-link if you catalog MEPS.]
- **Base observation field:** Pseudo-CAPPI ground-level rain-rate radar mosaic (clutter-filtered, clutter- and blockage-corrected, no VPR correction)
- **Probabilistic / ensemble:** No (deterministic extrapolation)
- **Horizontal resolution:** 1 km
- **Vertical structure:** 2D surface field (rain rate)
- **Lead time:** ~0–2 h [Verify against the gridded files — see caveat above. The 0–2 h figure is from MET's nowcast API documentation.]
- **Update frequency:** Every 5 minutes
- **Temporal output resolution:** 5 min
- **Latency:** ~4–5 min after valid time (inferred from catalog modification times — e.g. the 21:55Z file posted at 22:00:38Z; not an official spec)

---

## Inputs
- **Radar:** Norwegian, Swedish, and Finnish weather-radar networks, composited to a pseudo-CAPPI rain-rate mosaic on the Nordic 1 km grid
- **NWP fields:** MEPS (2.5 km) as background/fallback where radar coverage is insufficient
- [Note: MET Norway's location-based nowcast API additionally blends webcams, citizen observations (Netatmo, Holfuy), and professional stations for other variables and weather-symbol correction. Those enrichments appear in the point/API product; confirm whether they touch this gridded precipitation-rate field.]

---

## What it provides
- Precipitation rate (rain rate), 1 km, on the Nordic LCC grid
- [If the file check confirms forward lead times: a 0–2 h sequence of extrapolated rain-rate fields at 5-min steps.]

---

## Data availability
- **Is the data free?** Yes
- **License:** MET Norway open data — expected CC BY 4.0 / NLOD (same terms as your existing MET Norway radar entry). [Confirm on the dataset metadata for the `radarnowcasting` catalog.]
- **Is the data downloadable?** Yes
- **Data formats:** CF-NetCDF (`.nc`), ~65–69 MB per file
- **File naming:** `yrwms-nordic.mos.pcappi-0-rr.noclass-clfilter-novpr-clcorr-block.nordiclcc-1000.<YYYYMMDD>T<HHMMSS>Z.nc`
- **Official download location:**
  https://thredds.met.no/thredds/catalog/radarnowcasting/catalog.html
  (HTTP download and OPeNDAP access per file via the THREDDS server)

---

## Notes
- **Alternate access:** MET Norway also serves a location/point nowcast as JSON via the MET Weather API (`https://docs.api.met.no/doc/nowcast/datamodel.html`), covering the same underlying system with additional non-precipitation parameters. That is a point/API product rather than gridded data — treat as a "see also," not the catalog entry.
- The optical-flow persistence limitation (no growth/decay/initiation) is a documented characteristic worth keeping in the entry.
- **Placement contingency:** if verification shows the files are single-valid-time radar composites, this is an observation product → move to `observations/radar/` and consider whether it extends the existing MET Norway radar entry rather than standing alone.

---

## Official documentation
- THREDDS catalog: https://thredds.met.no/thredds/catalog/radarnowcasting/catalog.html
- MET Weather API — Nowcast data model: https://docs.api.met.no/doc/nowcast/datamodel.html
- MEPS background (MetCoOp): https://docs.api.met.no/doc/ (MEPS article, linked from the nowcast data model page)
