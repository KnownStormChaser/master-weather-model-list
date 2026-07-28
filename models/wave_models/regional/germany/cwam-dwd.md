# CWAM (Coastal Wave Model)

## What this model is
CWAM is the **high-resolution coastal deterministic wave forecast model** operated by Deutscher Wetterdienst (DWD), covering the German Bight and the western and southern Baltic Sea at roughly 900 m. It is the innermost tier of DWD's three-tier wave forecasting chain (GWAM → EWAM → CWAM), which parallels the ICON atmospheric model chain (ICON → ICON-EU → ICON-D2).

Like its siblings, CWAM is a configuration of **WAM**, the third-generation spectral wave model developed by the WAMDI Group. Unlike them, it is a genuinely shallow-water configuration: published descriptions of CWAM document bottom friction, depth-induced wave breaking, and depth/current refraction, with nonlinear transfer handled by the Discrete Interaction Approximation. At ~900 m the model resolves bathymetric structure and coastal geometry that EWAM's ~5.6 km grid smooths away, which is where most of the forecast difference comes from — reported local differences in significant wave height between CWAM and EWAM in coastal areas reach the order of metres.

CWAM was developed by DWD together with the German Federal Maritime and Hydrographic Agency (BSH). It takes lateral boundary conditions from [EWAM](./ewam-dwd.md) and is the terminal member of the chain — it is a parent for nothing further.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD), developed jointly with the Bundesamt für Seeschifffahrt und Hydrographie (BSH)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** German coastal waters — the **German Bight** (including the Wadden Sea and North Frisian coast) and the **western and southern Baltic Sea** (Kiel Bight, Mecklenburg Bight, waters off Rügen, Pomeranian Bight). A **single domain**, not a set of separate coastal grids.
- **Domain details:** single regular latitude–longitude grid, **630 × 387** points, **50″ longitude × 30″ latitude** (0.013889° × 0.008333°), spanning **6°10′25″E – 14°54′35″E** (6.173611 – 14.909722) and **56°26′45″N – 53°13′45″N** (56.445835 – 53.229168), scanning north to south. Live-verified from GRIB (00 UTC run, 2026-07-28). Of 243,810 grid points, **124,011 are sea points** (50.9%); the remaining 119,799 are masked via a GRIB bitmap (`missingValue = 9999`).
- **Coverage confirmed by live sampling** (2026-07-28 00 UTC, +24 h): valid wave fields returned near Helgoland, along the North Frisian coast, in the Wadden Sea near the Ems, in Kiel Bight, off Rügen, in the Pomeranian Bight, and at the northern domain edge toward the Skagerrak approaches.

---

## Basic details
- **Model type:** Deterministic coastal wave model (shallow-water configuration)
- **Grid system:** Single regular lat-lon grid (near-square cells in kilometres — see *Notes*)
- **Core wave model:** **WAM** — third-generation spectral wave model (WAMDI Group), in a shallow-water configuration. *Specific WAM cycle version not published by DWD* (**TBD**)
- **Spectral resolution:** 36 directions × 30 frequencies, covering wave periods from **1.5 s to 24 s** — the same spectral discretization used across all three DWD wave domains
- **Shallow-water physics:** bottom friction, depth-induced wave breaking, and depth/current refraction; nonlinear transfer via the Discrete Interaction Approximation (DIA). Documented in the CWAM literature rather than in DWD's open-data documentation.
- **Horizontal resolution:** ~900 m (≈926 m meridionally; ≈855–925 m zonally across the domain)
- **Forecast length:** **78 h** (3¼ days) — live-verified. **DWD's published legend states 48 h; this is stale.** See *Notes*.
- **Update frequency / cycles:** 2× daily (**00** and **12 UTC**)
- **Temporal output resolution:** **hourly** — 79 steps, 000 to 078 h

---

## Forcing and nesting
- **Wind forcing:** [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md) 10 m winds (~6.5 km), analyzed and forecast. *Note: DWD's legend gives ICON-EU for CWAM, not ICON-D2. Earlier revisions of this entry stated "ICON-EU or ICON-D2 (domain-dependent)", which is not supported by any DWD source located.*
- **Current and water-level forcing:** **Documented in the literature, not confirmed in current DWD documentation.** Published CWAM descriptions state the model is driven by water level changes and currents from **HBM (the HIROMB-BOOS Model)**, the operational circulation model run by BSH — making CWAM the only member of the DWD wave chain with current forcing. Those descriptions date from the COSMO-EU era, and since the wind driver has since changed to ICON-EU, the persistence of the HBM coupling in the current operational setup is **not verified** (**TBD**). This matters: current refraction is listed among CWAM's physics, and tidal currents in the German Bight are strong enough to change wave height materially.
- **Ice forcing:** Not documented by DWD. The GRIB bitmap is **identical at 000 h and 078 h**, confirming a static land–sea mask rather than a time-varying ice mask — relevant for the western Baltic, which ices in some winters (**TBD**)
- **Nested inside:** [EWAM](./ewam-dwd.md) — supplies lateral wave boundary conditions
- **Parent for:** None. CWAM is the terminal domain of the chain.

---

## Data assimilation
- **Assimilates wave observations:** **TBD.** DWD's open-data documentation makes no mention of wave data assimilation in any domain of the chain. CWAM has been extensively *validated* against TerraSAR-X / TanDEM-X SAR-derived significant wave height and against German Bight buoys, but validation is not assimilation, and no source located describes SAR or buoy data being ingested operationally. Flagged rather than asserted; see the matching note in the [GWAM entry](../../global/germany/gwam-dwd.md).

---

## What it provides
Thirteen integral parameters, one GRIB2 file per parameter per time step — the **same parameter set as GWAM and EWAM**. Field list and GRIB encoding live-verified on the 2026-07-28 00 UTC run:

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

**No water level, current, or wave setup fields are distributed**, despite water level and currents being (per the literature) model *inputs*. Users needing coastal water levels for the same domain should look to BSH's own products rather than to CWAM output.

**No wave spectra are distributed** — only these integral parameters.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, direct HTTPS
- **License:** DWD Open Data terms under **GeoNutzV** (Germany's federal geodata usage ordinance) — effectively CC BY 4.0. Commercial use permitted; **attribution to DWD is mandatory** ("Source: Deutscher Wetterdienst"). Note that DWD logs client IP addresses for up to 7 days for server operations.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, **bzip2-compressed** (`.grib2.bz2`), `grid_simple` packing, 8 bits per value
- **Official download location:**
  https://opendata.dwd.de/weather/maritime/wave_models/cwam/grib/
  - **Path template:** `/weather/maritime/wave_models/cwam/grib/{HH}/{param}/` — `{HH}` = cycle (`00` / `12`), `{param}` = lowercase parameter folder (`swh`, `tm10`, `mwd`, `shww`, `mpww`, `ppww`, `mdww`, `shts`, `mpts`, `ppts`, `mdts`, `sp_10m`, `dd_10m`)
  - **Filename convention:** `CWAM_{PARAM}_{YYYYMMDDHH}_{VVV}.grib2.bz2`
    - `{PARAM}` — uppercase parameter name (`SWH`, `DD_10M`, …)
    - `{YYYYMMDDHH}` — run initialization time (hour is `00` or `12`)
    - `{VVV}` — forecast lead time in hours, `000`, `001`, … `078`
  - **Example:** `.../cwam/grib/00/swh/CWAM_SWH_2026072800_024.grib2.bz2`
- **File size:** ~37 KB compressed / ~155 KB uncompressed per SWH field; ~19 KB–183 KB compressed depending on parameter. **~1.0 MB per time step** across all 13 parameters, **~83 MB per full run** — the smallest of the three domains despite the highest resolution, because the area is so limited.
- **Retention:** short rolling window — **two runs per cycle directory** (~48 h) at check time. No archive is offered on the open-data server; users needing history must self-archive.
- **New-data notifications:** none. Poll the directory tree, or watch `content.log.bz2` at `/weather/maritime/`, which lists recent file additions.

---

## Notes
- **Verified 2026-07-28.** Directory structure, file naming, step list, grid geometry, parameter encoding, bitmap behaviour, and domain coverage confirmed by direct inspection of live GRIB files from the 00 UTC run.
- **Download path changed.** This entry previously listed `https://opendata.dwd.de/weather/marine/wave/`. That path now returns **404**, as does its parent `/weather/marine/`. The wave models live under `/weather/maritime/wave_models/`.
- **The core model is WAM, not WAVEWATCH III.** The "WAM" in GWAM, EWAM, and CWAM is the model name itself. DWD has run WAM operationally since 1992; the model is maintained at Helmholtz-Zentrum Hereon and is the same lineage used by ECMWF's ECWAM and in Copernicus Marine wave production. Earlier revisions of this entry incorrectly attributed CWAM to WAVEWATCH III.
- **DWD's own documentation understates the forecast length.** The wave legend PDF (both English and German editions) gives CWAM as **T+48 h**. The live open-data feed carries **79 hourly steps out to 078 h**, verified across all 13 parameters and both the 00 and 12 UTC cycles. The published legend is accurate for GWAM (174 h) and EWAM (78 h) but stale for CWAM. Users should treat 78 h as the operational figure and not truncate ingest pipelines at 48 h. *This is worth re-checking periodically — it is equally possible that DWD extended the run and did not update the PDF, or that the extension is provisional.*
- **Resolution is ~900 m, not 0.025°–0.05°.** Earlier revisions of this entry gave "~0.025°–0.05° (domain-dependent)" across multiple coastal regions. The live grid is a single domain at a fixed 30″ × 50″ increment — roughly 0.0083° × 0.0139°, about three to six times finer than the figure previously stated.
- **Grid cells are near-square in kilometres.** The 5:3 longitude-to-latitude increment ratio (50″ / 30″) compensates exactly for meridian convergence where cos(latitude) = 0.6, i.e. at 53.13°N — essentially the domain's southern boundary. Cells are square there and become about 8% taller than wide at the northern edge. This is the same design convention used in [DMI's WAM chain](../denmark/wam-dmi.md), whose Danish Waters nest overlaps CWAM's western Baltic coverage.
- **`TM10` is not a plain mean period, despite what the GRIB header says.** eccodes decodes it as `mwp` / "Mean wave period" (10/0/15), but DWD defines `TM10` as the **energy period T<sub>m−1,0</sub>**. The genuine mean period T<sub>m01</sub> is distributed separately, per partition, as `MPWW` and `MPTS`. See the [GWAM entry](../../global/germany/gwam-dwd.md) for the full explanation — the same gotcha applies across all three DWD wave models.
- **Meteorological direction convention throughout.** All directions — wind *and* wave — are counted clockwise from north and follow the "coming from" convention.
- **Wave-model winds are not identical to ICON-EU winds.** DWD notes that `SP_10M` and `DD_10M` are interpolated onto the wave model grid and may differ slightly from the native ICON-EU fields.
- **Swell in a fetch-limited domain.** The `SHTS` / `MPTS` / `PPTS` / `MDTS` swell partition is distributed for CWAM as it is for the global and European domains, but in the western Baltic swell in the open-ocean sense is largely absent — the partition there mostly captures locally generated waves that have outrun the wind rather than remote swell. Interpret the partition split with the domain's fetch limits in mind.
- **Wadden Sea caveat.** The grid extends into the Wadden Sea, where intertidal flats dry at low water. The distributed land–sea mask is **static**, so cells that are periodically dry are treated as permanent water. Wave heights in the tidal flats should be treated with caution.
- **GRIB `generatingProcessIdentifier` distinguishes the three domains:** GWAM = 199, EWAM = 202, CWAM = 203. Useful for sorting mixed archives where filenames have been lost, since all three share identical parameter encodings and `centre = edzw`.
- **Related DWD systems:** [GWAM](../../global/germany/gwam-dwd.md) (global, 0.25°, ICON-forced, T+174 h 3-hourly) and [EWAM](./ewam-dwd.md) (European seas, 0.10° × 0.05°, ICON-EU forced, T+78 h hourly). Atmospheric forcing comes from [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md).
- **Neighbouring coverage.** [DMI's WAM](../denmark/wam-dmi.md) Danish Waters nest (~1.1 km) covers overlapping western Baltic waters at comparable resolution under a different forcing chain, offering a useful independent comparison for the same seas.
- **No ensemble sibling.** DWD does not distribute a wave ensemble on the open-data server.

---

## Official documentation
- DWD wave model legend (English): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_EN_pdf.pdf?__blob=publicationFile&v=3 — *note: CWAM forecast length in this document is stale (see Notes)*
- DWD wave model legend (German): https://www.dwd.de/DE/leistungen/opendata/help/modelle/legend_ICON_wave_DE_pdf.pdf?__blob=publicationFile&v=2
- DWD Open Data server root and terms: https://opendata.dwd.de/README.txt
- DWD Open Data help portal: https://www.dwd.de/opendatahelp
- DWD legal notice / terms of use: https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- GDI-DE metadata record (CWAM — Numerical Ocean Wave Prediction for German coasts): https://gdk.gdi-de.org/geonetwork/srv/api/records/urn:x-wmo:md:de.dwd.nwv.services.wms.cwam

### Key references
- WAMDI Group (1988). *The WAM Model — A Third Generation Ocean Wave Prediction Model.* Journal of Physical Oceanography, 18, 1775–1810.
- Hasselmann, S., Hasselmann, K., Allender, J. H., and Barnett, T. P. (1985). *Computations and Parameterizations of the Nonlinear Energy Transfer in a Gravity-Wave Spectrum. Part II: Parameterizations of the Nonlinear Energy Transfer for Application in Wave Models.* Journal of Physical Oceanography, 15, 1378–1391. — the DIA scheme used in CWAM.
- Pleskachevsky, A., Rosenthal, W., and Lehner, S. (2016). *Meteo-marine parameters for highly variable environment in coastal regions from satellite radar images.* ISPRS Journal of Photogrammetry and Remote Sensing, 119, 464–484.
- Staneva, J., Wahle, K., Koch, W., Behrens, A., Fenoglio-Marc, L., and Stanev, E. V. (2016). *Coupling of wave and circulation models in coastal–ocean predicting systems: a case study for the German Bight.* Ocean Science, 12, 797–806. https://doi.org/10.5194/os-12-797-2016
- WMO Publication No. 702, *Guide to Wave Analysis and Forecasting* — referenced by DWD for the integral parameter definitions.
