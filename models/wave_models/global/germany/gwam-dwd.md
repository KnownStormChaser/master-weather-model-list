# GWAM (Global Wave Model)

## What this model is
GWAM is the **global deterministic ocean wave forecast model** operated by Deutscher Wetterdienst (DWD). It is the global member of DWD's three-tier wave forecasting chain (GWAM → EWAM → CWAM), which parallels the ICON atmospheric model chain (ICON → ICON-EU → ICON-D2).

GWAM is a configuration of **WAM**, the third-generation spectral wave model developed by the WAMDI Group. It integrates the wave energy balance equation without ad hoc assumptions about spectral shape, resolving wave growth from wind momentum flux, propagation (advection and refraction), nonlinear wave–wave energy transfer, and dissipation through whitecapping and internal friction. DWD has run WAM operationally since 1992.

Internally the model carries a full two-dimensional wave energy spectrum at each sea point. Because the spectra are far too voluminous to distribute, DWD publishes a set of **integral parameters** derived from them, partitioned into wind-sea and swell regimes.

GWAM provides global sea-state guidance and supplies lateral boundary conditions to [EWAM](../../regional/germany/ewam-dwd.md).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Global oceans (excluding the highest latitudes)
- **Domain details:** single global regular latitude–longitude grid, **1440 × 699** points at **0.25° × 0.25°**, spanning **89.25°N – 85.25°S** and **0.00°E – 359.75°E**, scanning north to south. Live-verified from GRIB (00 UTC run, 2026-07-28). Of 1,006,560 grid points, **686,000 are sea points**; the remaining 320,560 are masked via a GRIB bitmap (`missingValue = 9999`).

---

## Basic details
- **Model type:** Deterministic global wave model
- **Grid system:** Single global regular lat-lon grid (no multi-grid mosaic, no unstructured mesh)
- **Core wave model:** **WAM** — third-generation spectral wave model (WAMDI Group). *Specific WAM cycle version not published by DWD* (**TBD**)
- **Spectral resolution:** 36 directions × 30 frequencies, covering wave periods from **1.5 s to 24 s**
- **Horizontal resolution:** 0.25° (~28 km)
- **Forecast length:** **174 h** (7¼ days)
- **Update frequency / cycles:** 2× daily (**00** and **12 UTC**)
- **Temporal output resolution:** **3-hourly** — 59 steps, 000 to 174 h. (Note: EWAM and CWAM are hourly; GWAM alone is 3-hourly.)

---

## Forcing and nesting
- **Wind forcing:** [ICON Global](../../../nwp_models/global/germany/icon-global.md) 10 m winds (~13 km), analyzed and forecast
- **Ice forcing:** Not documented by DWD. The GRIB bitmap is **identical at 000 h and 174 h**, confirming it is a static land–sea mask rather than a time-varying ice mask — so any ice treatment happens inside the model rather than in the distributed mask (**TBD**)
- **Current forcing:** None documented for GWAM. (Current and water-level coupling has been developed at the coastal end of the chain, in CWAM.)
- **Parent for:** [EWAM](../../regional/germany/ewam-dwd.md) — GWAM supplies boundary conditions to the European domain, which in turn feeds [CWAM](../../regional/germany/cwam-dwd.md)
- **Nested inside:** N/A (global parent)

---

## Data assimilation
- **Assimilates wave observations:** **TBD.** DWD's current open-data documentation makes no mention of wave data assimilation in GWAM, and describes the forecast as depending directly on ICON 10 m wind forcing. Published literature from the ERS/Envisat era describes a DWD altimeter SWH assimilation scheme with along-track quality control, in which analyzed SWH fields were used to update the prognostic wave spectra. Whether that scheme is still active in the current operational GWAM is not confirmed by any DWD source located, and is flagged here rather than asserted.
- **Observation sources (if active):** satellite altimeter significant wave height (historically) (**TBD**)
- **Method / cadence:** first-guess merge with along-track altimeter SWH, followed by spectral update (historically) (**TBD**)

---

## What it provides
Thirteen integral parameters, one GRIB2 file per parameter per time step. Field list and GRIB encoding live-verified on the 2026-07-28 00 UTC run:

| Group | Parameter | DWD short name | GRIB `shortName` | Discipline/Cat/Num | Unit |
|---|---|---|---|---|---|
| Wind | 10 m wind speed | `SP_10M` | `10si` | 0/2/1 | m s⁻¹ |
| Wind | 10 m wind direction | `DD_10M` | `10wdir` | 0/2/0 | ° true |
| Total spectrum | Significant wave height (H<sub>m0</sub>) | `SWH` | `swh` | 10/0/3 | m |
| Total spectrum | "Energy" wave period (T<sub>m−1,0</sub>) | `TM10` | `mwp` | 10/0/15 | s |
| Total spectrum | Mean wave direction | `MWD` | `mwd` | 10/0/14 | ° true |
| Wind sea | Significant wave height | `SHWW` | `shww` | 10/0/5 | m |
| Wind sea | Mean wave period (T<sub>m01</sub>) | `MPWW` | `mpww` | 10/0/6 | s |
| Wind sea | Peak wave period (T<sub>p</sub>) | `PPWW` | `pp1dw` | 10/0/35 | s |
| Wind sea | Mean wave direction | `MDWW` | `wvdir` | 10/0/4 | ° true |
| Swell | Significant wave height | `SHTS` | `shts` | 10/0/8 | m |
| Swell | Mean wave period (T<sub>m01</sub>) | `MPTS` | `mpts` | 10/0/9 | s |
| Swell | Peak wave period (T<sub>p</sub>) | `PPTS` | `pp1ds` | 10/0/36 | s |
| Swell | Mean wave direction | `MDTS` | `swdir` | 10/0/7 | ° true |

**Partitioning convention:** the wind-sea partition comprises waves propagating within a defined directional interval whose phase speeds do not exceed the wind speed; the swell partition is the entire remainder of the spectrum. Only a single swell partition is distributed — there is no first/second/third swell split as in some other global wave systems.

**No wave spectra are distributed** — only these integral parameters.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** DWD Open Data terms under **GeoNutzV** (Germany's federal geodata usage ordinance) — effectively CC BY 4.0. Commercial use permitted; **attribution to DWD is mandatory** ("Source: Deutscher Wetterdienst"). Note that DWD logs client IP addresses for up to 7 days for server operations.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, **bzip2-compressed** (`.grib2.bz2`), `grid_simple` packing, 11 bits per value
- **Official download location:**
  https://opendata.dwd.de/weather/maritime/wave_models/gwam/grib/
  - **Path template:** `/weather/maritime/wave_models/gwam/grib/{HH}/{param}/` — `{HH}` = cycle (`00` / `12`), `{param}` = lowercase parameter folder (`swh`, `tm10`, `mwd`, `shww`, `mpww`, `ppww`, `mdww`, `shts`, `mpts`, `ppts`, `mdts`, `sp_10m`, `dd_10m`)
  - **Filename convention:** `GWAM_{PARAM}_{YYYYMMDDHH}_{VVV}.grib2.bz2`
    - `{PARAM}` — uppercase parameter name (`SWH`, `DD_10M`, …)
    - `{YYYYMMDDHH}` — run initialization time (hour is `00` or `12`)
    - `{VVV}` — forecast lead time in hours, `000`, `003`, … `174`
  - **Example:** `.../gwam/grib/00/swh/GWAM_SWH_2026072800_024.grib2.bz2`
- **File size:** ~300 KB compressed / ~1.0 MB uncompressed per SWH field; ~150 KB–1.1 MB compressed depending on parameter. **~7.2 MB per time step** across all 13 parameters, **~425 MB per full run**.
- **Retention:** short rolling window — **two runs per cycle directory** (~48 h) at check time. No archive is offered on the open-data server; users needing history must self-archive.
- **New-data notifications:** none. Poll the directory tree, or watch `content.log.bz2` at `/weather/maritime/`, which lists recent file additions.

---

## Notes
- **Verified 2026-07-28.** Directory structure, file naming, step list, grid geometry, parameter encoding, and bitmap behaviour confirmed by direct inspection of live GRIB files from the 00 UTC run.
- **Download path changed.** This entry previously listed `https://opendata.dwd.de/weather/marine/wave/`. That path now returns **404**, as does its parent `/weather/marine/`. The wave models live under `/weather/maritime/wave_models/`.
- **The core model is WAM, not WAVEWATCH III.** The "WAM" in GWAM, EWAM, and CWAM is the model name itself. DWD has run WAM operationally since 1992; the model is maintained at Helmholtz-Zentrum Hereon and is the same lineage used by ECMWF's ECWAM and in Copernicus Marine wave production. Earlier revisions of this entry incorrectly attributed GWAM to WAVEWATCH III.
- **`TM10` is not a plain mean period, despite what the GRIB header says.** eccodes decodes it as `mwp` / "Mean wave period" (10/0/15), but DWD defines `TM10` as the **energy period T<sub>m−1,0</sub>**, computed from the −1st and 0th spectral moments and used in wave power calculations. The genuine mean period T<sub>m01</sub> is distributed separately, per partition, as `MPWW` and `MPTS`. Treating `TM10` as T<sub>m01</sub> will bias period-dependent calculations. This is a documentation-vs-metadata gap: the GRIB code table entry is generic, and DWD's definition is only in the legend PDF.
- **Meteorological direction convention throughout.** All directions — wind *and* wave — are counted clockwise from north and follow the "coming from" convention. Wave direction conventions vary between operators, so this is worth checking when comparing GWAM against models that report "going to".
- **Wave-model winds are not identical to ICON winds.** DWD notes that `SP_10M` and `DD_10M` are interpolated onto the wave model grid and may differ slightly from the native ICON fields. Use ICON output directly if exact wind consistency matters.
- **GWAM is the only 3-hourly member of the chain.** EWAM and CWAM both output hourly. Users blending the three domains need to account for the mismatched time axes.
- **Grid excludes the poles.** Coverage stops at 89.25°N and 85.25°S; there is no wave output for the innermost polar caps.
- **Related DWD systems:** [EWAM](../../regional/germany/ewam-dwd.md) (European seas, 0.05° × 0.10°, ICON-EU forced, T+78 h hourly) and [CWAM](../../regional/germany/cwam-dwd.md) (German coasts, ~900 m, ICON-EU forced, T+48 h hourly). Atmospheric forcing comes from [ICON Global](../../../nwp_models/global/germany/icon-global.md).
- **No ensemble sibling.** DWD does not distribute a wave ensemble on the open-data server, despite operating the [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md) atmospheric ensemble.

---

## Official documentation
- DWD wave model legend (English): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_EN_pdf.pdf?__blob=publicationFile&v=3
- DWD wave model legend (German): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_DE_pdf.pdf?__blob=publicationFile&v=2
- DWD Open Data server root and terms: https://opendata.dwd.de/README.txt
- DWD Open Data help portal: https://www.dwd.de/opendatahelp
- DWD legal notice / terms of use: https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- GDI-DE metadata record (DWD numerical ocean wave prediction): https://gdk.gdi-de.org/geonetwork/srv/api/records/urn:x-wmo:md:de.dwd.nwv.services.wms.wam
- DWD NWP and emergency response system overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/06_nwp_emergency_response_system/num_weather_prediction_emergency_system.html

### Key references
- WAMDI Group (1988). *The WAM Model — A Third Generation Ocean Wave Prediction Model.* Journal of Physical Oceanography, 18, 1775–1810.
- Komen, G. J., Cavaleri, L., Donelan, M., Hasselmann, K., Hasselmann, S., and Janssen, P. A. E. M. (1994). *Dynamics and Modelling of Ocean Waves.* Cambridge University Press, 532 pp.
- WMO Publication No. 702, *Guide to Wave Analysis and Forecasting* — referenced by DWD for the integral parameter definitions.
