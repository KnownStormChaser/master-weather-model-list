# Bris

## What this model is
Bris is MET Norway's operational data-driven (machine-learning) ensemble forecast model. Unlike physics-based systems such as [MEPS](../../../nwp_models/regional/norway/meps.md), Bris does not integrate equations of motion or physical parameterizations. It is a neural network trained on historical gridded NWP analyses, learning the transition from one analysis to the next and rolling that transition forward autoregressively.

Bris is a **global model on a stretched grid**: high resolution (2.5 km) over MET Norway's Nordic and Arctic focus areas, coarsening to ~31 km elsewhere. Only the Nordic sub-domain is currently distributed.

The computational cost of inference is low enough that MET Norway can afford a seamless 2.5 km **ensemble** out to ten days — the capability that motivates running Bris alongside MEPS, whose 2.5 km ensemble reaches only 61 hours. Bris is described by MET Norway as operational but **experimental**, and they are actively soliciting user feedback.

**Bris has no deterministic run and no control member.** Every distributed stream is an ensemble. This distinguishes it from every other MET Norway forecast system in this catalog and is a consequence of the design rather than a gap in distribution: spread in a probabilistically-trained network arises from stochastic generation at inference, not from perturbing initial conditions around an unperturbed control integration.

Indexed in [`AI_MODELS.md`](../../../../AI_MODELS.md) under *AI-based ensembles*.

---

## Who runs it
- **Organization:** Meteorologisk institutt (MET Norway — Norwegian Meteorological Institute)
- **Country / region:** Norway
- **Contact:** `bris@met.no`; dataset issues via the [NWPdocs issue tracker](https://github.com/metno/NWPdocs/issues)

---

## What area it covers
- **Coverage:** Global model; **Nordic sub-domain only is distributed**
- **Domain details:** The published grid is the [MEPS](../../../nwp_models/regional/norway/meps.md) domain with a 50-grid-point band removed around the entire perimeter — **849 × 969** points at 2.5 km, against MEPS's 949 × 1069.
- **Projection (from file metadata, verified 2026-09-09):** Lambert conformal conic; standard parallels 63.3° / 63.3°; latitude of projection origin 63.3°N; central meridian 15.0°E; spherical Earth, semi-major = semi-minor = 6,371,000 m; false easting/northing 0. Identical projection parameters to MEPS.

---

## Basic details
- **Model type:** Ensemble NWP (machine-learning / data-driven)
- **Model system / core:** Bris — encoder–processor–decoder graph neural network on a stretched global grid. Two checkpoints are named in the file `source` attribute: a **forecaster** and a separate **interpolator**, consistent with the HourGlass temporal-downscaling architecture described in Ingstad et al. (2026).
- **Framework:** Anemoi (**TBD — inferred, not stated on MET Norway's wiki page**). The inference code is `bris-inference` per the file `history` attribute, and the Nordhagen et al. (2025) author list includes ECMWF Anemoi developers. Worth confirming from an official MET Norway source before asserting this in [`AI_MODELS.md`](../../../../AI_MODELS.md).
- **Dynamical formulation:** Not applicable — data-driven; no dynamical core
- **Convection-allowing:** Not applicable in the parameterization sense. Effective 2.5 km grid spacing over the Nordic domain; whether convective-scale structure is resolved is a property of the training data and the trained network, not of a convection scheme.
- **Ensemble size:** **16 members** (3-hourly stream) and **30 members** (12-hourly stream). **No control member** — the `ensemble_member` coordinate runs 0–15 and 0–29 respectively, carries `standard_name = "realization"`, and holds no attribute distinguishing member 0 from the rest.
- **Horizontal resolution:** 2.5 km over the Nordic and Arctic focus areas, ~31 km elsewhere on the global stretched grid
- **Grid dimensions:** 849 × 969 (distributed Nordic domain)
- **Vertical levels:** Not applicable in the model-level sense; **not distributed**. MET Norway states Bris is a full 3D atmospheric model but that only a subset of parameters is written out. The only above-surface output is a **single 850 hPa level** in the 12-hourly stream (`pressure = 1`, value 850.0 hPa, verified 2026-09-09). The 3-hourly stream carries no upper-air fields at all.
- **Model top:** Not applicable / not published
- **Forecast length:** **90 h** (3-hourly stream, hourly steps 0–90 h, 91 time records) and **258 h** (12-hourly stream, 6-hourly steps 0–258 h, 44 time records)
- **Update frequency / cycles:** 3-hourly (00/03/06/09/12/15/18/21 UTC) for the 16-member stream; 12-hourly (00/12 UTC) for the 30-member stream
- **Delivery delay:** 2 h 10 min (3-hourly stream); 7 h 30 min (12-hourly stream)
- **Temporal output resolution:** 1 h (3-hourly stream); 6 h (12-hourly stream)

---

## Data assimilation
- **Data assimilation:** **No.** Bris performs no analysis of its own. It is initialized from the analyses and forecasts of other systems (see below), which remain dependent on conventional physics-based data assimilation.

---

## Initial and boundary conditions
- **Initial conditions:** A **MEPS + ECMWF IFS hybrid**, differing by stream. For the 00Z cycle, MET Norway documents:
  - 3-hourly stream — 00Z MEPS and **18Z IFS forecasts**
  - 12-hourly stream — 00Z MEPS and **00Z IFS analyses**

  The wiki states this only for the 00Z cycle; whether the same MEPS/IFS pairing and IFS lag apply at other cycle times is **TBD**.
- **Boundary conditions:** **Not applicable.** Bris is global on a stretched grid and takes no lateral boundary conditions. This is a structural difference from MEPS and [AROME-Arctic](../../../nwp_models/regional/norway/arome-arctic.md), both of which are limited-area models driven by IFS at the boundaries.

---

## Perturbations and design
- **Initial condition perturbations:** **TBD.** Not documented. Bris draws initial conditions from MEPS, itself a 30-member time-lagged ensemble, so member-to-member correspondence with MEPS members is a natural question but is not stated anywhere in MET Norway's documentation.
- **Model/physics perturbations:** Not applicable — no parameterizations to perturb.
- **Stochastic schemes:** **TBD.** Nordhagen et al. (2025) describe a probabilistically-trained stretched-grid network; whether the distributed operational ensemble generates spread through noise injection at inference (the AIFS ENS pattern), through multiple trained checkpoints (the AIGEFS pattern), through inherited MEPS/IFS initial-condition spread, or through a combination is **not documented on the wiki**. This matters for interpreting what the spread represents and should not be assumed. See [`AI_MODELS.md`](../../../../AI_MODELS.md), which draws exactly this distinction between AIFS ENS and AIGEFS.

---

## What it provides

Thirteen forecast fields are present in **both** streams (verified against live OPeNDAP DDS, 2026-09-09):

- `air_pressure_at_sea_level`, `surface_air_pressure` (Pa)
- `air_temperature_2m`, `dew_point_temperature_2m` (K)
- `x_wind_10m`, `y_wind_10m` (m s⁻¹) — 10-minute averages
- `precipitation_amount_acc` (kg m⁻²) — **accumulated from forecast start**, not per step
- `cloud_area_fraction`, plus `low_type_cloud_area_fraction` (< 2000 m), `medium_type_cloud_area_fraction` (2000–5000 m), `high_type_cloud_area_fraction` (> 5000 m)
- `integral_of_surface_downwelling_shortwave_flux_in_air_wrt_time` and the longwave equivalent (W s m⁻²) — integrated over the hour ending at the timestamp

Plus `altitude` as a static field, and `latitude` / `longitude` as 2D auxiliary coordinates.

The **12-hourly 30-member stream additionally carries six fields on a single 850 hPa level**: `air_temperature_pl`, `geopotential_pl`, `specific_humidity_pl`, `x_wind_pl`, `y_wind_pl`, `vertical_velocity_pl`.

**Documentation–data discrepancy.** MET Norway's parameter table lists three fields that are present in neither stream: `air_temperature_0m`, `relative_humidity_2m`, and `land_area_fraction`. Verified absent 2026-09-09. Read the wiki table as forward-looking rather than current.

**No derived ensemble products are published** — no mean, spread, percentiles, or exceedance probabilities. Members are distributed raw, packaged as an `ensemble_member` dimension within a single NetCDF file per cycle rather than as one file per member.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** Norwegian Licence for Open Government Data (NLOD) and Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution required: "MET Norway" or equivalent. **A file-level `license` attribute is present** and points to https://www.met.no/en/free-meteorological-data/Licensing-and-crediting — a cleaner licensing position than several other MET Norway THREDDS products in this catalog (compare [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), where no per-file licence is declared).
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF4 (CF-1.6 declared). **Direct NetCDF download and OPeNDAP both available; no NCML aggregations** — a departure from MEPS and AROME-Arctic, where most latest-catalogue products are NCML-only.
- **Official download location:**
  - Catalogue: https://thredds.met.no/thredds/catalog/brisnordiclatest/catalog.html (also reachable from https://thredds.met.no/thredds/catalog/metno.html under *Other products / Bris*)
  - HTTP file download: `https://thredds.met.no/thredds/fileServer/brisnordiclatest/{filename}`
  - OPeNDAP: `https://thredds.met.no/thredds/dodsC/brisnordiclatest/{filename}`
- **Filename convention:**
  - `bris_ens_2_5km_nordic_{YYYYMMDD}T{HH}Z.nc` — 16 members, hourly to 90 h
  - `bris_ens_2_5km_6h_nordic_{YYYYMMDD}T{HH}Z.nc` — 30 members, 6-hourly to 258 h
- **Retention:** **Rolling ~3 days. There is no archive.** MET Norway states that archiving of operational runs is not yet set up and will be added later, and that a separate rerun archive covering an extended past period is planned. Neither exists as of 2026-09-09 — probes for `brisnordicarchive`, `brisarchive`, `bris`, and `brisnordic` catalogues all returned HTTP 404.
- **Per-file volume (verified 2026-09-09):** ~58.2 GiB per 16-member file (62,445,056,748 bytes); ~77.1 GiB per 30-member file (82,734,211,171 bytes). At eight and two cycles per day respectively this is roughly **620 GiB/day**, uncompressed. Subsetting via OPeNDAP or the THREDDS NetcdfSubset service is effectively mandatory for most uses.
- **Redundancy:** The real-time stream is produced in two separate data halls for high availability.

---

## Notes
- **Relationship to MEPS.** Bris is not a counterpart to a deterministic sibling in the [AIFS Single](../../../nwp_models/global/eu/aifs-single.md) / [AIFS ENS](../../global/eu/aifs-ens.md) sense — there is no deterministic Bris. Its relationship to [MEPS](../../../nwp_models/regional/norway/meps.md) is twofold: MEPS analyses are the training data, and MEPS output supplies part of the initial conditions for every run. Bris therefore cannot be treated as an independent forecast for verification or blending purposes.
- **Relationship to other Norwegian entries.** Distinct from [AROME-Arctic](../../../nwp_models/regional/norway/arome-arctic.md) in domain, method, and the absence of any deterministic or control output. MET Norway's MET Nordic 1 km post-processed product and the MetCoOp MECaS calibrated ensemble are separate downstream products built on MEPS, not on Bris. On the marine side, MET Norway also runs the data-driven **AICE** sea ice system (see [Barents-2.5km EPS](../../../ocean_models/regional/norway/barents-25km-eps.md)), not yet catalogued.
- **Relationship to other national AI systems.** Comparable in intent to DWD's [AICON](../../../nwp_models/global/germany/aicon-global.md) — a national service running a data-driven model as a complement to, not a replacement for, its physics-based suite. Bris differs in being ensemble-only and in publishing a high-resolution regional sub-domain of a global stretched grid rather than the full global field.
- **Global model, regional entry — filing note.** Bris is architecturally global but distributes only a Nordic sub-domain. It is filed under `ensemble_models/regional/norway/` on the basis of what is actually published. If MET Norway later distributes the global field, the entry should be revisited. Compare the reverse case in [MEPS](../../../nwp_models/regional/norway/meps.md), which is an ensemble filed under `nwp_models/` and flagged for possible relocation.
- **Metadata quality issues (verified 2026-09-09).** The files declare `Convensions = "CF-1.6"` — the attribute name is **misspelled**, so CF-aware tooling keying on `Conventions` will not find a conventions declaration. There is no `title`, `summary`, `references`, or other ACDD metadata beyond `institution`, `license`, `creator_url`, `source`, `history`, and `comment`. The `comment` attribute reads `"Generated by Bris staging in PPI"`, consistent with the experimental framing and likely to change when the system moves off staging.
- **Experimental status is explicit.** MET Norway labels Bris experimental on the wiki and asks for user feedback. The *Known data issues* section of the wiki page exists as a heading but is **currently empty**. Treat this as a system likely to change without a version bump.
- **No changelog exists for Bris.** MET Norway maintains no per-model changelog for it, and the NWPdocs MEPS Changelog does not cover it. The announcement channel for MET Norway model changes in practice is the Statuspage at https://status.met.no (machine-readable at `https://status.met.no/api/v2/scheduled-maintenances.json`), which is where the AROME-Arctic cy46 upgrade was announced roughly six weeks in advance. Nothing Bris-related has been posted there as of 2026-09-09.
- **Wiki page is hard to find.** The NWPdocs wiki sidebar collapses at 15 entries and the wiki now has 17 pages, so *Bris* sits behind a "Show 1 more pages…" link and does not appear in the visible menu. Direct URL: https://github.com/metno/NWPdocs/wiki/Bris
- **Follow-up:** add to [`AI_MODELS.md`](../../../../AI_MODELS.md) under *AI-based ensembles*, and to [`STATUS.md`](../../../../STATUS.md) as an experimental system with a pending operational archive.

---

## Recent version history

No version history is published, and MET Norway assigns no version identifier to the operational Bris release. The following is reconstructed from documentation dates and live inspection.

### 2026-09-08 — Wiki documentation published
MET Norway added the *Bris* page to the NWPdocs wiki and updated the wiki Home page (2026-09-07) to list Bris among its available products. The model was already producing operationally on THREDDS before this date; the documentation date is not the operational start date, which is **TBD**.

---

## Official documentation
- https://github.com/metno/NWPdocs/wiki/Bris
- https://github.com/metno/NWPdocs/wiki/Home
- https://github.com/metno/NWPdocs/wiki/Data-access
- https://www.met.no/en/free-meteorological-data/Licensing-and-crediting

## References
- E. M. Nordhagen, H. H. Haugen, A. F. S. Salihi, M. S. Ingstad, T. N. Nipen, I. A. Seierstad, I.-L. Frogner, M. C. A. Clare, S. Lang, M. Chantry, P. Dueben, and J. Kristiansen, 2025: *High-Resolution Probabilistic Data-Driven Weather Modeling with a Stretched Grid.* Preprint, [arXiv:2511.23043](https://arxiv.org/abs/2511.23043).
- M. S. Ingstad, M. C. A. Clare, O. Ersland, V. Gahlen, H. H. Haugen, O. Miralles, E. M. Nordhagen, T. N. Nipen, I. A. Seierstad, J. B. Bremnes, M. Maier-Gerber, Z. B. Bouallègue, H. Cook, C. Lessig, G. Mertes, C. O'Brien, F. Pinault, A. P. Nemesio, and M. Chantry, 2026: *HourGlass: A Probabilistic Data-Driven Temporal Downscaler for Global and Regional Weather Forecasting.* Preprint, [arXiv:2607.11457](https://arxiv.org/abs/2607.11457).
