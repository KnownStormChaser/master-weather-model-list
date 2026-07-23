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
- **Ensemble marine systems use their phenomenon template, not `ensemble-model.template.md`.** Wave and storm surge ensembles derive their spread almost entirely from the driving atmospheric ensemble and share the deterministic system's physics, so the wave and surge templates each carry an optional **Ensemble configuration** section instead. File them under `models/wave_models/` or `models/storm_surge_models/` next to their deterministic siblings, cross-linked in both directions — the same phenomenon-over-ensemble-status convention used elsewhere in the repository. `ensemble-model.template.md` remains the right choice for atmospheric ensembles only.
- **Storm surge entries admit station (point) time series** as well as gridded fields, as a documented exception to the repository's gridded-data scope rule. Point NetCDF at tide gauge locations is the dominant native distribution form for this model class. Record which geometry is available under **Output geometry**; rendered graphics and viewer-only products remain excluded.
- If the public data is a subset of the full operational model, mention that under **Notes**.
- If the model is being retired, upgraded, or has version-specific details worth tracking, consider adding a **Version history** or **Status** section as seen in existing entries like `nbm.md`, `rrfs.md`, and `raqdps.md`.
- For AI-based or hybrid systems, the NWP or ensemble template is usually the right starting point — note the AI approach in the **What this model is** section and update the repository's [`AI_MODELS.md`](../AI_MODELS.md) index when adding a new one.
