# WW3-MARO (Météo-France Coastal Wave Model — Mediterranean, AROME-forced)

## What this model is
WW3-MARO is the AROME-forced Mediterranean configuration of Météo-France's **coastal** wave forecasting system, built on **WAVEWATCH III** (Tolman, 2002) over an **unstructured triangular mesh** (Roland et al., 2014).

The system was developed jointly by **SHOM** and Météo-France under the **HOMONIM** project (*Historique, Observation, MOdélisation des NIveaux Marins*), commissioned by the Direction Générale pour la Prévention des Risques in response to the interministerial *Submersions Rapides* plan. It has been operational since **18 March 2015**.

Its purpose is different from the offshore MFWAM products. It uses the same wave physics as MFWAM — which also supplies its lateral boundary conditions — but adds the coastal processes that matter in shallow water on a mesh refined to a few hundred metres near the coastline:

- unified wave-breaking parametrisation from offshore to the shore
- coastal reflection
- refraction by currents and bathymetry
- bottom friction

Bathymetry is from SHOM, developed under HOMONIM.

`MARO` decomposes as **M**editerranean + **AR**OME. The naming convention across this family is domain (`M` = Mediterranean, `W` = West/Atlantic) plus forcing (`ARO` = AROME, `ARP` = ARPEGE).

> **Output geometry — this entry is an unstructured mesh, not a grid.** Fields are defined on 89,695 nodes with an explicit 166,663-triangle connectivity table, not on a latitude–longitude array. It is in scope under the wave template's explicit provision for unstructured meshes, alongside the repository's existing non-gridded precedent (STOFS-3D-Atlantic station series). Consumers expecting a 2-D array will need to triangulate or interpolate.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as co-developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **Internal product reference:** BDPE 13503

---

## What area it covers
- **Coverage:** Northwestern Mediterranean — the French Mediterranean seaboard including the Gulf of Lion, the Ligurian Sea, and the waters around Corsica
- **Mesh extent (verified):** 3.034°E – 11.808°E, 40.742°N – 44.427°N
- **Reach offshore:** the documentation describes both coastal configurations as extending 200–300 km from metropolitan French coasts, Corsica included

Despite the dataset being titled for the "Méditerranée" domain, the mesh is confined to the northwestern basin. It does not cover the Adriatic, Aegean, Ionian, or eastern Mediterranean. For basin-wide Mediterranean waves see [MEDWAM](../greece/medwam.md).

### Mesh geometry (verified)
- **Nodes:** 89,695
- **Elements:** 166,663 triangles (`tri` variable, 1-based node indices)
- **Edge length:** median **489 m**; 5th percentile 204 m; 95th percentile 1.99 km; minimum 19 m; maximum 18.7 km

Resolution therefore varies by nearly three orders of magnitude, refined nearshore and coarse offshore. The documentation's "environ 200 m" figure describes the fine end of the distribution, matching the observed 5th percentile rather than the typical spacing.

> **Ignore the resolution figure in the product title.** The dataset is published as "Résolution 0.1°", the object-store path token is `001`, and the file's own `history` attribute names it `WW3-MARO_0.01_SP1_…` — three different values, none of which describes an unstructured mesh. No single resolution figure is meaningful here; use the edge-length distribution above.

### Node masking
The `MAPSTA` status map classifies every node, and is static:

| MAPSTA | Meaning | Count |
|---|---|---|
| 0 | Excluded (land / dry) | 11,433 |
| 1 | Active sea point | 78,190 |
| 2 | Active boundary point | 72 |

Data variables are filled at `MAPSTA = 0` nodes and valid elsewhere: `hs` at analysis time reports exactly **78,262 valid nodes**, which is precisely the 78,190 sea points plus the 72 boundary points. `MAPSTA ≥ 1` is therefore a reliable validity index, and unlike [MFWAM GLOB01](../../global/france/mfwam-global-france.md) it does not change with lead time.

---

## Basic details
- **Model type:** Deterministic coastal wave model
- **Grid system:** **unstructured triangular mesh**
- **Core wave model:** WAVEWATCH III (NOAA), with the SHOM/Météo-France unstructured-mesh implementation
- **Wave physics:** shared with MFWAM (Ardhuin et al., 2010; Janssen et al., 2014), plus coastal shallow-water processes
- **Nominal resolution:** ~200 m at the finest, ~500 m median edge length (see above)
- **Forecast length (verified):** **51 h at every cycle**
- **Update frequency / cycles (verified):** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** hourly, plus a final step at +51h
- **Analysis fields:** **yes** — each file includes hourly analyses preceding the run time (see below)

### Time axis structure
Unlike the MFWAM packages, these files **do include analysis steps**, and the number varies by cycle:

| Cycle | Analysis steps | Forecast steps | Total | Lead range |
|---|---|---|---|---|
| 00Z | 5 (−5h to −1h) | 49 (+1h…+48h hourly, +51h) | **55** | −5h → +51h |
| 06Z | 2 (−2h to −1h) | 49 | **52** | −2h → +51h |
| 12Z | 5 | 49 | **55** | −5h → +51h |
| 18Z | 5 (verified) | 49 | **55** | −5h → +51h |

All cycles include the analysis at lead 0. The dataset description's claim that "les analyses sur les 6 dernières heures sont aussi fournies" holds for 00Z, 12Z and 18Z (six hourly fields from −5h to 0h) but not for 06Z, which supplies three.

> **Every published cadence figure for this configuration is wrong.** `descriptif-modele-ww3.pdf` states the AROME Mediterranean configuration runs **5× daily at 00, 03, 06, 12 and 18 UTC** to 42 h, 39 h, 36 h, 42 h and 36 h respectively. `description-paquets-modele-ww3.pdf` gives step counts of 48, 42, 39, 45 and 42 for those cycles. Live enumeration found **4 cycles** (no 03Z run exists in the object store at any sampled date), a uniform **51 h** range, and step counts of 55/52/55/55. The node count is the one figure both documents get right.

> **The +51h step matches the MFWAM family.** [MFWAM FRANGP0025](./mfwam-hr-france.md) also terminates at exactly +51h after hourly output to +48h, and [GLOB01](../../global/france/mfwam-global-france.md) continues 3-hourly from +51h to +102h. The three products share a step schedule that none of them documents.

### File structure
One NetCDF file per cycle containing all lead times:

```
vague-surcote-WW3-MARO__001__SP1__000H999H__<YYYY-MM-DD>T<HH>:00:00Z.nc
```

`000H999H` denotes the aggregated all-steps file; there is no per-step file as in the MFWAM packages.

- **Format:** NetCDF-4 (HDF5 backing), written via NCO 5.1.1
- **File size:** 172.3 MiB (00Z, 12Z, 18Z); 163.1 MiB (06Z, three fewer steps)
- **Volume (verified):** ~680 MiB/day. The documentation's 220 Mo/day for this configuration is roughly a third of the live figure — consistent with the extra steps and the five undocumented variables.
- **Packing:** all data variables are `int16` with `scale_factor` and `add_offset`, `_FillValue = -32767`. For `hs`, `scale_factor = 0.002`, giving a 64 m ceiling at `valid_max = 32000`.
- **Dimensions:** `node` (89695), `element` (166663), `noel` (3), `time` (unlimited)

> **Time coordinate carries float rounding artifacts.** `time` is a `float64` Julian day count in days since 1990-01-01. Several steps decode a fraction of a second off the hour — the −5h step of the 2026-08-10 00Z file resolves to 18:59:59.999987 rather than 19:00:00. Round to the nearest hour rather than truncating, or steps will land in the wrong hour.

---

## Forcing and nesting
- **Wind forcing:** [AROME](../../../nwp_models/regional/france/arome.md) (~1.3 km over France). This is the distinguishing feature against the sibling [WW3-MARP](./ww3-marp-france.md), which runs the same mesh under ARPEGE.
- **Boundary conditions:** supplied by **MFWAM**, which also shares the wave physics. See [MFWAM GLOB01](../../global/france/mfwam-global-france.md).
- **Current and water-level forcing:** **none.** The documentation records HYCOM2D barotropic current and water-height forcing for the *Atlantic* configuration only, from July 2017. This is now confirmed from the data rather than inferred: the validity mask here is **fully static** — 78,262 valid nodes at every one of the 55 steps, zero nodes changing state — whereas [WW3-WARP](./ww3-warp-france.md) on the Atlantic mesh shows thousands of intertidal nodes wetting and drying on a 12.4 h semidiurnal cycle. No water-level forcing is active in this configuration.
- **Bathymetry:** SHOM, developed under HOMONIM.

---

## Data assimilation
- **Assimilates wave observations:** **No.** No assimilation is described for the coastal WW3 configurations. The wave state enters through MFWAM boundary conditions; MFWAM itself assimilates satellite altimetric and spectral data in its global configuration.
- **Validation:** the documentation reports hindcasts of 11 storm events plus a one-year simulation, evaluated against coastal buoys and satellite altimetry, with satisfactory behaviour and a noted tendency to **underestimate significant wave height at some peaks**.

---

## What it provides
**18 data variables**, all on `(time, node)`:

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

Plus coordinates and mesh: `longitude`, `latitude` (per node), `time`, `tri` (connectivity), `MAPSTA` (status map).

**Partition convention:** the documentation describes four wave types — total sea, wind sea, primary swell and secondary swell — which maps onto partition 0 = wind sea, 1 = primary swell, 2 = secondary swell. The partition indices themselves carry no metadata stating this, so the mapping rests on the documentation.

> **Five variables are published but undocumented.** Both PDFs describe **13 parameters**: significant height, mean direction and peak period for each of the four wave types, plus the mean period of the total sea. The files carry **18**. The undocumented additions are `hsts` (total swell height), `spr` (directional spread), and `pt0m10`/`pt0m11`/`pt0m12` (mean period per partition — the documentation lists only *peak* period per partition). These are populated, physically plausible fields, not placeholders.

No wave spectra and no surface currents are distributed.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Data formats:** NetCDF-4
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-maro-resolution-0-1deg

### Access paths
**The portal exposes the latest cycle only** — a single NetCDF resource, replaced in place each run, plus the two documentation PDFs.

**The object store carries a 15-day rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/WW3-MARO/001/SP1/<FILE>
```

with `<CYCLE>` as `YYYY-MM-DDTHH:00:00Z`. Retention boundary verified on 2026-08-10: `2026-07-26T00:00:00Z` absent, `2026-07-26T06:00:00Z` present — the same boundary as both MFWAM packages, confirming a bucket-wide expiry policy.

> **The WW3 packages publish later than the MFWAM GRIB2 files and appear in the bucket listing only after they land.** A directory listing of the current cycle taken before roughly T+4h45m will show the `MFWAM` and `HYCOM2D-*` prefixes but no `WW3-*` prefix at all, because the object store has no real directories — prefixes exist only once a key beneath them does. Enumerate a completed cycle, not the current one.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Published | Latency |
|---|---|---|
| 00Z | 04:48 | T+4h48m |
| 06Z | 11:36 | T+5h36m |
| 12Z | 16:31 | T+4h31m |
| 18Z | 23:39 | T+5h39m |

Consistently about 20 minutes behind the MFWAM GLOB01 files at the same cycle.

---

## Related systems

### The three WW3 coastal packages
Météo-France distributes **three** configurations of this system openly:

| Package | Domain | Wind forcing | Mesh | Range | Mask |
|---|---|---|---|---|---|
| **WW3-MARO** (this entry) | Mediterranean | AROME | 89,695 nodes | 51 h | Static |
| [WW3-MARP](./ww3-marp-france.md) | Mediterranean | ARPEGE | 89,695 nodes | **72 h** | Static |
| [WW3-WARP](./ww3-warp-france.md) | Atlantic | ARPEGE | 92,757 nodes | 72 h | **Tidal** |

MARO and MARP run on a **byte-identical mesh** — `longitude`, `latitude`, `tri` and `MAPSTA` match exactly — and differ only in atmospheric forcing, making them a clean controlled comparison of convection-permitting versus global winds on coastal sea state. Restrict such comparisons to leads ≤ 51 h, since MARP runs 21 h further out. Mesh preprocessing computed for one is reusable verbatim for the other.

MARO is also the **only one of the three with a non-uniform time axis**: its 06Z cycle carries 3 analysis fields instead of 6, and 52 steps instead of 55. Both siblings are uniform at 62 steps across all four cycles.

> **There is no WW3-WARO.** The Atlantic domain has no AROME-forced configuration. The four-way `MARO`/`MARP`/`WARO`/`WARP` naming does apply to the **HYCOM2D storm surge** packages in the same portal tag and object-store tree, which makes the asymmetry easy to miss.

### Other French wave products
- [MFWAM FRANGP0025](./mfwam-hr-france.md) — 0.025° regular grid over the same waters plus the Atlantic seaboard, AROME-forced, GRIB2. The gridded counterpart to this entry; coarser nearshore but far simpler to consume.
- [MFWAM GLOB01](../../global/france/mfwam-global-france.md) — global 0.1°, this model's boundary-condition source.
- [MEDWAM](../greece/medwam.md) — basin-wide Mediterranean, higher resolution offshore and data-assimilating, but without the coastal physics or the nearshore mesh refinement.

---

## Notes
- **This is the only French open wave product with coastal shallow-water physics.** Breaking, reflection, bottom friction and current refraction are absent from the MFWAM products, which are offshore models. For nearshore work inside the surf zone approach, this is the appropriate product despite the harder file format.

- **Analysis fields make this the only French wave package usable as a short hindcast.** Six hourly analyses per cycle at 00Z/12Z/18Z, with successive cycles overlapping, mean a continuous hourly analysed sea state can be assembled from the rolling archive. Neither MFWAM package publishes any analysis step.

- **Portal metadata is stale** in the same way as the MFWAM datasets — `temporal_coverage` reflects the February 2024 dataset creation, not the rolling window.

- **Storm surge companions.** The `HYCOM2D-MARO`, `-MARP`, `-WARO` and `-WARP` packages in the same tree are storm surge systems at 0.04°, belonging under `storm_surge_models/` and not yet catalogued. The Atlantic WW3 configuration ingests HYCOM2D output as forcing, so the two families are operationally coupled on that side.

---

## Recent version history

No formal version history is published. The following is from the model documentation.

### July 2017 — HYCOM2D forcing added to the Atlantic configuration
The Atlantic configuration began ingesting barotropic current and water height from HYCOM2D forced by ARPEGE, improving sea-state simulation over intertidal zones and in strong-current areas. No corresponding change is documented for the Mediterranean configurations.

### 18 March 2015 — operational
The SHOM/Météo-France coastal WW3 system entered operations, developed under the HOMONIM project.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-maro-resolution-0-1deg
- Model descriptif (FR, includes mesh maps for both configurations): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-maro-resolution-0-1deg/20240228-172916/descriptif-modele-ww3.pdf
- Package descriptif (FR, per-configuration parameter and cadence tables): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-maro-resolution-0-1deg/20240228-160808/description-paquets-modele-ww3.pdf
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

### Cited literature
- Tolman, H. L. (2002) — WAVEWATCH III model description
- Roland, A. et al. (2014) — unstructured-mesh wave modelling
- Ardhuin, F. et al. (2010) — wave dissipation parametrisation shared with MFWAM
- Janssen, P. et al. (2014) — wave physics shared with MFWAM

---

*Live-verified 2026-08-10 against the 2026-08-10T00:00:00Z and 2026-08-09T00/06/18Z cycles (NetCDF decoded with netCDF4-python), with cycle enumeration, mesh statistics and publication timestamps sampled 2026-07-26 to 2026-08-10.*
