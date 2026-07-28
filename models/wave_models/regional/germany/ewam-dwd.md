# EWAM (European Wave Model)

## What this model is
EWAM is the **regional deterministic ocean wave forecast model** operated by Deutscher Wetterdienst (DWD), covering European seas. It is the middle tier of DWD's three-tier wave forecasting chain (GWAM → EWAM → CWAM), which parallels the ICON atmospheric model chain (ICON → ICON-EU → ICON-D2).

Like its siblings, EWAM is a configuration of **WAM**, the third-generation spectral wave model developed by the WAMDI Group. It carries a full two-dimensional wave energy spectrum at each sea point and resolves wave growth from wind momentum flux, propagation (advection and refraction), nonlinear wave–wave energy transfer, and dissipation. DWD has run WAM operationally since 1992.

EWAM takes lateral boundary conditions from [GWAM](../../global/germany/gwam-dwd.md) and supplies them in turn to [CWAM](./cwam-dwd.md). It refines the global sea state over the European shelf seas, the Mediterranean, and the Black Sea, at roughly five times the resolution of GWAM and with hourly rather than 3-hourly output.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD)
- **Country / region:** Germany (European domain)

---

## What area it covers
- **Coverage:** European seas — the north-east Atlantic, North Sea, Baltic Sea, **Mediterranean Sea**, and **Black Sea**
- **Domain details:** single regular latitude–longitude grid, **526 × 721** points, **0.10° longitude × 0.05° latitude**, spanning **10.50°W – 42.00°E** and **66.00°N – 30.00°N**, scanning north to south. Live-verified from GRIB (00 UTC run, 2026-07-28). Of 379,246 grid points, **138,388 are sea points** (36.5%); the remaining 240,858 are masked via a GRIB bitmap (`missingValue = 9999`).
- **Basin coverage confirmed by live sampling** (2026-07-28 00 UTC, +24 h): valid wave fields returned in the central Black Sea, Ionian Sea, western Mediterranean, northern Adriatic, Baltic (Gotland Basin), North Sea, and north-east Atlantic.

---

## Basic details
- **Model type:** Deterministic regional wave model
- **Grid system:** Single regular lat-lon grid (anisotropic in degrees, near-isotropic in kilometres — see *Notes*)
- **Core wave model:** **WAM** — third-generation spectral wave model (WAMDI Group). *Specific WAM cycle version not published by DWD* (**TBD**)
- **Spectral resolution:** 36 directions × 30 frequencies, covering wave periods from **1.5 s to 24 s**
- **Horizontal resolution:** 0.10° × 0.05° (~5.6 km meridionally; ~5.6 km zonally at 60°N, ~9.1 km at 30°N)
- **Forecast length:** **78 h** (3¼ days)
- **Update frequency / cycles:** 2× daily (**00** and **12 UTC**)
- **Temporal output resolution:** **hourly** — 79 steps, 000 to 078 h

---

## Forcing and nesting
- **Wind forcing:** [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md) 10 m winds (~6.5 km), analyzed and forecast
- **Ice forcing:** Not documented by DWD. The GRIB bitmap is **identical at 000 h and 078 h**, confirming it is a static land–sea mask rather than a time-varying ice mask — relevant for the northern Baltic and Gulf of Bothnia, where seasonal ice cover materially affects wave generation but is not reflected in the distributed mask (**TBD**)
- **Current forcing:** None documented for EWAM. (Current and water-level coupling has been developed at the coastal end of the chain, in CWAM.)
- **Nested inside:** [GWAM](../../global/germany/gwam-dwd.md) — supplies lateral wave boundary conditions at the Atlantic and Mediterranean open boundaries
- **Parent for:** [CWAM](./cwam-dwd.md) — EWAM supplies boundary conditions to the German coastal domain

---

## Data assimilation
- **Assimilates wave observations:** **TBD.** DWD's current open-data documentation makes no mention of wave data assimilation, and describes the forecast as depending directly on ICON-EU 10 m wind forcing. Published literature from the ERS/Envisat era describes a DWD altimeter SWH assimilation scheme, but that work centred on the global model, and whether any assimilation is active in the current operational EWAM is not confirmed by any DWD source located. Flagged rather than asserted; see the matching note in the [GWAM entry](../../global/germany/gwam-dwd.md).

---

## What it provides
Thirteen integral parameters, one GRIB2 file per parameter per time step — the **same parameter set as GWAM and CWAM**. Field list and GRIB encoding live-verified on the 2026-07-28 00 UTC run:

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

**Partitioning convention:** the wind-sea partition comprises waves propagating within a defined directional interval whose phase speeds do not exceed the wind speed; the swell partition is the entire remainder of the spectrum. Only a single swell partition is distributed.

**No wave spectra are distributed** — only these integral parameters.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** DWD Open Data terms under **GeoNutzV** (Germany's federal geodata usage ordinance) — effectively CC BY 4.0. Commercial use permitted; **attribution to DWD is mandatory** ("Source: Deutscher Wetterdienst"). Note that DWD logs client IP addresses for up to 7 days for server operations.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, **bzip2-compressed** (`.grib2.bz2`), `grid_simple` packing, 10 bits per value
- **Official download location:**
  https://opendata.dwd.de/weather/maritime/wave_models/ewam/grib/
  - **Path template:** `/weather/maritime/wave_models/ewam/grib/{HH}/{param}/` — `{HH}` = cycle (`00` / `12`), `{param}` = lowercase parameter folder (`swh`, `tm10`, `mwd`, `shww`, `mpww`, `ppww`, `mdww`, `shts`, `mpts`, `ppts`, `mdts`, `sp_10m`, `dd_10m`)
  - **Filename convention:** `EWAM_{PARAM}_{YYYYMMDDHH}_{VVV}.grib2.bz2`
    - `{PARAM}` — uppercase parameter name (`SWH`, `DD_10M`, …)
    - `{YYYYMMDDHH}` — run initialization time (hour is `00` or `12`)
    - `{VVV}` — forecast lead time in hours, `000`, `001`, … `078`
  - **Example:** `.../ewam/grib/00/swh/EWAM_SWH_2026072800_024.grib2.bz2`
- **File size:** ~80 KB compressed / ~215 KB uncompressed per SWH field; ~33 KB–235 KB compressed depending on parameter. **~1.7 MB per time step** across all 13 parameters, **~133 MB per full run** — roughly a third of GWAM's volume despite the hourly output, because the domain is far smaller.
- **Retention:** short rolling window — **two runs per cycle directory** (~48 h) at check time. No archive is offered on the open-data server; users needing history must self-archive.
- **New-data notifications:** none. Poll the directory tree, or watch `content.log.bz2` at `/weather/maritime/`, which lists recent file additions.

---

## Notes
- **Verified 2026-07-28.** Directory structure, file naming, step list, grid geometry, parameter encoding, bitmap behaviour, and basin coverage confirmed by direct inspection of live GRIB files from the 00 UTC run.
- **Download path changed.** This entry previously listed `https://opendata.dwd.de/weather/marine/wave/`. That path now returns **404**, as does its parent `/weather/marine/`. The wave models live under `/weather/maritime/wave_models/`.
- **The core model is WAM, not WAVEWATCH III.** The "WAM" in GWAM, EWAM, and CWAM is the model name itself. DWD has run WAM operationally since 1992; the model is maintained at Helmholtz-Zentrum Hereon and is the same lineage used by ECMWF's ECWAM and in Copernicus Marine wave production. Earlier revisions of this entry incorrectly attributed EWAM to WAVEWATCH III.
- **Forecast length is 78 h, not 120 h.** Earlier revisions of this entry stated "up to 120 hours (5 days)". DWD's documentation and the live file listing both give 78 h, with 79 hourly steps per run. Users who built pipelines against the 120 h figure will find steps 079–120 simply absent.
- **Coverage includes the Mediterranean and Black Sea.** Earlier revisions described the domain as "North Sea, Baltic Sea, and adjacent Atlantic regions", which understates it considerably. The 30°N–66°N, 10.5°W–42°E box takes in the entire Mediterranean basin and the Black Sea, and DWD's own model description names those seas explicitly. Live sampling confirms active wave fields throughout both.
- **Longitude is encoded 0–360, not −180/+180.** The GRIB header reports `longitudeOfFirstGridPointInDegrees = 349.5` for the 10.5°W western edge, so the domain wraps the prime meridian in index space. Readers that assume a monotonically increasing signed longitude axis will mis-georeference the Atlantic portion of the grid. `cfgrib` and `xarray` handle this correctly; hand-rolled index arithmetic often does not.
- **The grid is anisotropic in degrees but near-isotropic in kilometres around 60°N.** The 2:1 longitude-to-latitude increment ratio (0.10° / 0.05°) exactly compensates for the convergence of meridians at 60°N, where cos(60°) = 0.5. Grid cells are close to square through the Baltic, North Sea, and Norwegian Sea — the domain's operational core — and become progressively wider than tall toward the Mediterranean.
- **`TM10` is not a plain mean period, despite what the GRIB header says.** eccodes decodes it as `mwp` / "Mean wave period" (10/0/15), but DWD defines `TM10` as the **energy period T<sub>m−1,0</sub>**. The genuine mean period T<sub>m01</sub> is distributed separately, per partition, as `MPWW` and `MPTS`. See the [GWAM entry](../../global/germany/gwam-dwd.md) for the full explanation — the same gotcha applies across all three DWD wave models.
- **Meteorological direction convention throughout.** All directions — wind *and* wave — are counted clockwise from north and follow the "coming from" convention.
- **Wave-model winds are not identical to ICON-EU winds.** DWD notes that `SP_10M` and `DD_10M` are interpolated onto the wave model grid and may differ slightly from the native ICON-EU fields. Use ICON-EU output directly if exact wind consistency matters.
- **Time axis differs from GWAM.** EWAM and CWAM are hourly; GWAM alone is 3-hourly. Users blending the three domains need to account for the mismatched time axes.
- **GRIB `generatingProcessIdentifier` distinguishes the domains:** GWAM = 199, EWAM = 202. Useful for sorting mixed archives where filenames have been lost, since all three models share identical parameter encodings and `centre = edzw`.
- **Related DWD systems:** [GWAM](../../global/germany/gwam-dwd.md) (global, 0.25°, ICON-forced, T+174 h 3-hourly) and [CWAM](./cwam-dwd.md) (German coasts, ~900 m, ICON-EU forced, T+48 h hourly). Atmospheric forcing comes from [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md), the European nest of [ICON Global](../../../nwp_models/global/germany/icon-global.md).
- **No ensemble sibling.** DWD does not distribute a wave ensemble on the open-data server.
- **Overlapping coverage from other catalogued systems.** The EWAM domain overlaps substantially with Copernicus Marine's regional wave products and with several national systems documented elsewhere in this repository. EWAM's distinguishing features are its unusually low access friction (plain HTTPS, no account) and its position as the boundary provider for CWAM, rather than resolution or assimilation.

---

## Official documentation
- DWD wave model legend (English): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_EN_pdf.pdf?__blob=publicationFile&v=3
- DWD wave model legend (German): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_DE_pdf.pdf?__blob=publicationFile&v=2
- DWD Open Data server root and terms: https://opendata.dwd.de/README.txt
- DWD Open Data help portal: https://www.dwd.de/opendatahelp
- DWD legal notice / terms of use: https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- GDI-DE metadata record (EWAM — Numerical Ocean Wave Prediction for European coasts): https://gdk.gdi-de.org/geonetwork/srv/api/records/urn:x-wmo:md:de.dwd.nwv.services.wms.ewam
- DWD NWP and emergency response system overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/06_nwp_emergency_response_system/num_weather_prediction_emergency_system.html

### Key references
- WAMDI Group (1988). *The WAM Model — A Third Generation Ocean Wave Prediction Model.* Journal of Physical Oceanography, 18, 1775–1810.
- Komen, G. J., Cavaleri, L., Donelan, M., Hasselmann, K., Hasselmann, S., and Janssen, P. A. E. M. (1994). *Dynamics and Modelling of Ocean Waves.* Cambridge University Press, 532 pp.
- WMO Publication No. 702, *Guide to Wave Analysis and Forecasting* — referenced by DWD for the integral parameter definitions.
