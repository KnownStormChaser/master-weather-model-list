# HARMONIE-AROME DINI-EPS derived products (DMI)

## What this model is
This entry covers the **publicly distributed probabilistic products** of the UWC-West HARMONIE-AROME **DINI ensemble prediction system**, as published by the Danish Meteorological Institute.

**Read this first: no raw ensemble members are published.** DMI distributes three collections of *derived* ensemble statistics — ensemble means, percentiles, and threshold-exceedance probabilities — computed from the 31-member DINI-EPS. Users who need individual members must obtain them elsewhere; the closest open source is KNMI's [HARMONIE-AROME EPS Europe (P4a)](../netherlands/harmonie-eps-knmi-eu.md), which publishes members but on a coarser regridded grid.

The compensating advantage is resolution. DMI publishes these products on the **native 1906 × 1606 / 2 km Lambert DINI grid** — the model's own integration grid, not a regridded subset. For probabilistic fields over north-western Europe at convection-permitting scale, this is the finest openly available packaging of the UWC-West DINI ensemble.

---

## Who runs it
- **Organization:** Danish Meteorological Institute (DMI), as part of the United Weather Centres-West (UWC-West) partnership with KNMI (Netherlands), the Icelandic Met Office, and Met Éireann (Ireland)
- **Country / region:** Denmark (distribution); multi-national (operations)
- **Computing infrastructure:** UWC-West "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík

---

## What area it covers
- **Coverage:** the DINI domain — Denmark, Iceland, the Netherlands, Ireland and the surrounding seas, extending from East Greenland to southern Italy at the corners
- **Grid (live-verified from GRIB, 02 UTC cycle, 23 July 2026):**
  - Projection: **Lambert conformal conic**, `LaD = 55.5°N`, `LoV = 8.0°W`, southern pole at 90°S / 0°E
  - Grid dimensions: **1906 × 1606** = 3,061,036 points
  - Grid spacing: **2000 m × 2000 m** (`DxInMetres` / `DyInMetres`)
  - First grid point: 39.671°N, 25.422°W
  - Earth shape: sphere of radius 6,371,229 m (`shapeOfTheEarth = 6`)

This is the same projection definition as the deterministic [HARMONIE-DMI DINI](../../../nwp_models/regional/denmark/harmonie-dmi.md) product.

---

## Basic details
- **Model type:** Ensemble NWP (regional, limited-area) — derived products only
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral core, AROME physics, SURFEX surface scheme), cycle 43h, UWC-West configuration
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (2 km, deep convection explicitly resolved)
- **Ensemble size:** **31 — 30 perturbed members plus 1 control.** Live-verified: every derived-product GRIB message carries `numberOfForecastsInEnsemble = 31`.
- **Horizontal resolution:** 2 km, native grid (no regridding)
- **Vertical levels:** 90 hybrid levels in the underlying model; the derived products distribute surface and near-surface fields only
- **Forecast length:** **54 hours** — live-verified
- **Update frequency / cycles:** **hourly** — a new cycle every hour, 00–23 UTC, live-verified. This is materially more frequent than DMI's deterministic DINI product, which is collected every third hour.
- **Temporal output resolution:** 1 hour for means and percentiles; **3 hours** for probabilities (19 steps, 0–54 h)

---

## Data assimilation
- **Data assimilation:** Yes — the UWC-West DINI production runs 3D-Var with a three-hour assimilation window and a one-hour cut-off, with observation processing via ECMWF's SAPP across the four partner services.

---

## Initial and boundary conditions
- **Initial conditions:** UWC-West HARMONIE-AROME analysis on the DINI domain
- **Boundary conditions:** ECMWF IFS — the control is coupled to IFS-HRES and the 30 perturbed members to IFS-ENS members 001–030 (a 1+30 LBC configuration)

---

## Perturbations and design
- **Initial condition perturbations:** Inherited from the driving IFS-ENS boundary and initial perturbations. (TBD — the specific UWC-West perturbation method is not documented in DMI's open-data pages.)
- **Model/physics perturbations:** TBD
- **Stochastic schemes:** TBD

### Time-lagged composition (important)
The UWC-West DINI-EPS is a **continuous ensemble**: each hourly cycle integrates the control plus **5 perturbed members**, and the full 30-member ensemble is composed across **six consecutive hourly cycles**. This is documented in detail for the same underlying production in the [KNMI P4a entry](../netherlands/harmonie-eps-knmi-eu.md), where member IDs were observed rotating over a 6-hour cycle.

Every DMI derived-product message nevertheless reports `numberOfForecastsInEnsemble = 31`, not 6. The straightforward reading is that DMI computes its statistics over the **full 31-member time-lagged composite**, drawing on the five preceding hourly cycles rather than on the six members integrated in the current hour alone. That has a practical consequence: the members contributing to a given product were initialised at up to six different analysis times, so lead times are not uniform across the ensemble.

*This is a strong inference from the GRIB-encoded member count plus the documented UWC-West design, not a statement DMI publishes. Flagged as **TBD** pending confirmation from DMI.*

---

## What it provides

Three collections, each with a different parameter set. All fields live-verified from the 02 UTC cycle, 23 July 2026.

### `HARMONIE_DINI_EPS_MEANS` — ensemble mean
Hourly, steps 0–54 (55 files per cycle). **8 fields**, all with `derivedForecast = 0` (unweighted ensemble mean over all members), GRIB2 PDT 2 — or PDT 12 for the time-interval field:

| Field | Level | Unit |
|---|---|---|
| 2 m temperature | 2 m above ground | K |
| 2 m dewpoint temperature | 2 m above ground | K |
| 2 m relative humidity | 2 m above ground | % |
| 10 m wind speed | 10 m above ground | m s⁻¹ |
| Total cloud cover | surface | 0–1 |
| Mean sea level pressure | mean sea level | Pa |
| Maximum 10 m wind gust (PDT 12) | 10 m above ground | m s⁻¹ |
| Total column water vapour | entire atmosphere | kg m⁻² |

**Only the mean is published — no ensemble spread or standard deviation field exists in this collection.** Users needing spread must derive it from the percentile collection.

### `HARMONIE_DINI_EPS_PERCENTILES` — percentile distribution
Hourly, steps 0–54 (55 files per cycle). **63 messages = 9 fields × 7 percentiles**, GRIB2 PDT 6 (PDT 10 for the gust field). Percentile values: **0, 10, 25, 50, 75, 90, 100** — i.e. minimum, p10, p25, median, p75, p90, maximum.

Fields: 2 m temperature · 2 m dewpoint · 2 m relative humidity · 10 m wind speed · total cloud cover · maximum 10 m wind gust · **visibility** · **cloud base height** · total column water vapour.

Note the asymmetry against the means collection: percentiles **add** visibility and cloud base, but **omit** mean sea level pressure.

### `HARMONIE_DINI_EPS_PROBABILITIES` — threshold exceedance
**3-hourly**, steps 0–54 (19 files per cycle). **6 fields**, GRIB2 PDT 5 (PDT 9 for gust fields), values 0–1:

| Probability field | Encoding |
|---|---|
| P(2 m temperature < 273 K) | `probabilityType = 0`, lower limit 273 |
| P(10 m wind speed > 25 m s⁻¹) | `probabilityType = 1`, upper limit 25 |
| P(10 m wind speed > 33 m s⁻¹) | `probabilityType = 1`, upper limit 33 |
| P(max 10 m gust > 25 m s⁻¹) | `probabilityType = 1`, upper limit 25 |
| P(max 10 m gust > 33 m s⁻¹) | `probabilityType = 1`, upper limit 33 |
| P(visibility < 100 m) | `probabilityType = 0`, lower limit 100 |

The thresholds map onto operational warning criteria: 25 m s⁻¹ is the storm threshold and 33 m s⁻¹ the hurricane-force threshold in Danish marine warnings; 273 K is the freezing point; 100 m visibility is a dense-fog criterion.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB edition 2, centre `ekmi`
- **File packaging:** one file per forecast time step, flat prefix, named `HARMONIE_DINI_EPS_<PRODUCT>_<modelRun>_<validTime>.grib`. Files are large — a single percentiles time step is roughly 450–700 MB, a means step 55–74 MB, a probabilities step 40–141 MB.
- **Official download location:**
  - AWS S3 (no account, no key, no registration): `s3://dmi-opendata/forecastdata/HARMONIE_DINI_EPS_MEANS/`, `.../HARMONIE_DINI_EPS_PERCENTILES/`, `.../HARMONIE_DINI_EPS_PROBABILITIES/` (region `eu-north-1`)
    `aws s3 ls --no-sign-request s3://dmi-opendata/forecastdata/HARMONIE_DINI_EPS_MEANS/`
  - DMI Forecast STAC API (free API key required): `https://dmigw.govcloud.dk/v1/forecastdata/collections/harmonie_dini_eps_means`
  - DMI Forecast EDR API (free API key required): `https://dmigw.govcloud.dk/v1/forecastedr/collections/harmonie_dini_eps_means`

---

## Notes
- **Derived products only — this is a scope-relevant caveat, not a footnote.** The catalog's usual expectation for an ensemble entry is raw member data. This dataset does not provide it. It is included because the files are raw gridded GRIB2 (not rendered imagery, not viewer-only) and are freely and openly licensed, which satisfies the format and access criteria; but anyone arriving here looking for members will need [KNMI P4a](../netherlands/harmonie-eps-knmi-eu.md) instead.
- **Native-grid advantage.** At 1906 × 1606 / 2 km this is the model's own integration grid. KNMI's P4a distribution of the same underlying run is regridded to a rotated ~0.05° grid (676 × 564). For anyone working at convection-permitting scale, the DMI packaging preserves detail that the KNMI one does not — at the cost of file size and of losing the members.
- **Parameter sets differ between the three collections.** Means carry MSLP but not visibility or cloud base; percentiles carry visibility and cloud base but not MSLP; probabilities carry a bespoke six-field warning-threshold set. Do not assume a shared parameter list.
- **Probabilities are 3-hourly while means and percentiles are hourly.** Live-verified: 19 files per probabilities cycle versus 55 for the other two.
- **Same underlying run as several sibling distributions.** The DINI-EPS integration behind these products is the same UWC-West production distributed by KNMI as [`harmonie_arome_cy43_p4a`](../netherlands/harmonie-eps-knmi-eu.md) and, in deterministic form, as DMI's [DINI](../../../nwp_models/regional/denmark/harmonie-dmi.md), KNMI's P1/P3/P5, and Met Éireann's [Irish distribution](../../../nwp_models/regional/ireland/harmonie-arome-ireland.md). These are repackagings of one model state, not independent forecasts.
- **Deterministic counterpart:** DMI's [HARMONIE-AROME DINI](../../../nwp_models/regional/denmark/harmonie-dmi.md), which is the DINI-EPS control member collected every third hour.
- **Not MEPS.** Despite being run by a Nordic operator, DMI's HARMONIE production belongs to UWC-West, not MetCoOp. [MEPS](../../../nwp_models/regional/norway/meps.md) is a separate Nordic HARMONIE-AROME ensemble that does not include DMI.
- **Cycle 46 upgrade pending.** The UWC-West cy43 production is scheduled for replacement by HARMONIE-AROME cycle 46h1.1.1 in **October 2026**, which changes output encoding from FA to FullPOS (WMO) GRIB2 among other changes. Re-verify this entry's GRIB structure after the changeover.
- **S3 retention.** Live-measured on 23 July 2026, the EPS prefixes held cycles back to 19 July 21 UTC — roughly 3½ days, longer than the 48 h documented for the STAC API.
- **Publication latency.** Not documented by DMI for the EPS collections. Observed for the 02 UTC 23 July 2026 cycle: means complete at ~05:24 UTC (+3h24m), probabilities at ~05:38 UTC (+3h38m).

---

## Official documentation
- DMI: *Forecast Data*
  https://www.dmi.dk/friedata/dokumentation/forecast-data
- DMI: *Forecast Data Weather Model (HARMONIE) for DINI and IG*
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-weather-model-harmonie-for-dini-and-ig
- DMI: *Forecast Data STAC-API*
  https://www.dmi.dk/friedata/dokumentation/forecast-data-stac-api
- DMI Open Data documentation portal
  https://opendatadocs.dmi.govcloud.dk/en/Data/Forecast_Data
- AWS Open Data Registry: *Danish Meteorological Institute (DMI) Open Data Forecasts*
  https://registry.opendata.aws/dmi-opendata/
- Bengtsson et al. (2017), *The HARMONIE–AROME Model Configuration in the ALADIN–HIRLAM NWP System*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
