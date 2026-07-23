# WAM (DMI)

## What this model is
WAM is the **third-generation spectral ocean wave model** the Danish Meteorological Institute (DMI) runs operationally to forecast sea state over the North Atlantic, the North Sea and Baltic Sea, and Danish coastal waters. It computes the two-dimensional wave energy spectrum as a function of position, time, frequency and direction, and derives the distributed integrated parameters (wave height, period, direction, wind-sea/swell partitions) from that spectrum.

DMI runs WAM as a **successively nested chain of three domains** — a coarse North Atlantic parent, a North Sea–Baltic intermediate nest, and a fine Danish Waters nest. All three share the same model code, physics, cycle schedule, forcing chain, and 14-parameter output set, differing only in extent and resolution. They are presented together here because DMI documents and distributes them as a single model with three model areas.

DMI uses WAM for shipping forecasts, and reads it together with the [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) storm-surge forecasts when predicting maximum coastal water heights.

---

## Who runs it
- **Organization:** Danish Meteorological Institute (DMI)
- **Country / region:** Denmark

---

## What area it covers
- **Coverage:** North Atlantic, north-western European shelf seas, the North Sea and Baltic Sea, and the inner Danish waters
- **Domain details:** three nested regular latitude–longitude grids. All bounds and increments below are **live-verified from GRIB** (00 UTC run, 23 July 2026):

| Domain | Collection / prefix | Grid (lon × lat) | Longitude | Latitude | Δlon | Δlat | Approx. resolution |
|---|---|---|---|---|---|---|---|
| North Atlantic | `wam_natlant` / `WAM_NATLANT_SF` | 397 × 193 | 69.0°W – 30.0°E | 30.0°N – 78.0°N | 0.25° (15′) | 0.25° (15′) | ~16–28 km |
| North Sea–Baltic | `wam_nsb` / `WAM_NSB_SF` | 517 × 381 | 13.0°W – 30.0°E | 47.0°N – 66.0°N | 0.08333° (5′) | 0.05° (3′) | ~5.5 km |
| Danish Waters | `wam_dw` / `WAM_DW_SF` | 541 × 701 | 7.0°E – 16.0°E | 53.0°N – 60.0°N | 0.01667° (1′) | 0.01° (36″) | ~1.1 km |

Every domain uses a longitude increment exactly 5/3 of its latitude increment, which makes the grid close to isotropic in kilometres at mid-domain latitude. Each nest is exactly one-fifth the grid spacing of its parent.

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** Three separate regular lat-lon grids in a one-way successive nest (not a multi-grid mosaic, not unstructured)
- **Core wave model:** WAM Cycle 4.5
- **Horizontal resolution:** domain-dependent — see table above
- **Forecast length:** **132 hours (5.5 days)** — live-verified: 133 files per cycle, steps 0–132
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) — live-verified
- **Temporal output resolution:** 1 hour
- **Spectral discretisation (frequency bins, directional bins):** TBD — DMI's historical technical documentation describes 25 logarithmic frequency bins and 15° directional resolution, but this has not been confirmed for the current operational configuration

---

## Forcing and nesting
- **Wind forcing:** 10 m wind from DMI's [HARMONIE-AROME DINI](../../../nwp_models/regional/denmark/harmonie-dmi.md) run, with ECMWF IFS global forecasts beyond the HARMONIE range. DMI documents this same split for the sibling [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) chain as HARMONIE for the first 60 hours and ECMWF thereafter; the WAM chain is documented as sharing DKSS's forcing, and the 132 h forecast length necessarily extends well past HARMONIE DINI's 60 h. *(TBD — confirm the exact WAM changeover hour against DMI's WAM model page.)*
- **Ice forcing:** TBD — WAM Cycle 4.5 supports an ice mask, but the source and concentration threshold used by DMI are not documented in the open-data pages
- **Current forcing:** None documented. Despite DKSS being run on overlapping domains, no wave–current coupling is described for the open-data WAM chain. (TBD)
- **Nested inside / parent for:** the North Sea–Baltic domain is nested inside the North Atlantic domain and receives interpolated wave spectra from it; Danish Waters sits at the fine end of the same successive nest

---

## Data assimilation
- **Assimilates wave observations:** No wave-observation assimilation is documented for the operational DMI WAM chain.
- **Initialisation:** DMI's technical documentation describes the system as cold-started once and warm-started thereafter, each run initialised from the sea state calculated by the previous cycle. *(TBD — this comes from older DMI technical reports; not re-confirmed for the current configuration.)*

---

## What it provides
All three domains carry the **same 14 parameters**, live-verified against DMI's published parameter list. GRIB1 parameter numbers are given in `table2Version = 140`, centre `ekmi`:

| GRIB1 param | shortName | Description | Unit |
|---|---|---|---|
| 245 | `wind` | 10 m wind speed | m s⁻¹ |
| 249 | `dwi` | 10 m wind direction (from true north) | degrees |
| 229 | `swh` | Significant height of combined wind waves and swell | m |
| 231 | `pp1d` | Peak (dominant) wave period | s |
| 232 | `mwp` | Mean wave period | s |
| 221 | `mp2` | Mean zero-crossing wave period | s |
| 230 | `mwd` | Mean wave direction (from true north) | degrees |
| 234 | `shww` | Significant height of wind waves | m |
| 236 | `mpww` | Mean period of wind waves | s |
| 235 | `mdww` | Mean direction of wind waves | degrees |
| 237 | `shts` | Significant height of total swell | m |
| 239 | `mpts` | Mean period of total swell | s |
| 238 | `mdts` | Mean direction of total swell | degrees |
| 253 | `bfi` | Benjamin-Feir index | 0–1 (dimensionless) |

No directional spectra, no partitioned (primary/secondary) swell components, no Stokes drift.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** **GRIB edition 1** (not GRIB2 — the HARMONIE products in the same bucket are GRIB2)
- **File packaging:** one file per forecast time step, flat prefix, named `WAM_<AREA>_SF_<modelRun>_<validTime>.grib` (e.g. `WAM_NSB_SF_2026-07-23T000000Z_2026-07-23T060000Z.grib`)
- **Official download location:**
  - AWS S3 (no account, no key, no registration): `s3://dmi-opendata/forecastdata/WAM_NATLANT_SF/`, `.../WAM_NSB_SF/`, `.../WAM_DW_SF/` (region `eu-north-1`)
    `aws s3 ls --no-sign-request s3://dmi-opendata/forecastdata/WAM_NSB_SF/`
  - DMI Forecast STAC API (free API key required): `https://dmigw.govcloud.dk/v1/forecastdata/collections/wam_nsb`
  - DMI Forecast EDR API (free API key required): `https://dmigw.govcloud.dk/v1/forecastedr/collections/wam_nsb`

---

## Notes
- **Land is bitmap-masked, so `numberOfValues` is far below `Ni × Nj`.** Live-verified at the 00 UTC 23 July 2026 run: North Sea–Baltic carries 92,314 values of 196,977 grid points (47%), North Atlantic 47,397 of 76,621 (62%), Danish Waters 139,639 of 379,241 (37%). Readers that ignore the GRIB bitmap will misalign the field against the grid.
- **All WAM grids scan north-to-south.** The first grid point is the *northern* boundary (66°N for NSB, 78°N for NATLANT, 60°N for DW) and the last is the southern. This is the opposite of the sibling [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) grids, which scan south-to-north. Code that handles both DMI marine models needs to respect the scanning-mode flags rather than assume a shared convention.
- **GRIB1, not GRIB2.** Parameters are identified by `indicatorOfParameter` in local table version 140 rather than by discipline/category/number. eccodes resolves this table correctly for WAM (unlike DKSS — see that entry).
- **Ice-covered points.** WAM Cycle 4.5 does not produce wave parameters where ice cover is assumed present; expect masked or zero-energy points in the northern reaches of the North Atlantic domain in winter. Not verified for this dataset.
- **S3 retention is longer than the documented API window.** DMI documents 48 hours of model runs in the STAC API, but the S3 bucket was live-measured on 23 July 2026 holding cycles back to 20 July 00 UTC — a rolling window of roughly 3¼ days. Treat the extra depth as convenience, not a guarantee.
- **Publication latency.** DMI documents the complete model as available at approximately +2h45m (`wam_natlant`, `wam_nsb`) and +3h00m (`wam_dw`) after the nominal cycle time; observed S3 object timestamps for the 00 UTC 23 July 2026 run match (02:38–03:12 UTC). Downstream marine models arrive later than HARMONIE because they consume its output.
- **Relationship to other models:** [HARMONIE-AROME DMI (DINI/IG)](../../../nwp_models/regional/denmark/harmonie-dmi.md) is the meteorological driver. [DKSS](../../../storm_surge_models/regional/denmark/dkss.md) is the sibling storm-surge chain sharing the same forcing and schedule; DMI combines WAM and DKSS output when forecasting maximum coastal water height.
- **DMI's WAM-EPS is not this system and is not in the catalog.** DMI has developed a pre-operational wave ensemble (WAM-EPS, forced by the DMI-COMEPS/NEA atmospheric ensemble, run at half the operational resolution). It is published only as rendered graphics on `ocean.dmi.dk` and is not present in the open-data feed, so it falls outside scope.
- **Not the Copernicus Marine Baltic WAM.** DMI also contributes to Copernicus Marine Baltic wave products (`BALTICSEA_ANALYSISFORECAST_WAV_003_010`), which are a separate WAM Cycle 4.7 system on a 1 nm grid with different forcing and coupling, distributed as NetCDF under the Copernicus Marine licence. Those are documented separately.

---

## Official documentation
- DMI: *Forecast Data Wave Model (WAM)*
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-wave-model-wam
- DMI: *Wave Model (WAM) EDR API parameter list*
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-wave-model-wam-edr-api-parameter-list
- DMI: *Forecast Data Availability*
  https://opendatadocs.dmi.govcloud.dk/Data/Forecast_Data_Availability
- DMI Open Data documentation portal
  https://opendatadocs.dmi.govcloud.dk/en/Data/Forecast_Data
- AWS Open Data Registry: *Danish Meteorological Institute (DMI) Open Data Forecasts*
  https://registry.opendata.aws/dmi-opendata/
- DMI ocean model background: *The DMI wave model*
  https://ocean.dmi.dk/validations/waves/background.uk.php
- WAMDI Group (1988), *The WAM Model — A Third Generation Ocean Wave Prediction Model*, J. Phys. Oceanogr., 18, 1775–1810.
