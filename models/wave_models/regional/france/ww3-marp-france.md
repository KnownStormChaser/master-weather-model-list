# WW3-MARP (Météo-France Coastal Wave Model — Mediterranean, ARPEGE-forced)

## What this model is
WW3-MARP is the ARPEGE-forced Mediterranean configuration of Météo-France's **coastal** wave forecasting system, built on **WAVEWATCH III** (Tolman, 2002) over an **unstructured triangular mesh** (Roland et al., 2014).

It is one of three openly distributed configurations of the SHOM/Météo-France coastal wave system developed under the **HOMONIM** project (*Historique, Observation, MOdélisation des NIveaux Marins*), commissioned by the Direction Générale pour la Prévention des Risques under the interministerial *Submersions Rapides* plan and operational since **18 March 2015**. It uses MFWAM's wave physics with added coastal processes — unified breaking from offshore to shore, coastal reflection, refraction by currents and bathymetry, and bottom friction — on SHOM's HOMONIM bathymetry.

**MARP runs on a mesh byte-identical to [WW3-MARO](./ww3-maro-france.md)'s** and differs from it in exactly one input: ARPEGE winds (~10 km) rather than AROME (~1.3 km). That makes the pair an unusually clean controlled comparison of atmospheric forcing on coastal sea state — with one caveat: ARPEGE's longer range means MARP forecasts to **72 h** against MARO's 51 h, so the comparison only holds over the overlapping window.

`MARP` decomposes as **M**editerranean + **ARP**EGE.

> **Output geometry — this entry is an unstructured mesh, not a grid.** Fields are on 89,695 nodes with a 166,663-triangle connectivity table. In scope under the wave template's provision for unstructured meshes.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as co-developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **Internal product reference:** BDPE **12389** — read from the shared package documentation's three-column layout (ATL Arp 12388 / MED Arp 12389 / MED Arome 13503). The column association is inferred from ordering in a table that extracts poorly; treat as probable rather than confirmed.

---

## What area it covers
- **Coverage:** Northwestern Mediterranean — the French Mediterranean seaboard including the Gulf of Lion, the Ligurian Sea, and the waters around Corsica
- **Mesh extent (verified):** 3.034°E – 11.808°E, 40.742°N – 44.427°N
- **Reach offshore:** the documentation describes both coastal configurations as extending 200–300 km from metropolitan French coasts, Corsica included

As with [WW3-MARO](./ww3-maro-france.md), the "Méditerranée" label overstates the coverage: the mesh is confined to the northwestern basin and excludes the Adriatic, Aegean, Ionian and eastern Mediterranean. For basin-wide Mediterranean waves see [MEDWAM](../greece/medwam.md).

### Mesh geometry (verified)
- **Nodes:** 89,695
- **Elements:** 166,663 triangles (`tri` variable, 1-based node indices)
- **Edge length:** median **489 m**; 5th percentile 204 m; 95th percentile 1.99 km; minimum 19 m; maximum 18.7 km

> **The mesh is byte-identical to WW3-MARO's.** Direct array comparison of the 2026-08-10 00Z files confirms `longitude`, `latitude`, `tri` and `MAPSTA` match exactly across both products (MD5 `059b262fec`, `a3b41a6f25`, `b65896e859`, `5ec4676289` respectively). Any mesh preprocessing — triangulation, interpolation weights, coastline masks, node-to-cell mappings — computed for one product can be reused verbatim for the other.

> **Ignore the resolution figure in the product title.** Published as "Résolution 0.1°", path token `001`, and the file's own `history` attribute names it `WW3-MARP_0.01_SP1_…`. None describes an unstructured mesh. Use the edge-length distribution.

### Node masking
`MAPSTA` carries three values, identical in distribution to MARO:

| MAPSTA | Meaning | Count |
|---|---|---|
| 0 | Excluded (land) | 11,433 |
| 1 | Active sea point | 78,190 |
| 2 | Active boundary point | 72 |

**The mask is fully static.** Valid node count is **78,262 at every one of the 62 time steps** — exactly the 78,190 sea points plus the 72 boundary points — with **zero nodes changing state** across the file. `MAPSTA ≥ 1` is therefore an exact and time-invariant validity index.

> **This is the key structural contrast with [WW3-WARP](./ww3-warp-france.md).** The Atlantic configuration carries a fourth `MAPSTA` class (value 15, 2,900 intertidal nodes) and a validity mask that varies with the tide, because it ingests HYCOM2D water level. No Mediterranean configuration does, and the absence of any wetting-and-drying behaviour here is direct evidence that MARP has no water-level forcing — a point the documentation asserts only for the Atlantic.

---

## Basic details
- **Model type:** Deterministic coastal wave model
- **Grid system:** **unstructured triangular mesh** (shared with WW3-MARO)
- **Core wave model:** WAVEWATCH III (NOAA), SHOM/Météo-France unstructured implementation
- **Wave physics:** shared with MFWAM (Ardhuin et al., 2010; Janssen et al., 2014), plus coastal shallow-water processes
- **Nominal resolution:** ~200 m at the finest, ~490 m median edge length
- **Forecast length (verified):** **72 h at every cycle**
- **Update frequency / cycles (verified):** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** hourly to +48h, then 3-hourly to +72h
- **Analysis fields:** yes — 6 hourly analysed fields per cycle

### Time axis structure
Uniform across all four cycles, verified at 00Z, 06Z, 12Z and 18Z:

| Component | Leads | Steps |
|---|---|---|
| Analyses | −5h to −1h | 5 |
| Analysis at run time | 0h | 1 |
| Hourly forecast | +1h to +48h | 48 |
| 3-hourly forecast | +51h, +54h … +72h | 8 |
| **Total** | **−5h to +72h** | **62** |

The dataset description's "analyses sur les 6 dernières heures" holds at every cycle. This is **not** true of the AROME-forced sibling, where the 06Z cycle supplies only three analysis fields — so MARP is the more predictable of the two to script against.

> **The documented step counts are wrong; the ranges are right except at 18Z.** `description-paquets-modele-ww3.pdf` gives 78 steps for 00Z, 06Z and 12Z, implying hourly output all the way to +72h (6 analyses + 72 hourly), and 66 steps for 18Z at a 60 h range. Live output is hourly only to +48h and 3-hourly thereafter — **62 steps at every cycle** — and **18Z reaches 72 h like the others**. This is the same failure pattern as [WW3-WARP](./ww3-warp-france.md), and differs from [WW3-MARO](./ww3-maro-france.md), where the documented range itself is wrong.

> **The +48h hourly-to-3-hourly transition is a family-wide convention** shared with [WW3-WARP](./ww3-warp-france.md) (+72h), [WW3-MARO](./ww3-maro-france.md) (+51h), [MFWAM FRANGP0025](./mfwam-hr-france.md) (+51h) and [MFWAM GLOB01](../../global/france/mfwam-global-france.md) (+102h). None of the five documents it.

### File structure
One NetCDF file per cycle containing all lead times:

```
vague-surcote-WW3-MARP__001__SP1__000H999H__<YYYY-MM-DD>T<HH>:00:00Z.nc
```

- **Format:** NetCDF-4 (HDF5 backing), written via NCO 5.1.1
- **File size:** **193.8 MiB at every cycle** — uniform, since all four carry 62 steps
- **Volume (verified):** ~775 MiB/day. The documentation's 260 Mo/day for this configuration is roughly a third of the live figure.
- **Packing:** `int16` with `scale_factor` / `add_offset`, `_FillValue = -32767`. For `hs`, `scale_factor = 0.002`.
- **Dimensions:** `node` (89695), `element` (166663), `noel` (3), `time` (unlimited)

> **Time coordinate carries float rounding artifacts**, as in the sibling packages: `time` is a `float64` Julian day count in days since 1990-01-01, and several steps decode a fraction of a second off the hour. Round to the nearest hour rather than truncating.

---

## Forcing and nesting
- **Wind forcing:** [ARPEGE](../../../nwp_models/global/france/arpege.md) (~10 km over France). This is the sole documented difference from [WW3-MARO](./ww3-maro-france.md).
- **Current and water-level forcing:** **none.** The documentation records HYCOM2D barotropic current and water-height forcing for the *Atlantic* configuration only, from July 2017. The fully static mask here (see *Node masking*) independently confirms that no water-level forcing is active in this configuration.
- **Boundary conditions:** supplied by **MFWAM**, which also shares the wave physics. See [MFWAM GLOB01](../../global/france/mfwam-global-france.md).
- **Bathymetry:** SHOM, developed under HOMONIM.

---

## Data assimilation
- **Assimilates wave observations:** **No.** No assimilation is described for the coastal WW3 configurations; the wave state enters through MFWAM boundary conditions.
- **Validation:** the documentation reports hindcasts of 11 storm events plus a one-year simulation against coastal buoys and satellite altimetry, with satisfactory behaviour and a tendency to **underestimate significant wave height at some peaks**. The validation is described for the system as a whole, not per configuration.

---

## What it provides
**18 data variables**, all on `(time, node)` — an identical set to both siblings:

| Variable | Description | Units |
|---|---|---|
| `hs` | Significant height, wind waves and swell (total sea) | m |
| `dir` | Mean wave direction (total sea) | degree |
| `t0m1` | Mean period T0m1 (total sea) | s |
| `ptp` | Peak period (total sea) | s |
| `spr` | Directional spread | degree |
| `hsts` | Significant height, total swell | m |
| `phs0` / `pdir0` / `ptp0` / `pt0m10` | Partition 0 — height, direction, peak period, mean period | m / degree / s / s |
| `phs1` / `pdir1` / `ptp1` / `pt0m11` | Partition 1 — same four | m / degree / s / s |
| `phs2` / `pdir2` / `ptp2` / `pt0m12` | Partition 2 — same four | m / degree / s / s |

Plus `longitude`, `latitude` (per node), `time`, `tri` (connectivity), `MAPSTA`.

**Partition convention:** partition 0 = wind sea, 1 = primary swell, 2 = secondary swell, per the documentation's four wave types. The partition indices carry no metadata stating this.

> **Five variables are published but undocumented**, identically to the sibling packages. Both PDFs describe 13 parameters; the files carry 18. The extras are `hsts`, `spr`, and `pt0m10`/`pt0m11`/`pt0m12` (mean period per partition, where the documentation lists only peak period per partition).

No wave spectra are distributed.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Data formats:** NetCDF-4
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-marp-resolution-0-1deg
- **Portal dataset id:** `65bd19a20a9351d1cbe9a090`

### Access paths
**The portal exposes the latest cycle only** — a single NetCDF resource replaced in place each run, plus the two documentation PDFs.

**The object store carries the rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/WW3-MARP/001/SP1/<FILE>
```

> **Enumerate a completed cycle, not the current one.** These files publish about T+4h43m, roughly 16 minutes behind the MFWAM GRIB2 files. Because the object store has no real directories — a prefix exists only once a key beneath it does — a listing of the in-progress cycle shows `MFWAM` and `HYCOM2D-*` but no `WW3-*` prefix at all.

### Retention — 15 days from cycle time, expiring at cycle granularity
Objects expire **15 days after their nominal cycle time**, not 15 days after they were written. Observed directly on 2026-08-10: at 04:45 UTC the `2026-07-26T06:00:00Z` cycle was present; by 06:12 UTC it had gone while `2026-07-26T12:00:00Z` remained. `2026-07-26T06:00Z` + 15 days = `2026-08-10T06:00Z`, falling between the two observations. The boundary advanced simultaneously for MFWAM, WW3-MARO, WW3-MARP and WW3-WARP — a **bucket-wide lifecycle policy**. The oldest available cycle is therefore a moving target within the day.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Published | Latency |
|---|---|---|
| 00Z | 04:43 | T+4h43m |
| 06Z | 11:34 | T+5h34m |
| 12Z | 16:25 | T+4h25m |
| 18Z | 23:34 | T+5h34m |

MARP is the **first** of the three WW3 packages to publish at each cycle. Verified order at 2026-08-09 00Z: MFWAM GLOB01 04:27 → MFWAM FRANGP0025 04:29 → **WW3-MARP 04:43** → WW3-MARO 04:49 → WW3-WARP 04:58.

---

## Related systems

### The three WW3 coastal packages

| Package | Domain | Wind forcing | Mesh | Range | HYCOM2D forcing | Mask |
|---|---|---|---|---|---|---|
| [WW3-MARO](./ww3-maro-france.md) | Mediterranean | AROME | 89,695 nodes | 51 h | No | Static |
| **WW3-MARP** (this entry) | Mediterranean | ARPEGE | 89,695 nodes | 72 h | No | Static |
| [WW3-WARP](./ww3-warp-france.md) | Atlantic | ARPEGE | 92,757 nodes | 72 h | Yes | **Tidal** |

> **There is no WW3-WARO.** The four-way `MARO`/`MARP`/`WARO`/`WARP` naming applies to the **HYCOM2D storm surge** packages in the same tree, not to WW3.

### Other French wave products
- [MFWAM FRANGP0025](./mfwam-hr-france.md) — 0.025° regular grid covering the same waters plus the Atlantic seaboard, AROME-forced, GRIB2. Simpler to consume; no coastal physics.
- [MFWAM GLOB01](../../global/france/mfwam-global-france.md) — global 0.1°, this model's boundary-condition source.
- [MEDWAM](../greece/medwam.md) — basin-wide Mediterranean, data-assimilating, but without coastal physics or nearshore mesh refinement.

---

## Notes
- **The MARO/MARP pair is the cleanest forcing comparison in the French open catalogue.** Same mesh (byte-identical), same bathymetry, same physics, same output variables, same cycles — differing only in whether the driving winds come from a convection-permitting model. Useful for quantifying the value of AROME winds in coastal wave prediction. Restrict comparisons to leads ≤ 51 h, where both products have data.

- **MARP is the better default of the two for operational use** unless AROME winds are specifically wanted: it runs 21 h further out, has a uniform 62-step structure at every cycle, and publishes first.

- **Documentation could not be retrieved during verification.** Both PDF URLs on this dataset returned a persistent upstream connection error across roughly eight attempts over fifteen minutes, direct and via the portal redirect, while the equivalents on the MARO and WARP datasets fetched normally. The portal reports file sizes of 468.0 Ko and 48.1 Ko, matching the MARO/WARP copies exactly (479,218 and 49,260 bytes), and filenames and 2024-02-28 upload dates also match — so these are almost certainly the same shared documents, whose content is summarised in the [WW3-MARO entry](./ww3-maro-france.md#official-documentation). **Not hash-confirmed**, unlike the MARO/WARP pair which were verified byte-identical to each other.

- **Portal metadata is stale**: `temporal_coverage` reads 2024-03-05 to 2024-03-06, from shortly after the dataset's February 2024 creation, and does not track the rolling window.

- **Storm surge companions.** The `HYCOM2D-MARO`, `-MARP`, `-WARO` and `-WARP` packages in the same tree are storm surge systems at 0.04°, belonging under `storm_surge_models/` and not yet catalogued.

---

## Recent version history

No formal version history is published. The following is from the model documentation.

### 18 March 2015 — operational
The SHOM/Météo-France coastal WW3 system entered operations under the HOMONIM project.

The July 2017 addition of HYCOM2D current and water-level forcing applied to the Atlantic configuration only and did not affect this one. No configuration-specific changes are documented for the Mediterranean ARPEGE run.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-marp-resolution-0-1deg
- Model descriptif (FR, shared across all three WW3 datasets): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-marp-resolution-0-1deg/20240228-172813/descriptif-modele-ww3.pdf
- Package descriptif (FR, shared): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-marp-resolution-0-1deg/20240228-162429/description-paquets-modele-ww3.pdf
- Working copies of the same two documents are reachable from the [WW3-MARO](./ww3-maro-france.md#official-documentation) and [WW3-WARP](./ww3-warp-france.md#official-documentation) datasets if these URLs fail.
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

### Cited literature
- Tolman, H. L. (2002) — WAVEWATCH III model description
- Roland, A. et al. (2014) — unstructured-mesh wave modelling
- Ardhuin, F. et al. (2010) — wave dissipation parametrisation shared with MFWAM
- Janssen, P. et al. (2014) — wave physics shared with MFWAM

---

*Live-verified 2026-08-10 against the 2026-08-10T00:00:00Z and 2026-08-09T06/12/18Z cycles (NetCDF decoded with netCDF4-python), with mesh identity confirmed by direct array comparison against WW3-MARO, and cycle enumeration and publication timestamps sampled 2026-07-26 to 2026-08-10.*
