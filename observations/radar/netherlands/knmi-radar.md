# KNMI Radar (Netherlands — National Network: Volumes, Composites & Radar/Gauge QPE)

## What this is
The Royal Netherlands Meteorological Institute (KNMI) publishes the output of its national weather-radar network openly through the KNMI Data Platform. This entry covers the **observational** products: the full dual-polarimetric **single-site volume data** from each of the two operational radars, the national **2D composite products** (reflectivity pseudo-CAPPI, echo-top height, a 3D composite, and radar-derived hail probability), and the **radar/gauge quantitative precipitation estimation (QPE)** family (real-time, reanalysis, multi-hour, and climatologically gauge-adjusted accumulations). All are freely downloadable with a free KNMI API key.

The same platform also carries **nowcast/forecast** products (radar extrapolation, ensemble and seamless precipitation nowcasts, and ML-based QRF forecasts) — those are forecast systems and belong in the catalog's nowcasting area rather than here; they are itemised in the Scope note. The Netherlands is a EUMETNET OPERA member, so — as with Germany, the UK, Czechia, and the Nordics — these KNMI products are the national counterpart to the [OPERA pan-European composite](../eu/opera-composite.md).

---

## Who operates it
- **Operator:** Royal Netherlands Meteorological Institute (Koninklijk Nederlands Meteorologisch Instituut, KNMI).
- **Country / region:** Netherlands.
- **Data distributor:** KNMI Data Platform (`dataplatform.knmi.nl`), via the Open Data API.

---

## Network composition
KNMI operates a **two-radar C-band dual-polarisation Doppler** network:

| Site | Code | Position | Status |
|---|---|---|---|
| **Den Helder** | `NL61` | 4.790°E, 52.953°N | Operational |
| **Herwijnen** | `NL62` | 5.138°E, 51.837°N | Operational |
| **De Bilt** | (legacy) | 5.178°E, 52.102°N | Decommissioned (archive only) |

De Bilt still appears in composite metadata for historical continuity but is flagged non-operational; it was superseded by Herwijnen. National composites blend the two operational radars (with neighbouring foreign radars filled in near the domain edges).

Each radar delivers a **full polarimetric moment set** (verified from the Den Helder volume): corrected and uncorrected reflectivity (**Z**, **uZ**, plus vertical-polarisation **Zv**/**uZv**), radial velocity (**V**, **Vv**), spectrum width (**W**, **Wv**), **KDP**, differential phase (**PhiDP**, **uPhiDP**), correlation coefficient (**RhoHV**), clutter-correction (**CCOR**), clutter-phase alignment (**CPA**), and signal-quality index (**SQI**). Publishing **KDP** directly is notable — many networks compute it but distribute only PhiDP. The volume scan runs **16 elevations** (0.3° × several, 0.8°, 1.2°, 2.0°, 2.8°, 4.5°, 6.0°, 8.0°, 10.0°, 12.0°, 15.0°, 20.0°, 25.0°, plus a 90° birdbath scan) on a 5-minute cycle.

---

## Products
**Single-site volume data** (`radar_volume_denhelder` v2.0, `radar_volume_full_herwijnen` v1.0) — 3D polar volumes in **KNMI HDF5**, azimuthal-equidistant projection centred on each radar (Den Helder: `+proj=aeqd +lat_0=52.9533 +lon_0=4.79`), one file per radar every 5 minutes, containing all 16 scans and the full moment set above.

**Composite / mosaic products** — 2D grids on the **RAD_NL25** grid: **700 × 765** pixels, **1 km**, polar-stereographic (`+proj=stere +lat_0=90 +lon_0=0 +lat_ts=60`, sphere a=6378137/b=6356752), KNMI HDF5:
- **Reflectivity composite** (`radar_reflectivity_composites` v2.0) — 5-minute pseudo-CAPPI at 1.5 km (`RAD_NL25_PCP_H1.5`), reflectivity in dBZ (calibration `dBZ = 0.5·PV − 32`; 0 = missing, 255 = out-of-image).
- **Echo-top height** (`radar_echotopheight_5min` v1.0) — 5-minute echo-top-height composite.
- **3D radar composite** (`3d_radar_composite` v1.0) — 3D composite and derived products.
- **Hail probability** (`radar_hail_warning_5min` v1.0, `radar_hail_warning_24h` v1.0) — radar-derived probability of hail (5-minute and 24-hour).

**Radar/gauge precipitation (QPE) accumulations** — same RAD_NL25 grid, KNMI HDF5:
- **Real-time** (`nl_rdr_data_rtcor_5m` v1.0, `RAD_NL25_RAC_RT`) — 5-minute real-time radar/gauge accumulation.
- **Reanalysis** (`nl_rdr_data_recor_5m` v1.0 — early; `nl_rdr_data_rfcor_5m` v1.0 — final) — 5-minute gauge-adjusted reanalyses, produced with more complete gauge data after the fact.
- **Multi-hour** (`radar_corr_accum_03h` v1.0, `radar_corr_accum_24h` v1.0) — 3-hour and 24-hour radar/gauge accumulations.
- **Climatological gauge-adjusted (MFBS)** — mean-field-bias + spatially-adjusted accumulations at 5-minute / 1-hour / 24-hour resolution, on both the **RAD_NL25** (1 km) and **RAD_NL21** grids (`rad_nl25_rac_mfbs_*`, `rad_nl21_rac_mfbs_*`). **Also offered in NetCDF4** (`*_netcdf4` datasets) in addition to HDF5 — the one part of the radar suite with a CF-NetCDF option.

The real-time / reanalysis / climatological split lets users trade latency against gauge-adjustment quality; the `radar_tar_*` datasets provide longer `.tar`-bundled archives of several of these product lines. The QPE methodology is documented in an ESSD data paper (doi:10.5194/essd-17-4715-2025) and the KNMI-OSS radar repository.

---

## Data availability
- **Is the data free?** Yes (free KNMI API key required).
- **Is the data downloadable?** Yes.
- **Access tier:** Open with a free registered key (no approval gate, no commercial restriction); a shared anonymous key is also published.
- **Data formats:** **KNMI HDF5** (legacy KNMI structure — see Notes; *not* ODIM HDF5). The climatological MFBS accumulation products are additionally available in **NetCDF4**.
- **Update cadence:** 5 minutes for volumes, composites, and real-time QPE; 3-hourly and 24-hourly for the corresponding accumulation products.
- **Primary access:** KNMI Data Platform Open Data API, with the key in the `Authorization` HTTP header (no `Bearer` prefix):
```
  https://api.dataplatform.knmi.nl/open-data/v1/datasets/{dataset}/versions/{version}/files
```
  Note the dataset name uses underscores and the version is a separate path segment (e.g. `radar_reflectivity_composites` / `2.0`), which differs from the hyphenated catalog slug (`radar-reflectivity-composites-2-0`). Key options (anonymous / registered / bulk) and the MQTT Notification Service are identical to the KNMI HARMONIE datasets.
- **Archive depth:** Real-time products roll over a recent window; longer archives are provided via the `radar_tar_*` bundled datasets and the reanalysis (RECOR/RFCOR) and climatological (MFBS) product lines. (Exact rolling-window length not verified here.)
- **Licence:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required.

---

## Scope note
The KNMI radar tree also contains **forecast/nowcast** products, which are out of scope for this observational entry and belong in the catalog's nowcasting area:
- `radar_forecast` (v2.0) — 5-minute radar extrapolation nowcast to +2 h.
- `precipitation_nl_ensemble_nowcast_5min` — radar/gauge 5-minute ensemble nowcast.
- `seamless_precipitation_ensemble_forecast_members` / `…_probabilities` — seamless nowcast-to-NWP blended ensemble (members and exceedance probabilities).
- `qrf_rt_ssh_v2021` / `qrf_rt_ssh_v2025` — ML (Quantile Regression Forest) ensemble precipitation forecasts.

Also excluded from this entry:
- **EURADCLIM** (`rad_opera_*_euradclim_*`) — KNMI produces this **OPERA-based European** climatological gauge-adjusted dataset; it is pan-European (not the national network) and climatological, and relates to the [OPERA composite](../eu/opera-composite.md) rather than here.
- **CESAR IDRA / TARA** (`cesar_idra_*`, `cesar_tara_*`) — research X-band radars at the Cabauw observatory, not part of the operational national network.
- **Bird density** (`radar_bird_density_composite_nl`) — a niche biological product derived from the radars.

---

## Notes
- **Observation, not forecast.** These are observational/analysis products. The gauge-adjusted QPE and composites blend radar with the KNMI rain-gauge network (and, near the edges, neighbouring foreign radars) — they are multi-sensor analyses, not raw radar.
- **KNMI HDF5, not ODIM.** Unlike most European national feeds in this catalog (ČHMÚ, DWD single-site, UK, Slovakia — all ODIM HDF5), KNMI uses its **own legacy HDF5 structure**: top-level groups `overview`, `geographic` (with `map_projection`), `image1…N` (`image_data` + `calibration`), `radar1…N`, and `scan1…N` for volumes — rather than ODIM's `dataset1/data1` + `/what` `/where` `/how` convention. Standard ODIM readers (`wradlib`/`xradar` ODIM backends) will not parse these directly; read them with `h5py` per KNMI's format documentation, or use the tooling in the KNMI-OSS radar repository.
- **Grid naming.** `RAD_NL25` is the 1 km national composite grid (700 × 765); `RAD_NL21` is a second grid used for some climatological accumulation products. Radar site codes `NL61` (Den Helder) and `NL62` (Herwijnen) appear in the volume filenames.
- **Relationship to NWP.** KNMI radar reflectivity and QPE feed precipitation nowcasting and are assimilated into the HARMONIE-AROME analysis.
- **Data feed vs viewer.** KNMI's public rainfall-radar map and app loops are rendered viewers; the gridded feed is the HDF5/NetCDF here.
- **Single-site vs composite.** `radar_volume_*` = per-radar polar volumes (azimuthal-equidistant, centred on the radar); the composite and QPE products = national RAD_NL25 grids.

---

## Official documentation
- KNMI Data Platform (dataset search): https://dataplatform.knmi.nl/
- KNMI Open Data API documentation: https://developer.dataplatform.knmi.nl/open-data-api
- KNMI-OSS radar datasets repository: https://gitlab.com/KNMI-OSS/radar/datasets
- Radar/gauge QPE data description paper (ESSD): https://doi.org/10.5194/essd-17-4715-2025
- KNMI HARMONIE/parameter and format context: https://english.knmidata.nl/open-data
