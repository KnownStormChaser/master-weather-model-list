# FNMOC Wave Ensemble (Navy WAVEWATCH III Ensemble)

## What this model is
The FNMOC Wave Ensemble is the U.S. Navy's operational global **ensemble** ocean-wave forecast system, run by the Fleet Numerical Meteorology and Oceanography Center (FNMOC). It runs the WAVEWATCH III spectral wave model as a 21-member ensemble, with each member forced by the corresponding member of the Navy's atmospheric [FNMOC Ensemble](../../../ensemble_models/global/usa/fnmoc-ensemble.md) — so the wave-forecast spread is inherited almost entirely from the driving atmospheric ensemble rather than from perturbing the wave model itself.

It is the wave sibling of the FNMOC (NAVGEM) atmospheric ensemble and is the Navy counterpart to Canada's [GEWPS](../canada/gewps-canada.md) and NOAA's GEFS-based global wave ensemble.

---

## Who runs it
- **Organization:** Fleet Numerical Meteorology and Oceanography Center (FNMOC), with model development by the Naval Research Laboratory – Monterey
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global oceans
- **Distributed grid:** Single global regular latitude–longitude grid, **720 × 361** points, **0.5°**, spanning 90°S→90°N and 0°→359.5°E. Live-verified on the 2026-07-24 12 UTC run (`gridType: regular_ll`, `centre: fnmo`).

---

## Basic details
- **Model type:** Ensemble wave model
- **Grid system:** Single global 0.5° regular lat-lon grid on distribution (computational grid configuration TBD)
- **Core wave model:** WAVEWATCH III (WW3); version TBD
- **Spectral resolution:** TBD (frequency × direction bins not documented from the public product)
- **Horizontal resolution:** 0.5° distributed
- **Forecast length:** 384 hours (16 days) — live-verified (f384 present, f390 absent)
- **Update frequency / cycles:** 2× daily (00, 12 UTC) — live-verified (both cycle directories present)
- **Temporal output resolution:** 3-hourly to f240, then 6-hourly to f384 (live-verified; f243 absent, f246 present)

---

## Forcing and nesting
- **Wind forcing:** 10 m winds from the Navy's atmospheric [FNMOC Ensemble](../../../ensemble_models/global/usa/fnmoc-ensemble.md) — each wave member forced by the matching atmospheric member (member-matched)
- **Ice forcing:** TBD (no ice-concentration field is distributed in this feed; the WW3 ice-masking rules for the Navy configuration are not documented here)
- **Current forcing:** TBD (likely none)
- **Nested inside / parent for:** Self-contained global system. No deterministic FNMOC wave sibling is published on this public NOMADS feed (the `fnmoc/prod/` directory carries only [NAVGEM](../../../nwp_models/global/usa/navgem.md) and this wave ensemble); the Navy's deterministic global WW3 runs internally within the Earth System Prediction Capability (ESPC) but is not on this feed.

---

## Ensemble configuration
- **Ensemble size:** 21 members — 1 control (`et000`) + 20 perturbed (`et001`–`et020`). Live-verified via distinct `perturbationNumber` 0–20 across separate files.
- **Source of perturbations:** Inherited from the atmospheric [FNMOC Ensemble](../../../ensemble_models/global/usa/fnmoc-ensemble.md) (perturbed 10 m winds per member). The wave model itself is not separately perturbed.
- **Resolution / output differences vs deterministic sibling:** No deterministic sibling on this feed to compare against (see Forcing and nesting).
- **Member packaging (live-verified 2026-07-24 12Z):** one GRIB2 file per member per forecast hour, member token in the filename (`etMMM`, `000`=control, `001`–`020`=perturbed). GRIB2 encoding: Product Definition Template 4.1, `numberOfForecastsInEnsemble = 20`, `centre = fnmo`. As with the atmospheric ensemble, FNMOC stamps a **local** `typeOfEnsembleForecast = 192` on every member, so members must be distinguished by `perturbationNumber`, not by `typeOfEnsembleForecast`. A single member/step file is ~1.4 MB.
- **Derived products distributed:** None as raw GRIB in this feed — raw members only. No ensemble-mean or spread files are published here; the rendered combined NCEP/FNMOC wave product (NFCENS) is a separate viewer-only product and is out of this repository's scope.

---

## Data assimilation
- **Assimilates wave observations:** No — the system is a forced wave ensemble with no wave data assimilation (spread derives from the atmospheric ensemble).

---

## What it provides
Probabilistic global wave forecasts (surface, GRIB2, one file per member per step). Live-verified field list on the 2026-07-24 12 UTC run (8 fields; discipline 10 / oceanographic):

- **Combined sea state:** significant height of combined wind waves and swell (`swh`, `10/0/3`)
- **Primary wave system:** mean period (`perpw`, `10/0/11`) and direction (`dirpw`, `10/0/10`)
- **Peak:** peak wave period (`pp1d`, `10/0/34`)
- **Wind-wave partition:** significant height (`shww`, `10/0/5`) and direction (`wvdir`, `10/0/4`)
- **Two FNMOC-local fields** eccodes 2.48 reports as `unknown`: a direction field (`10/0/249`, 0–360°) and a period field (`10/0/254`, ~1.9–22.7 s, correlated ~0.84 with wind-wave height). Best interpretation: a peak/mean wave direction and the wind-wave mean period, respectively — **TBD** pending resolution against NCEP/FNMOC's local GRIB2 wave table (Code Table 4.2, discipline 10, category 0).

No swell-partition, Stokes-drift, or sea-ice fields are distributed in this feed (a leaner set than Canada's [GEWPS](../canada/gewps-canada.md)).

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location (NOMADS):**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/fnmoc/prod/
  - Daily/cycle directories: `fens_wave.YYYYMMDD/CC/` (CC = 00 or 12)
  - Note this rides the Navy's own `fnmoc/prod/` feed — **not** the `naefs/prod/` feed used by the atmospheric FNMOC ensemble.
  - FTP mirror: `ftp://ftp.ncep.noaa.gov/pub/data/nccf/com/fnmoc/prod/`
- **File naming (live-verified 2026-07-24 12Z):** `ENSEMBLE.halfDegree.OCN.fcst_etMMM.FFF.YYYYMMDDHH`, where `MMM` = member (`000`–`020`; `000` = control), `FFF` = forecast hour. (`OCN` marks the ocean/wave stream, paralleling the atmospheric ensemble's `MET` stream.)

---

## Notes
- **Relationship to siblings:** Wave sibling of the atmospheric [FNMOC Ensemble](../../../ensemble_models/global/usa/fnmoc-ensemble.md) (its wind-forcing source) and Navy counterpart to Canada's [GEWPS](../canada/gewps-canada.md). All three national global wave ensembles (Navy, NCEP GEFS-wave, ECCC GEWPS) share WAVEWATCH III lineage.
- **NFCENS is a different, out-of-scope product:** the "Combined NCEP/FNMOC Wave Ensembles" (NFCENS) is a rendered 0.1° statistical map product (viewer-only) that blends NCEP and FNMOC wave members. It is not the raw member feed documented here and does not meet the repository's raw-gridded-data scope.
- **Live verification (2026-07-24 12Z cycle):** grid (0.5°, 720 × 361), member scheme (`et000`–`et020`, `perturbationNumber` 0–20), cadence (3-hourly→f240, 6-hourly→f384), f384 horizon, and the 8-field set were confirmed by pulling raw `et000`/`et001`/`et020` files. Two local wave parameters (`10/0/249`, `10/0/254`) remain unresolved (flagged above). Spectral resolution, ice-masking rules, and WW3 version were not determinable from the public product and are left TBD.

---

## Official documentation
- FNMOC: https://www.metoc.navy.mil/fnmoc/fnmoc.html
- NRL NAVGEM page (atmospheric driver): https://www.nrlmry.navy.mil/metoc/nogaps/navgem.html
- WAVEWATCH III (model): https://polar.ncep.noaa.gov/waves/wavewatch/
