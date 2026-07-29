# WAM Baltic Sea (FMI open data distribution)

## What this model is
FMI's operational third-generation spectral wave forecast for the Baltic Sea, distributed as raw gridded data through the FMI Open Data download service. It forecasts significant wave height, mean wave direction, and total swell height across the Baltic basin at approximately 1 nautical mile resolution.

This entry documents the **open-data distribution**, which is a reduced subset of FMI's full operational wave production. The same underlying WAM production is the FMI contribution to the Copernicus Marine Baltic Monitoring and Forecasting Centre (BAL MFC) product `BALTICSEA_ANALYSISFORECAST_WAV_003_010`; the Copernicus distribution carries a substantially richer parameter set (see Notes).

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI)
- **Country / region:** Finland; domain covers the Baltic Sea (multi-national waters)

---

## What area it covers
- **Coverage:** Baltic Sea, including the Gulf of Bothnia, Gulf of Finland, Gulf of Riga, and the transition waters toward the Danish straits
- **Domain details (live-verified 2026-07-29):** Native grid is **polar stereographic, 775 × 764**, Dx 1894.183 m / Dy 1925.183 m (≈1 nmi), LaD 60.0°N, grid orientation 20.0°E, spherical earth R = 6371220 m, first (SW) grid point 53.000°N 9.010°E. The rotated rectangle's geographic bounding box spans 2.709–36.200°E and 53.000–66.839°N; the sea-point bitmap retains **117,189 of 592,100** grid points (~20%). Published literature describes the FMI Baltic WAM domain as 53–66°N, 9–30°E, which is consistent with the wet portion of this grid.

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** Single regular polar-stereographic grid with a sea-point bitmap; sub-grid island obstruction grids are used in the underlying production (Tuomi et al. 2014) — not separately distributed
- **Core wave model:** WAM. The FMI Baltic production has been documented at WAM cycle 4.6.2 (Aguiar et al. 2024) and the Copernicus sibling product at cycle 4.7; **the cycle used in the open-data feed is not stated by FMI (TBD)**
- **Horizontal resolution:** ~1.9 km (1 nmi)
- **Forecast length:** 60 h of delivered output. **Output begins at T+6, not T+0** — the earliest retrievable step for any cycle is `step=6`, and the last is `step=66` (live-verified: requests with `starttime` earlier than T+6 clamp to step 6; T+69 returns an empty response). Whether the underlying run produces T+0 to T+5 and FMI withholds it, or the run itself starts at +6, is **TBD**.
- **Update frequency / cycles:** 4× daily, 6-hourly (00/06/12/18 UTC)
- **Temporal output resolution:** 1 hour
- **Data retention:** Only the **three most recent cycles** (~12–18 h). Live-verified 2026-07-29: 00Z, 06Z and 12Z returned data; 18Z had not yet published at 19:28 UTC (latency > 1 h 28 m, exact figure **TBD**), and 2026-07-28 18Z and earlier returned nothing. There is no archive.

---

## Forcing and nesting
- **Wind forcing:** MetCoOp HARMONIE-AROME 10 m winds at 2.5 km, hourly, per the Copernicus BAL MFC documentation for the sibling product — see [HARMONIE (MEPS) — FMI distribution](../../../nwp_models/regional/finland/harmonie-fmi.md). Not independently confirmed for the open-data feed (**TBD**).
- **Ice forcing:** The FMI Baltic WAM production accounts for seasonal ice cover using obstruction/threshold treatments driven by FMI ice charts or modelled ice concentration (Tuomi et al. 2019). The specific treatment in the current operational chain is **TBD**.
- **Current forcing:** The Copernicus sibling product is forced with surface currents, sea level anomaly and ice from the Baltic Sea ocean forecast. Whether the FMI open-data chain uses the same coupling is **TBD**; see [Baltic Sea hydrodynamic model (NEMO) — FMI](../../../ocean_models/regional/finland/nemo-baltic-fmi.md).
- **Nested inside / parent for:** Open-boundary wave spectra from the ECMWF wave model, per the BAL MFC documentation (**TBD** for this feed).

---

## Data assimilation
- **Assimilates wave observations:** No documented wave data assimilation in the FMI Baltic WAM chain (**TBD** — FMI does not state this for the open-data product).

---

## What it provides

**Delivered — three fields only (live-verified 2026-07-29):**

| `param` | eccodes | GRIB2 d/c/n | Units | Precision |
|---|---|---|---|---|
| `SigWaveHeight` | `swh` | 10/0/3 | m | 0.001 m |
| `WaveDirection` | `mwd` | 10/0/14 | degrees true | 1° |
| `SigWaveHeightSwell0` | `shts` | 10/0/8 | m | 0.01 m |

**Advertised but never delivered:** `WavePeriod`, `SigWavePeriodSwell0`, `WaveDirectionSwell0`. All three appear in the WFS `observedProperties` list and in the `gml:fileReference` URL that the WFS itself generates, but return **zero-byte responses** at every cycle, step and bounding box tested. **This feed contains no wave period parameter of any kind.**

**Not provided:** peak period, mean period, wind-sea partition, wave spectra, Stokes drift, maximum wave parameters. For any of these, use the Copernicus Marine distribution of the same production (see Notes).

---

## Data availability
- **Is the data free?** Yes — no registration or account required; the user must accept the Creative Commons licence before using the open data interfaces
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution to the Finnish Meteorological Institute required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, GRIB1, and NetCDF via the `format` parameter — but **not all parameters export to all formats**. `SigWaveHeightSwell0` returns zero bytes when `format=netcdf`; it is GRIB-only. NetCDF output is classic `NETCDF3_64BIT_OFFSET`, CF-1.6.
- **File size:** ~1.28 MB for all three fields at one timestep on the native grid; a full 3-parameter, 61-step run is roughly 78 MB.
- **Official download location:**
  - WFS stored query (lists available runs): `https://opendata.fmi.fi/wfs?service=WFS&version=2.0.0&request=getFeature&storedquery_id=fmi::forecast::wam::grid&`
  - Binary download service: `https://opendata.fmi.fi/download` with `producer=wam`
  - Point time-series stored queries: `fmi::forecast::wam::point::{simple,timevaluepair,multipointcoverage}`
- **Delivery mechanism:** HTTP GET against the SmartMet-based binary download service; the WFS returns a `gml:fileReference` URL rather than embedding the grid. The download URL can also be constructed directly without the WFS step. Request limits: 20,000 download requests/day, 10,000 view requests/day, 600 combined per 5 minutes.

---

## Notes
- **The advertised parameter list is half fiction.** Three of six WFS-advertised parameters are undeliverable. Because the WFS itself emits a `fileReference` naming all six, a client that follows FMI's documented two-step workflow verbatim will silently receive a file containing only three. Verify parameter availability before building on this feed.
- **Output starts at T+6.** Unusual for a wave forecast, and undocumented. It means the feed cannot be used for nowcast-adjacent work; the first valid time is six hours after the nominal analysis.
- **Retention is punishing.** Three cycles, no archive. Any time-series application must poll and store locally.
- **GRIB encoding quirks (live-verified):**
  - `centre = ecmf` — files declare ECMWF as originating centre, not FMI.
  - Level types are nonsensical for wave fields: `swh` and `mwd` are encoded as `heightAboveGround` level 0, `shts` as `entireAtmosphere` level 0.
  - Missing-value sentinel is **9999** in GRIB but **32700** (`_FillValue`) in NetCDF.
  - `mwd` is quantized to whole degrees.
- **GRIB and NetCDF disagree about what `WaveDirection` is.** In GRIB it is `mwd` (mean wave direction of combined wind waves and swell, 10/0/14) with `stepType = instant`. In NetCDF the same request yields `sea_surface_wave_from_direction_167` with `long_name = "Wave direction of wind waves"` on a separate `time_h` axis carrying `cell_methods: time_h: mean` and time bounds. One of these is wrong; which one is **TBD**. Treat the GRIB encoding as authoritative until FMI clarifies.
- **Richer sibling distribution.** The same FMI WAM production reaches Copernicus Marine as `BALTICSEA_ANALYSISFORECAST_WAV_003_010`, which delivers significant height, period and direction for total sea, wind sea *and* swell, plus Stokes drift and maximum-wave parameters, at 1 nmi with a 10-day forecast from 00Z and 6 days from 12Z. That product requires Copernicus registration and licence acceptance; this FMI feed does not. The trade is registration-free access against a much thinner parameter set and a 60-hour horizon.
- **Higher-resolution FMI wave domains are not in the open feed.** FMI has operated a 0.5 nmi Archipelago Sea wave forecast since 2014 (Tuomi and Björkqvist 2014). Producer-name probes for `wam_hires`, `wam_archipelago`, and `wam_saaristomeri` all returned nothing; if such a feed exists it is not exposed here.
- **Companion FMI marine feeds on the same service:** [Baltic Sea hydrodynamic model (NEMO)](../../../ocean_models/regional/finland/nemo-baltic-fmi.md) (`producer=nemo`) and the OAAS sea level forecast, the latter distributed as point time series only.

---

## Official documentation
- FMI Open Data — Forecast models manual: https://en.ilmatieteenlaitos.fi/open-data-manual-forecast-models
- FMI Open Data — open data sets: https://en.ilmatieteenlaitos.fi/open-data-sets-available
- FMI Open Data — licence (CC BY 4.0): https://en.ilmatieteenlaitos.fi/open-data-licence
- FMI WFS GetCapabilities: https://opendata.fmi.fi/wfs?request=GetCapabilities
- Copernicus Marine sibling product: https://data.marine.copernicus.eu/product/BALTICSEA_ANALYSISFORECAST_WAV_003_010/description
- Copernicus Marine QUID (BAL wave): https://documentation.marine.copernicus.eu/QUID/CMEMS-BAL-QUID-003-010.pdf

### Key references
- Aguiar, E. et al. (2024). *Evaluating accuracy of Baltic Sea wave forecasts*. Geophysica, 59(1), 003. https://www.geophysica.fi/pdf/geophysica_2024_59_1_003_aguiar.pdf
- Björkqvist, J.-V. et al. (2019). *WAM, SWAN and WAVEWATCH III in the Finnish archipelago — the effect of spectral performance on bulk wave parameters*. Journal of Operational Oceanography. https://doi.org/10.1080/1755876X.2019.1633236
- Tuomi, L. et al. (2019). *Wave hindcast statistics in the seasonally ice-covered Baltic Sea*. Frontiers in Earth Science, 7, 166. https://doi.org/10.3389/feart.2019.00166
- Björkqvist, J.-V. et al. (2021). *Swell hindcast statistics for the Baltic Sea*. Ocean Science, 17, 1815–1829. https://doi.org/10.5194/os-17-1815-2021
