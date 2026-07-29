# ItaliaMeteo Seasonal Downscaling (SEAS5 0.1° Italy)

## What this model is
This is **not an independent seasonal prediction system**. It is Agenzia ItaliaMeteo's **statistically downscaled and bias-corrected rendering of ECMWF SEAS5** over Italy, distributed as raw gridded GRIB with all ensemble members retained.

The downscaling is performed by **quantile mapping** against ERA5-Land (and ERA5 interpolated over marine areas) as the reference dataset, taking the SEAS5 output from roughly 1° to **0.1° (≈10 km)** — the native ERA5-Land grid spacing. The procedure delivers calibrated daily fields of maximum and minimum 2 m temperature and total precipitation. MeteoHub markets it as the "Seasonal Forecast | 0.1° downscaling" dataset and presents the map products under the name "previsioni mensili" (monthly forecasts).

Italy has no national dynamical seasonal forecasting system of its own in this chain. The Italian contribution to the C3S seasonal multi-system is **CMCC SPS3.5** (`system=35`), an unrelated system operated by CMCC. This product's physics come entirely from [SEAS5](../../global/eu/seas5.md); ItaliaMeteo's contribution is the calibration and downscaling layer on top.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo (methodology and production); MeteoHub platform operated by ItaliaMeteo with Cineca
- **Country / region:** Italy
- **Coordinating body / programme:** None directly. The driving forecasts, reforecasts and reference reanalyses are all obtained from the Copernicus Climate Data Store (C3S), so the product sits downstream of C3S without being a C3S contribution itself.

---

## What area it covers
- **Coverage:** Italy and surrounding Mediterranean / Alpine area
- **Domain details:** Regular lat-lon grid, **35°N–48°N, 6°E–19°E**, 0.1° spacing, **131 × 131 = 17,161 grid points** (live-verified from GRIB section 2). Advertised domain on the MeteoHub dataset card matches exactly.

---

## Basic details
- **Model type:** Statistically downscaled long-range ensemble — post-processed derivative of a single coupled system
- **Model system / core:** ECMWF SEAS5, retrieved via C3S. Live-verified from the preserved ECMWF MARS local definition (localDefinitionNumber 15):

  | Key | Value | Meaning |
  |---|---|---|
  | `class` | `c3` | Copernicus Climate Change Service |
  | `stream` | `mmsf` | multi-model seasonal forecast, real-time |
  | `system` | `51` | ECMWF SEAS5 (C3S labelling) |
  | `method` | `1` | — |
  | `origin` | `ecmf` | ECMWF |

- **Range class:** Seasonal (monthly-to-seasonal; the distributed horizon is shorter than SEAS5's own)
- **Forecast length:** **4 calendar months** — the initialization month plus the following three
- **Initialization cadence:** Monthly, following the C3S SEAS5 release cycle
- **Ensemble generation:** Inherited from SEAS5 (burst ensemble, single start date). No perturbations are added by the downscaling.
- **Ensemble size:** **25 members** (`perturbationNumber` 1–25, live-verified)
- **Temporal output resolution:** Daily
- **Output aggregation levels:** Daily fields only in the raw GRIB distribution. The monthly medians, anomalies and provincial box plots shown on the MeteoHub map viewer are derived downstream and are **not** part of this dataset.

---

## Downscaling method
*(This section replaces the template's "Coupled configuration" block. The coupled Earth-system configuration — IFS Cy43r1 atmosphere, NEMO ocean, LIM2 sea ice, HTESSEL land — belongs to the parent system and is documented in [`seas5.md`](../../global/eu/seas5.md). Nothing in that stack is re-run here.)*

- **Technique:** Statistical downscaling with bias correction, via **quantile mapping**
- **Reference dataset:** ERA5-Land, with ERA5 interpolated for marine areas
- **Calibration period:** SEAS5 reforecasts for the 30 years **1993–2022**
- **Resolution change:** ~1° (C3S-distributed SEAS5 grid) → 0.1° (ERA5-Land grid)
- **Applied to:** individual SEAS5 ensemble members, not to the ensemble mean — the member structure survives into the distributed product
- **Whether quantile mapping is fitted per-member or on the pooled member distribution:** **TBD** — not stated in the published methodology

---

## Initialization
Not applicable to this product directly. Atmosphere, ocean, sea-ice and land initialization are inherited wholesale from the parent SEAS5 run; see [`seas5.md`](../../global/eu/seas5.md).

**The GRIB files do not record which SEAS5 run they came from.** See *File and encoding structure* below — the initialization month is carried only by the directory path.

---

## Hindcasts (reforecasts)
- **Hindcast period:** 1993–2022 (30 years), used to fit the quantile-mapping transfer functions
- **Reference climatology period:** 1993–2022, the same window, used for the anomaly products on the map viewer
- **Distributed alongside forecasts?** **No.** Only real-time forecast months are published. The downscaled reforecasts, if retained, are not on the open server.
- **Ensemble size note:** SEAS5 real-time carries 51 members while its reforecast carries 25. This product distributes **25**. That the count matches the hindcast configuration rather than the real-time one is suggestive — quantile mapping has to be calibrated against the reforecast distribution — but ItaliaMeteo does not document whether a 25-member subset is selected deliberately or which SEAS5 member IDs it corresponds to. **TBD.**

---

## What it provides

Three daily variables, each for every member and every day of the validity month:

| GRIB shortName | Parameter | Units | ECMWF param |
|---|---|---|---|
| `mx2t24` | Maximum temperature at 2 m in the last 24 hours | K | `51.128` |
| `mn2t24` | Minimum temperature at 2 m in the last 24 hours | K | `52.128` |
| `tp` | Total precipitation | m | `228.128` |

Message count per file is `3 variables × 25 members × days-in-month` — 2,325 messages for a 31-day month, 2,250 for 30 days, 2,100 for February 2026.

No pressure-level fields, no wind, no other surface variables. This is a deliberately narrow three-variable product aimed at agricultural, water-management and energy users.

---

## File and encoding structure

### Layout
```
/nwp/SEASONAL/{YYYY}_{MM}/downscaled_seasonal_{yyyy}_{mm}.grib
        ^^^^^^^^^^^^^^^^                        ^^^^^^^^^^^^^
        initialization month                    validity month
```

Each initialization directory holds exactly four files, at leads 0 through 3 months. Verified across all nine initialization months present:

| Init dir | Validity months |
|---|---|
| `2025_11/` | 2025_11, 2025_12, 2026_01, 2026_02 |
| `2026_07/` | 2026_07, 2026_08, 2026_09, 2026_10 |

The archive is genuinely lead-resolved, not duplicated: comparing member 1's `mx2t24` for 15 July 2026 from the June initialization against the July initialization gives field means of 303.22 K and 302.58 K, differing by up to 14.64 K at individual grid points.

### The initialization date is not in the GRIB
This is the single biggest trap. Within a file, `dataDate` is set to **the day being described**, incrementing daily across the month, with a constant `step = 24`:

```
dataDate 20260701, step 24, validityDate 20260702
dataDate 20260702, step 24, validityDate 20260703
...
```

There is no reference to the SEAS5 start date anywhere in the message. Two consequences:

1. **The initialization month exists only in the directory path.** Files moved or renamed out of their parent directory lose their lead information irrecoverably, and two files with identical names from different initializations will collide.
2. **`validityDate` is one day later than the day the field describes.** The record stamped 2 July holds the 24-hour maximum, minimum and accumulation for 1 July. Index on `dataDate`, not `validityDate`.

### Time-range encoding is wrong
`timeRangeIndicator = 10` (P1 = 0, P2 = 24) marks every message as an **instantaneous** value at +24 h. It isn't: `mx2t24` and `mn2t24` are 24-hour extrema and `tp` is a 24-hour accumulation. eccodes accordingly reports `stepType = instant` for all three. Any pipeline that trusts `stepType` — for example to decide whether to de-accumulate `tp` — will mishandle these fields. `tp` is already a per-day accumulation and must **not** be differenced.

### Other encoding notes
- **GRIB edition 1**, ECMWF local definition 15, `table2Version = 128`
- `grid_simple` packing, `bitsPerValue = 16`
- `numberOfForecastsInEnsemble = 0` — unset; use `perturbationNumber` to identify members
- File sizes track month length: ~80.1 MB (31 days), ~77.5 MB (30 days), ~72.3 MB (28 days)

### Reading the data
The operator ships a short recipe at `/nwp/SEASONAL/data_preprocessing_instructions.txt`, which opens the files with `xarray` + `cfgrib` and notes the expected array shape as `(ensemble_members, days_in_month, lat, lon)`:

```python
import xarray as xr
ds = xr.open_dataset("downscaled_seasonal_2026_07.grib", engine="cfgrib")
data = {var: ds[var].values for var in ds.data_vars}   # mx2t24, mn2t24, tp
```

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (attribution required). The MeteoHub dataset card states `License: CCBY4.0` with `Attribution: Agenzia ItaliaMeteo`. Note this attribution string differs from the one on [WW3-MEDITA](../../../wave_models/regional/italy/ww3-medita.md) (`ItaliaMeteo-ARPAE`), consistent with ARPAE having no role in this chain. See https://meteohub.agenziaitaliameteo.it/app/license
- **Upstream licence note:** the driving SEAS5 forecasts, reforecasts and ERA5/ERA5-Land reanalyses are all Copernicus products obtained through the CDS. Users redistributing this data onward should expect Copernicus attribution obligations to travel with it alongside ItaliaMeteo's CC BY 4.0 terms.
- **Is the data downloadable?** Yes — anonymous HTTP, no registration, no API key
- **Data formats:** GRIB (edition 1)
- **Official download location:**
  https://meteohub.agenziaitaliameteo.it/nwp/SEASONAL/
- **Preprocessing instructions:**
  https://meteohub.agenziaitaliameteo.it/nwp/SEASONAL/data_preprocessing_instructions.txt
- **Dataset metadata API:**
  https://meteohub.agenziaitaliameteo.it/api/datasets/SEASONAL — returns `{"id": "SEASONAL", "name": "Seasonal Forecast | 0.1° downscaling", "category": "FOR", "format": "grib", "source": "nwp", "is_public": true}`
- **Map viewer (derived monthly medians and anomalies, not this dataset):**
  https://meteohub.agenziaitaliameteo.it/ui/content/monthly-forecast

### Archive depth and publication latency
Unlike the WW3-MEDITA tree on the same server, this archive is **complete and not rolling**. All nine initialization months from 2025_11 onward were present and full (4 files each) as of 2026-07-28.

Publication timing is irregular and not documented. <cite index="11-1">The ECMWF contribution to C3S seasonal forecasts is released monthly at 12 UTC on the 6th</cite>, but observed directory creation times lag that by anywhere from about three days to about three weeks. The June and July 2026 initialization files both carry write timestamps of 27 July 2026, later than the directory creation times, indicating that months are occasionally reprocessed and rewritten in place. **Do not assume a file fetched once is final** — re-check the mtime if reproducibility matters.

### Not in the open data catalog
`SEASONAL` is **absent from the ItaliaMeteo CKAN portal** (`dati.agenziaitaliameteo.it`); searches for `seasonal`, `stagionali` and `downscaling` all return zero results. The CC BY 4.0 declaration rests on the MeteoHub dataset card and platform licence page rather than a formal open-data catalog record — the same pattern as WW3-MEDITA and MOLOCH_AIM.

---

## Notes
- **Category placement.** This entry uses `climate-model.template.md` but is not a long-range *coupled* system, and the template's **Coupled configuration** and **Initialization** blocks have been replaced by pointers to [`seas5.md`](../../global/eu/seas5.md) rather than filled with TBDs. The repository already admits statistically post-processed forecast products as entries (NBM, NAEFS, 557th WW GEPS), so the precedent is established; what is new here is a post-processing layer over a *seasonal* system rather than a medium-range one.
- **Why this warrants a separate entry from SEAS5.** Per the repository's entry-granularity convention, it differs from the parent in domain (Italy vs global), resolution (0.1° vs 1° as distributed), operator (ItaliaMeteo vs ECMWF), access channel (open HTTP vs CDS with registration and licence acceptance), and licence (CC BY 4.0 vs the Copernicus licence). It is also a genuinely different *product*: bias-corrected against ERA5-Land, not raw model output.
- **Access-route contrast worth knowing.** Getting member-level daily SEAS5 data out of the CDS requires registration, licence acceptance, and dealing with the seasonal-forecast GRIB layout. This product hands over 25 members of daily calibrated data for Italy over plain HTTP with no account. For users whose domain is Italy and whose variables are temperature and precipitation, it is markedly the easier route — at the cost of accepting ItaliaMeteo's calibration choices.
- **The three-variable limit is the main constraint.** No wind, no radiation, no pressure levels, no soil variables. Users needing anything beyond daily Tmax / Tmin / precipitation must go to the CDS for the parent system.
- **Only the current operational stream is published.** The downscaled 1993–2022 reforecasts that the calibration depends on are not distributed, so users cannot reproduce the calibration or compute their own anomalies against a matching downscaled climatology from this server alone.

---

## Official documentation
- MeteoHub monthly forecast methodology (Italian): https://meteohub.agenziaitaliameteo.it/ui/content/monthly-forecast?lang=it
- ItaliaMeteo monthly forecasts (English): https://www.agenziaitaliameteo.it/en/meteorology/forecasts/monthly-forecasts/
- ItaliaMeteo announcement of the monthly forecast maps: https://www.agenziaitaliameteo.it/agenzia/news-e-gallery/news/disponibili-sul-sito-le-mappe-di-previsione-mensile-dellagenzia/
- MeteoHub release notes adding the downloadable seasonal dataset: https://www.agenziaitaliameteo.it/agenzia/approfondimenti/news/online-la-nuova-release-di-meteohub-disponibili-nuove-mappe-e-dataset-scaricabili/
- MeteoHub user guide (web interface, REST API, CLI client): https://meteohub.agenziaitaliameteo.it/ui/user-guide
- Parent system: [SEAS5](../../global/eu/seas5.md)
- C3S seasonal multi-system description: https://confluence.ecmwf.int/display/CKB/Description+of+the+C3S+seasonal+multi-system

---

*Live-verified against initialization months 2025_11 through 2026_07 on 2026-07-28/29.*
