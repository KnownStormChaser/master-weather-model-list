# NWATL (Northwestern Atlantic Ocean Physics and Ice — Copernicus redistribution of CIOPS-East)

## What this model is
NWATL is the Copernicus Marine **COLAB-MFC** near-real-time ocean physics and sea ice product for the Northwestern Atlantic along the North American coastline. It is **not a new modelling system**: it is a reformatted redistribution of **[CIOPS-East](../canada/ciops.md)**, Environment and Climate Change Canada's coastal ice-ocean forecast system, converted into the Copernicus Marine format by **NOW Systems** (Spain) using **CESGA** supercomputing resources.

Live files state this outright — every dataset carries:

```
source      = "Coastal Ice Ocean Prediction System East : version 230 ; CIOPSE_230_F"
institution = "NOW Systems (Spain) - The Canadian Centre for Meteorological and Environmental Prediction"
```

This is the first Copernicus Marine regional physics product whose modelling system sits **outside Europe** and whose operator is a non-European national service. It is also the newest: the PUM is Issue 1.0, approved **July 2026**, with NRT delivery beginning 7 July 2026 and a back-catalogue of best estimates from **21 May 2025**.

> **The Copernicus copy is a version behind the source.** Files stamp CIOPS-E **version 230**; ECCC's own distribution has been at **version 240** since 14 April 2026. See *Relationship to other entries*.

The official Copernicus Marine product identifier is `NWATL_ANALYSISFORECAST_PHY_ICE_017_001`.

*Verified 2026-08-25 from the PUM (Issue 1.0, July 2026), Marine Data Store STAC records, and live ARCO Zarr metadata and coordinate arrays.*

---

## Who runs it
- **Modelling system developed and run by:** **Environment and Climate Change Canada (ECCC)**, at the Canadian Centre for Meteorological and Environmental Prediction (CCMEP)
- **Copernicus Production Unit:** **NOW Systems** (Spain), with supercomputing support from **CESGA**. Copernicus metadata records the producer as `COLAB-NOW-MADRID-ES`.
- **Country:** Canada (modelling) / Spain (reprocessing and delivery)
- **Programme or coordinating body:** Copernicus Marine Service — **COLAB-MFC**, a new Monitoring and Forecasting Centre built around collaboration with non-European partners
- **Role in any larger system:** a redistribution endpoint, not a source. NWATL feeds nothing; it consumes CIOPS-E output.

> **Operator-country filing.** Under the repository's operator-based directory convention this is genuinely ambiguous: the model is Canadian, the Copernicus Production Unit is Spanish. Filed here under `canada/` alongside the [CIOPS](../canada/ciops.md) and [RIOPS](../canada/riops.md) entries, on the grounds that the modelling system determines what the data *is*, while NOW Systems only reformats it. Noted because it is the kind of non-obvious placement [`COPERNICUS.md`](../../../../COPERNICUS.md) exists to explain.

---

## What area it covers
- **Coverage:** Northwestern Atlantic along the North American coastline — Gulf of St. Lawrence, Scotian Shelf, Gulf of Maine, Grand Banks, and the adjacent open Atlantic
- **Domain bounds (documented and live-verified, in agreement):** **77.00°W – 37.04°W, 34.88°N – 54.46°N**
- **Grid dimensions (live-verified):** **1333 × 980** (longitude × latitude) — the PUM writes this as "980x1333", i.e. latitude × longitude
- **Grid spacing (live-verified from the coordinate arrays):** **0.03° longitude × 0.02° latitude**

> ### ⚠ The grid is not 1/36°, despite the PUM and the dataset names
> The PUM states *"Latitude and longitude step is 0.02777863°"* and gives the resolution as 1/36°; the dataset identifiers all contain **`0.027deg`**. Neither is correct.
>
> Direct inspection of the delivered coordinate arrays gives **latitude step exactly 0.02°** (980 points, 34.88 → 54.46) and **longitude step exactly 0.03°** (1333 points, −77.00 → −37.04). The grid is **anisotropic and neither axis is 1/36°**.
>
> `0.02777863` is precisely the [IBI product's](../spain/ibi-phy-005-001.md) grid step, and IBI is run by the same Production Unit. The figure and the `0.027deg` dataset token appear to have been carried over from the IBI templates without being updated. The *physical* resolution claim (~2–2.5 km) is roughly right — 0.02° latitude is ~2.22 km, 0.03° longitude is ~2.1–2.5 km across the domain — but any code deriving grid geometry from the stated step or the dataset name will be wrong.

- **Vertical levels:** **99 z-levels**, live-verified from 0.506 m to 5657.810 m
- **Minimum depth:** **7.5 m**, imposed to accommodate high tides

---

## Basic details
- **Model type:** Regional coastal ocean physics and sea ice; deterministic; **no data assimilation**
- **Core ocean model:** **NEMO 3.6** (Madec et al., 2017)
- **Sea ice model:** **CICE 6.2.0** (Hunke et al., 2021)
- **System name:** **CIOPS-E version 230**
- **Horizontal resolution:** ~2–2.5 km on a 0.03° × 0.02° grid (see the warning above)
- **Vertical levels:** 99 z-levels
- **Forecast length:** **48 hours**
- **Update frequency:** **4× daily** (00Z, 06Z, 12Z, 18Z), mirroring the ECCC schedule
- **Target delivery time:** 6-hourly; first delivery of the day at **10:00 UTC**
- **Temporal output resolution:** **hourly instantaneous** — necessary rather than convenient, since the currents contain the full tidal signal
- **Archive availability:** continuous best-estimate series from **2025-05-21**, built by archiving the **0–6 h segment** of each cycle while the full 48-hour forecast is overwritten every 6 hours
- **Bathymetry source:** **SRTM30** (Becker et al. 2009) and **GoMSS** (Katavouta et al. 2016), plus additional observations from the Department of Fisheries and Oceans

### There is no data assimilation
The PUM lists both the assimilation scheme and the assimilated observations as **N/A**. CIOPS-E runs a *pseudo-analysis* forced at the ocean boundaries by [RIOPS](../canada/riops.md) forecasts and spectrally nudged to the RIOPS solution in the deep ocean. Observational constraint is therefore inherited from RIOPS rather than applied here, and skill at any location is bounded by RIOPS at the nearest assimilated information.

- **Initial conditions (00Z):** the pseudo-analysis described above
- **Initial conditions (06Z, 12Z, 18Z):** restart files saved at hour 6 of the preceding forecast

---

## Forcing
- **Atmospheric forcing:** horizontal and temporal interpolation of **Global Deterministic Prediction System G1 v9.0.0** blended with the **High-Resolution Deterministic Prediction System (HRDPS) v7.0.0** where coverage allows
- **River runoff:** the **R1D river model** for the St. Lawrence; climatological values elsewhere (Dai and Trenberth 2002; Saucier et al. 2003), with specific treatment at river mouths
- **Lateral boundary conditions:** **[RIOPS](../canada/riops.md)** forecasts, with spectral nudging to RIOPS below 1500 m
- **Tidal forcing:** explicit, **13 constituents — M2, N2, S2, K2, K1, O1, Q1, P1, M4, Mf, Mm, Mn4, Ms4** — from **WebTide**. The largest tidal set of any product in this comparison group, appropriate for a domain containing the Bay of Fundy.

**Currents include tides.** As with the ECCC-side [CIOPS](../canada/ciops.md) and [RIOPS](../canada/riops.md) products, `uo`, `vo`, and `zos` carry the tidal signal. There are no de-tided datasets — a notable difference from every European Copernicus regional physics product, all of which ship Doodson-filtered variants.

---

## Coupling
- **Ocean–sea ice:** NEMO 3.6 coupled to CICE 6.2.0
- **Ocean–wave:** none. The PUM lists **Coupling: N/A**.
- **Atmosphere:** one-way forcing from GDPS G1 / HRDPS; no coupled atmosphere

Unlike the Mediterranean and Black Sea products, there is no internal wave model here, and no Copernicus wave product covers this domain.

---

## Data assimilation
**None.** See *Basic details*. Both the scheme and the observation list are recorded as N/A in the PUM.

This makes NWATL the only product in the Copernicus Marine regional physics portfolio without its own assimilation — the [Marmara sub-system](../italy/blk-phy-007-001.md) of the Black Sea product is the closest parallel, and that is a component rather than a whole product.

---

## What it provides

Eight datasets, all hourly instantaneous, all on the 1333 × 980 grid. Variable names and units live-verified.

### Ocean physics
| Variable | Standard name | Units | Dimensionality | Dataset |
|---|---|---|---|---|
| `thetao` | `sea_water_potential_temperature` | **K** | 3D (99) | `…phy_temp…` |
| `so` | `sea_water_salinity` | 1e-3 | 3D (99) | `…phy_sal…` |
| `uo` / `vo` | `eastward` / `northward_sea_water_velocity` | m s⁻¹ | 3D (99) | `…phy_cur…` |
| `zos` | `sea_surface_height_above_geoid` | m | 2D | `…phy_ssh…` |
| `mlotst` | `ocean_mixed_layer_thickness_defined_by_sigma_theta` | m | 2D | `…phy_mld…` |

### Sea ice
| Variable | Standard name | Units | Dataset |
|---|---|---|---|
| `iceconc` | `sea_ice_area_fraction` | 1 | `…ice…` |
| `icevol` | `sea_ice_thickness` | m | `…ice…` |
| `inowvol` | `surface_snow_thickness_where_sea_ice` | m | `…ice…` |
| `icestrength` | `compressive_strength_of_sea_ice` | Pa m | `…ice…` |
| `icepressure` | `sea_ice_internal_pressure` | Pa m | `…ice…` |
| `icedivergence` | `divergence_of_sea_ice_velocity` | s⁻¹ | `…ice…` |
| `iceshear` | `maximum_over_coordinate_rotation_of_sea_ice_horizontal_shear_strain_rate` | s⁻¹ | `…ice…` |
| `iitzocrtx` / `iitmecrty` | `sea_ice_x_velocity` / `sea_ice_y_velocity` | m s⁻¹ | `…ice_cur…` |
| `icesurftemp` | `surface_temperature` | **K** | `…ice_temp…` |

The **ice mechanics set** — compressive strength, internal pressure, divergence, shear — is unusually rich and is inherited directly from ECCC's CICE output. Only [GIOPS](../canada/giops.md) and CIOPS-East carry the equivalent among products in this repository.

> **Temperature is in Kelvin.** `thetao` and `icesurftemp` are delivered in **K**, not degrees Celsius. Every European Copernicus Marine regional physics product delivers `degrees_C`. This is ECCC's native convention surviving the reformatting, and it is a silent interoperability trap for anyone applying a common Copernicus processing chain across basins.

> **`inowvol` is a misspelling of `isnowvol`.** The snow-volume variable name has lost its `s`. The standard name is correct (`surface_snow_thickness_where_sea_ice`), so the field is identifiable, but any code selecting by variable name against ECCC's `isnowvol` will miss it. A data-level defect, live-verified across the archive.

> **`icevol` is named as a volume but labelled as a thickness.** ECCC documents its `iicevol` explicitly as *sea ice volume per unit grid cell area — not ice thickness*, a distinction flagged in the [GIOPS entry](../canada/giops.md). The Copernicus copy retains the volume-derived name while assigning `standard_name = sea_ice_thickness`. For CICE output the two are numerically the same quantity (grid-cell-mean thickness), so this is defensible, but the name and the standard name disagree about which it is, and users moving between the ECCC and Copernicus copies should not assume identical semantics (**flagged, not resolved**).

**Ice velocity variable names carry a doubled prefix.** ECCC's NEMO short names are `itzocrtx` / `itmecrty`; the delivered Copernicus names are `iitzocrtx` / `iitmecrty`. Another apparent transcription artifact.

### Static fields
**None distributed.** No bathymetry, land-sea mask, or coordinate/cell-area dataset — unlike every European Copernicus regional physics product, all of which ship a `static` dataset. The domain mask must be inferred from `_FillValue`. This matches the ECCC-side behaviour documented in the [CIOPS entry](../canada/ciops.md).

---

## Data availability
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence (free after self-registration). *The ECCC source distribution is under the more permissive ECCC Data Servers End-use Licence v2.1, which permits commercial use with attribution and requires no registration — see* Relationship to other entries.
- **Data formats:** NetCDF-4, **CF-1.11** — the newest CF version in the Copernicus regional physics portfolio (Med and IBI at 1.8, NWS at 1.7, Black Sea at 1.6)
- **Product identifier:** `NWATL_ANALYSISFORECAST_PHY_ICE_017_001`
- **DOI:** https://doi.org/10.48670/mds-00381
- **Delivery mechanism:** Copernicus Marine Toolbox
- **ARCO endpoints:** `https://s3.waw3-1.cloudferro.com/mdl-arco-time-017/arco/NWATL_ANALYSISFORECAST_PHY_ICE_017_001/…`
- **Datasets** (all hourly instantaneous, all tagged `_202607`):
  - `cmems_mod_nwatl_phy_cur_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_phy_temp_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_phy_sal_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_phy_ssh_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_phy_mld_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_ice_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_ice_cur_anfc_0.027deg_PT1H-i`
  - `cmems_mod_nwatl_ice_temp_anfc_0.027deg_PT1H-i`
- **Live archive extent (verified 2026-08-25):** 2025-05-21T00:00Z → 2026-08-26T18:00Z, **11 107 hourly steps**. A continuous 15-month series would be ~11 100 hours, so coverage is close to complete with a small number of gaps.
- **Archive construction:** the full 48-hour forecast is overwritten every 6 hours; only the **0–6 h segment** of each cycle is retained to build the best-estimate series. Users pulling the recent edge get forecast; users pulling historically get a stitched analysis-equivalent.
- **Official access:** https://data.marine.copernicus.eu/product/NWATL_ANALYSISFORECAST_PHY_ICE_017_001/description

> **Verified 2026-08-25.** Grid dimensions and spacing (from coordinate arrays, not metadata), vertical level count and range, per-dataset variable inventory, units, global attributes including the CIOPS-E version stamp, and archive extent confirmed from Marine Data Store STAC records and live ARCO Zarr data.

---

## Relationship to other entries

- **Same run, different distribution — and different versions.** This product is a reformatting of **[CIOPS-East](../canada/ciops.md)**. Identity is **confirmed from data**: the `source` attribute reads `Coastal Ice Ocean Prediction System East : version 230 ; CIOPSE_230_F`.

  **The two channels are not equivalent:**

  | | **ECCC MSC Datamart** | **Copernicus Marine (this entry)** |
  |---|---|---|
  | CIOPS-E version | **240** (since 2026-04-14) | **230** |
  | Registration | None | Required (free) |
  | Licence | ECCC End-use v2.1, commercial use permitted | Copernicus Marine licence |
  | Retention | **30 days** | **Continuous from 2025-05-21** |
  | Format | NetCDF-4, CF-1.6, CMC `nomvar` codes | NetCDF-4, CF-1.11, CF-normalised names |
  | Temperature units | Kelvin | **Kelvin** (unchanged) |
  | Domains | East, West, Salish Sea | **East only** |
  | Static fields | None | None |

  **The version gap is the significant finding.** ECCC moved CIOPS-E to v2.4.0 on 14 April 2026 as an HPC-infrastructure port that the [CIOPS entry](../canada/ciops.md) records as *computationally only*. If that characterisation holds, the scientific content of v230 and v240 is identical and the gap is cosmetic. But the Copernicus product went live in July 2026 stamped with the **older** version, three months after the ECCC changeover — so either the COLAB chain is running from a frozen v230 configuration, or the stamp was not updated. **Flagged, not resolved**; worth a question to the COLAB-MFC.

  **Neither channel is a superset.** Copernicus offers a 15-month archive against ECCC's 30-day window — the decisive advantage for any retrospective work. ECCC offers the current model version, no registration, a commercial-use licence, and the West and Salish Sea domains that Copernicus does not carry.

- **Grandparent systems:** [RIOPS](../canada/riops.md) supplies the boundary conditions and the deep-ocean nudging target, and is where this product's observational constraint actually comes from; [GIOPS](../canada/giops.md) sits above RIOPS. Neither is redistributed through Copernicus.

- **No Copernicus wave counterpart.** Unlike every European basin in the portfolio, the NWATL domain has no companion Copernicus wave product, and this system has no internal wave model.

- **Same Production Unit as IBI.** NOW Systems runs both this and [IBI](../spain/ibi-phy-005-001.md), which explains the IBI-derived grid-step figure and `0.027deg` dataset naming carried into this product's documentation. Worth watching for other inherited-template artifacts as COLAB-MFC expands.

- **Programme context:** see [`COPERNICUS.md`](../../../../COPERNICUS.md). This is the first COLAB-MFC product and the first Copernicus Marine regional physics product operated from outside Europe — the index's operator-versus-coverage discussion should be extended to cover it.

---

## Version history

### July 2026 — PUM Issue 1.0 (record table dated 2026-02-05)
Initial version. NRT delivery from **7 July 2026**; historic best-estimate catalogue back-filled to **21 May 2025**.

There is no prior version history — this is a new Copernicus product. The underlying **CIOPS-E** system has its own lineage, documented in the [CIOPS entry](../canada/ciops.md); the version distributed here is **230**, which predates ECCC's April 2026 move to 240.

---

## Notes

- **This is a redistribution, not an independent system.** Anything about model physics, forcing, or skill traces back to ECCC's CIOPS-East. Cite ECCC for the science; cite Copernicus for this distribution.

- **The grid is 0.03° × 0.02°, not 1/36°.** The single most important correction. See the warning under *What area it covers*. The PUM figure and the `0.027deg` dataset token are both inherited from the IBI product.

- **Temperature is in Kelvin.** Unique among Copernicus Marine regional physics products. Silent failure mode for cross-basin pipelines.

- **`inowvol` is misspelled.** Should be `isnowvol`. Select by standard name, not variable name.

- **`icevol` versus `sea_ice_thickness`.** Name and standard name disagree about the quantity; ECCC documents the source field as volume per unit area. Numerically the same for CICE, semantically ambiguous as delivered.

- **Ice velocity names carry a doubled `i`.** `iitzocrtx` / `iitmecrty` against ECCC's `itzocrtx` / `itmecrty`.

- **No static fields at all.** No bathymetry, mask, or coordinate dataset — the only Copernicus regional physics product with none. Infer the mask from `_FillValue`.

- **No de-tided datasets, and currents contain tides.** With 13 tidal constituents and hourly output, sub-daily sampling is mandatory; a daily mean computed naively from this product will alias the semidiurnal signal rather than remove it. Every European sibling ships Doodson-filtered variants; this one does not.

- **No data assimilation.** Skill is bounded by RIOPS. This is a dynamical downscaling, and should not be compared like-for-like against the assimilating European regional products.

- **The archive is stitched from forecast segments.** Only the 0–6 h portion of each cycle survives. The "best estimate" series is therefore a concatenation of short-lead forecast segments, not an analysis, and there is no reanalysis-quality product behind it.

- **CF-1.11 is the newest convention version in the portfolio**, which is worth noting only because it sits alongside the Black Sea product's CF-1.6 within the same programme.

- **The product is very new and still moving.** PUM Issue 1.0 approved July 2026, NRT from 7 July 2026, and the PUM itself notes QUID and SQO availability as "tbc". Expect specifics to change; re-verify before relying on anything here.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/NWATL_ANALYSISFORECAST_PHY_ICE_017_001/description
- **Product User Manual (Issue 1.0, July 2026):** https://documentation.marine.copernicus.eu/PUM/CMEMS-COLAB-PUM-017-001.pdf
- DOI: https://doi.org/10.48670/mds-00381
- **ECCC CIOPS documentation (the actual modelling system):** https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops_en/
- ECCC CIOPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/changelog_ciops_en/
- ECCC licence: https://eccc-msc.github.io/open-data/licence/readme_en/
- NOW Systems: https://nologin.es/
- CESGA: https://www.cesga.es/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- Madec, G. and the NEMO Team (2017). NEMO ocean engine. *Note du Pôle de modélisation*, IPSL.
- Hunke, E. et al. (2021). CICE-Consortium/CICE: CICE version 6.2.0. *Zenodo.*
- Becker, J.J. et al. (2009). Global bathymetry and elevation data at 30 arc seconds resolution: SRTM30_PLUS. *Marine Geodesy*, 32, 355–371.
- Katavouta, A. et al. (2016). The Gulf of Maine and Scotian Shelf (GoMSS) model configuration.
- Dai, A. and Trenberth, K.E. (2002). Estimates of freshwater discharge from continents. *Journal of Hydrometeorology*, 3, 660–687.
- Saucier, F.J. et al. (2003). Modeling the sea ice-ocean seasonal cycle in Hudson Bay, Foxe Basin and Hudson Strait. *Climate Dynamics*, 20, 413–433.
