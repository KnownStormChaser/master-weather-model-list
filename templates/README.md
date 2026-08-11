# Templates

These templates are here to make adding new model entries fast and consistent.

The available templates are:
- [`nwp-model.template.md`](./nwp-model.template.md) — deterministic numerical weather prediction models (global and regional)
- [`ensemble-model.template.md`](./ensemble-model.template.md) — ensemble forecast systems (global and regional)
- [`wave-model.template.md`](./wave-model.template.md) — ocean wave forecast models (deterministic and ensemble)
- [`storm-surge-model.template.md`](./storm-surge-model.template.md) — storm surge and coastal water level forecast systems (deterministic and ensemble)
- [`ocean-model.template.md`](./ocean-model.template.md) — ocean physics forecast models (temperature, salinity, currents, sea level, sea ice)
- [`tropical-cyclone-model.template.md`](./tropical-cyclone-model.template.md) — tropical cyclone / hurricane forecast models
- [`air-quality-model.template.md`](./air-quality-model.template.md) — air quality and atmospheric composition models
- [`nowcasting-model.template.md`](./nowcasting-model.template.md) — nowcasting systems (observation extrapolation, seamless extrapolation–NWP blends, and ML-based 0–6 h prediction)
- [`climate-model.template.md`](./climate-model.template.md) — long-range coupled forecast systems (sub-seasonal, seasonal, and interannual prediction)
- [`hydrological-model.template.md`](./hydrological-model.template.md) — river discharge, runoff, and flood forecast systems (deterministic, ensemble, and seasonal)
- [`fire-danger-model.template.md`](./fire-danger-model.template.md) — fire danger index and fire behaviour forecast systems
- [`space-weather-model.template.md`](./space-weather-model.template.md) — Earth-referenced space weather forecast systems (ionosphere, thermosphere, aurora, ground geomagnetic and geoelectric effects)

How to use:
1. Copy the template that best fits the model category.
2. Rename the file to the model name (e.g. `gfs.md`, `gefs.md`, `ukmo-wave.md`).
3. Place the file in the appropriate directory under `models/` (by type, geographic scope, and country or organization).
4. Fill in all fields you can verify from official documentation.
5. If a field is unknown, leave `TBD` (do not guess).
6. Prefer official docs for: resolution, vertical levels, run frequency, forecast length, and download format.

Tips:
- If the model is part of a family (e.g., ICON vs ICON-EPS, IFS vs EPS), add a short note in **Notes**.
- If there are multiple domains (e.g., several coastal wave domains), make separate entries unless the operator treats them as a single product.
- **Phenomenon ensembles use their phenomenon template, not `ensemble-model.template.md`.** Wave, storm surge, and air quality ensembles derive their spread largely from the driving atmospheric ensemble and share the deterministic system's physics and chemistry, so the wave, surge, and air quality templates each carry an optional **Ensemble configuration** section instead. File them under `models/wave_models/`, `models/storm_surge_models/`, or `models/air_quality_models/` next to their deterministic siblings, cross-linked in both directions — the phenomenon-over-ensemble-status convention used throughout the repository. `ensemble-model.template.md` remains the right choice for ensembles of the atmospheric *state* itself.
- **Air quality ensembles add one wrinkle the marine categories do not have:** multi-model systems such as CAMS Regional, whose members are independently developed models and whose spread is structural rather than perturbation-based. The air quality template's ensemble block opens with an **Ensemble type** field for exactly this reason; fill it in first.
- **Storm surge entries admit station (point) time series** as well as gridded fields, as a documented exception to the repository's gridded-data scope rule. Point NetCDF at tide gauge locations is the dominant native distribution form for this model class. Record which geometry is available under **Output geometry**; rendered graphics and viewer-only products remain excluded.
- **Seasonal forecasts of a phenomenon use the phenomenon's template, not `climate-model.template.md`.** A seasonal river discharge or fire danger forecast is an impact model driven by a long-range atmospheric system, standing in the same relation to that system as a wave model does to its driving NWP model. File it under `models/hydrological_models/` or `models/fire_danger_models/` beside its medium-range sibling and cross-link the driving system's entry in `climate_models/`, rather than duplicating that system's coupled configuration. The hydrological and fire danger templates each carry an optional **Long-range configuration** section for the hindcast period, reference climatology, and sources of predictability that long-range forecasts genuinely require — the same pattern as the optional **Ensemble configuration** sections. `climate-model.template.md` remains the right choice for long-range forecasts of the atmospheric or coupled *state* itself, including statistical or dynamical downscalings of it.
- **Hydrological entries admit reach-based vector output** on a river network, as a documented exception to the gridded-data scope rule, paralleling the station time series exception for storm surge. Note that systems publishing per-reach channel output usually also publish gridded land surface and terrain routing fields from the same cycle — check before assuming the vector product is all there is, and record what is available under **Output geometry**.
- **Fire danger and space weather entries both hinge on a distinction the operator's own labelling will not settle.** For fire danger it is WMS versus WCS: a service advertising fire danger layers over WMS returns rendered images and is out of scope, while WCS GetCoverage returns the underlying raster and is in scope. The two endpoints frequently expose different layer lists, so verify with a real GetCoverage request. For space weather it is Earth-referenced versus Sun-referenced, and forecast versus zero-lead specification; both templates open with a scope block to force these before drafting.
- If the public data is a subset of the full operational model, mention that under **Notes**.
- If the model is being retired, upgraded, or has version-specific details worth tracking, consider adding a **Version history** or **Status** section as seen in existing entries like `nbm.md`, `rrfs.md`, and `raqdps.md`.
- For AI-based or hybrid systems, the NWP or ensemble template is usually the right starting point — note the AI approach in the **What this model is** section and update the repository's [`AI_MODELS.md`](../AI_MODELS.md) index when adding a new one.
