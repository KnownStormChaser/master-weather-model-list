# WW3-WARP (Météo-France Coastal Wave Model — Atlantic, ARPEGE-forced)

## What this model is
WW3-WARP is the ARPEGE-forced Atlantic configuration of Météo-France's **coastal** wave forecasting system, built on **WAVEWATCH III** (Tolman, 2002) over an **unstructured triangular mesh** (Roland et al., 2014).

It is the Atlantic sibling of [WW3-MARO](./ww3-maro-france.md) and shares its lineage: developed by **SHOM** and Météo-France under the **HOMONIM** project (*Historique, Observation, MOdélisation des NIveaux Marins*), commissioned by the Direction Générale pour la Prévention des Risques under the interministerial *Submersions Rapides* plan, operational since **18 March 2015**. It uses MFWAM's wave physics with added coastal processes — unified breaking from offshore to shore, coastal reflection, refraction by currents and bathymetry, and bottom friction — on SHOM's HOMONIM bathymetry.

**One thing makes this configuration distinct from both Mediterranean packages:** since July 2017 it ingests **barotropic current and water height from HYCOM2D**, giving it a tidally varying water level. The effect is directly observable in the published fields, where thousands of intertidal nodes wet and dry on a semidiurnal cycle (see *Node masking*). No Mediterranean configuration has this.

`WARP` decomposes as **W**est/Atlantic + **ARP**EGE. There is no AROME-forced Atlantic configuration — see [WW3-MARO](./ww3-maro-france.md#related-systems).

> **Output geometry — this entry is an unstructured mesh, not a grid.** Fields are on 92,757 nodes with a 175,634-triangle connectivity table. In scope under the wave template's provision for unstructured meshes.

---

## Who runs it
- **Organization:** Météo-France, with SHOM (Service Hydrographique et Océanographique de la Marine) as co-developer
- **Country / region:** France
- **Programme:** HOMONIM, under DGPR contracting authority
- **Internal product reference:** BDPE 12388 (the shared package documentation lists 12388 and 12389 across the two ARPEGE configurations; the mapping to Atlantic vs Mediterranean is not stated unambiguously — **TBD**)

---

## What area it covers
- **Coverage:** French Atlantic seaboard — Bay of Biscay, western approaches, the English Channel, and into the southern North Sea
- **Mesh extent (verified):** 7.000°W – 4.716°E, 43.295°N – 52.905°N
- **Reach offshore:** the documentation describes both coastal configurations as extending 200–300 km from metropolitan French coasts

The northern edge at 52.9°N reaches the Dutch coast; the southern edge at 43.3°N covers the Spanish Cantabrian shore. This is a substantially larger domain than the Mediterranean mesh both in extent and node count.

### Mesh geometry (verified)
- **Nodes:** 92,757
- **Elements:** 175,634 triangles (`tri` variable, 1-based node indices)
- **Edge length:** median **679 m**; 5th percentile 337 m; 95th percentile 5.20 km; minimum 137 m; maximum 12.4 km

Compared with [WW3-MARO](./ww3-maro-france.md)'s mesh (median 489 m, 5th percentile 204 m), this mesh is **coarser nearshore but more uniform** — its finest edges are roughly seven times longer than MARO's, while its coarsest are shorter. The larger domain is resolved with proportionally less extreme local refinement.

> **Ignore the resolution figure in the product title.** Published as "Résolution 0.1°", path token `001`, and the file's own `history` attribute names it `WW3-WARP_0.01_SP1_…`. None describes an unstructured mesh. Use the edge-length distribution.

### Node masking — time-varying, and MAPSTA does not predict it
The static `MAPSTA` status map carries **four** values here, one more than the Mediterranean mesh:

| MAPSTA | Interpretation | Count |
|---|---|---|
| 0 | Excluded (land) | 7,238 |
| 1 | Active sea point | 82,415 |
| 2 | Active boundary point | 204 |
| **15** | **Intertidal / wetting-drying point** | **2,900** |

The `MAPSTA = 15` class is absent from [WW3-MARO](./ww3-maro-france.md), which has only 0, 1 and 2. Those 2,900 nodes cluster in exactly France's major tidal-flat and estuarine systems — the Bay of Mont-Saint-Michel (~48.65°N, 1.55°W), the Cotentin east coast, the Loire estuary and Guérande, the Arcachon basin, and the Baie de Somme.

**Their valid-node count oscillates with a semidiurnal period.** Counting non-fill `hs` values among those 2,900 nodes by lead hour: peaks of 1663 at +2h, 1754 at +15h and 1749 at +27h; troughs of 79 at +8h, 22 at +20h and 8 at +33h. Peak-to-peak and trough-to-trough spacings of 12–13 h match the M2 tidal constituent (12.42 h). This is the HYCOM2D water-level forcing made visible: the mesh wets and dries with the tide.

> **Consequence — build the validity index per time step, not from MAPSTA.** Across the 2026-08-10 00Z file:
> - **79,806 nodes are valid at every step** (79,602 with MAPSTA 1, plus all 204 boundary points)
> - **84,712 are valid at some step**
> - **4,906 change state**, and critically these are **not confined to the MAPSTA 15 class**: 2,093 are MAPSTA 15, but **2,813 are ordinary MAPSTA 1 sea points** that also dry out
> - Total valid nodes range from **82,032 to 83,535** across the file
>
> `MAPSTA ≥ 1` therefore over-counts by roughly 3,000–4,500 nodes depending on the step. This is the opposite of MARO, where `MAPSTA ≥ 1` matches the valid count exactly and never changes. Code written against the Mediterranean packages will silently mishandle this one.

---

## Basic details
- **Model type:** Deterministic coastal wave model
- **Grid system:** **unstructured triangular mesh**
- **Core wave model:** WAVEWATCH III (NOAA), SHOM/Météo-France unstructured implementation
- **Wave physics:** shared with MFWAM (Ardhuin et al., 2010; Janssen et al., 2014), plus coastal shallow-water processes and tidal wetting/drying
- **Nominal resolution:** ~340 m at the fine end, ~680 m median edge length
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

The dataset description's "analyses sur les 6 dernières heures" holds at every cycle here — unlike [WW3-MARO](./ww3-maro-france.md), where the 06Z cycle supplies only three.

> **The documented step counts are wrong; the ranges are mostly right.** `description-paquets-modele-ww3.pdf` gives 78 steps for the 00Z, 06Z and 12Z cycles and 66 for 18Z, implying **hourly output all the way to +72h** (6 analyses + 72 hourly). Live output is hourly only to +48h and 3-hourly thereafter, giving **62 steps**. The document's 72 h range is correct for 00Z/06Z/12Z; its claim that **18Z stops at 60 h is wrong** — 18Z reaches 72 h like the others.
>
> This differs from [WW3-MARO](./ww3-maro-france.md), where the documented *range* was also wrong. Here only the sampling and the 18Z figure are.

> **The +48h hourly-to-3-hourly transition is a family-wide convention.** [MFWAM FRANGP0025](./mfwam-hr-france.md) stops at +51h, [WW3-MARO](./ww3-maro-france.md) at +51h, this model continues to +72h, and [MFWAM GLOB01](../../global/france/mfwam-global-france.md) to +102h — all with the same hourly-to-+48h-then-3-hourly schedule, and none of them documenting it.

### File structure
One NetCDF file per cycle containing all lead times:

```
vague-surcote-WW3-WARP__001__SP1__000H999H__<YYYY-MM-DD>T<HH>:00:00Z.nc
```

- **Format:** NetCDF-4 (HDF5 backing), written via NCO 5.1.1
- **File size:** **200.5 MiB at every cycle** — uniform, since all four carry 62 steps
- **Volume (verified):** ~802 MiB/day. The documentation's 350 Mo/day for the Atlantic configuration is well under half the live figure.
- **Packing:** `int16` with `scale_factor` / `add_offset`, `_FillValue = -32767`. For `hs`, `scale_factor = 0.002` and `valid_max = 32000`, a 64 m ceiling.
- **Dimensions:** `node` (92757), `element` (175634), `noel` (3), `time` (unlimited)

> **Time coordinate carries float rounding artifacts**, as in the Mediterranean packages: `time` is a `float64` Julian day count in days since 1990-01-01, and several steps decode a fraction of a second off the hour. Round to the nearest hour rather than truncating.

---

## Forcing and nesting
- **Wind forcing:** [ARPEGE](../../../nwp_models/global/france/arpege.md) (~10 km over France)
- **Current and water-level forcing:** **HYCOM2D forced by ARPEGE**, supplying barotropic ocean current and water height, since **July 2017**. The documentation states this improves sea-state simulation over intertidal zones and in strong-current areas — and unlike the Mediterranean case, it is independently confirmed here by the tidal wet/dry cycling described under *Node masking*. The corresponding surge packages (`HYCOM2D-WARO`, `HYCOM2D-WARP`) are not yet catalogued.
- **Boundary conditions:** supplied by **MFWAM**, which also shares the wave physics. See [MFWAM GLOB01](../../global/france/mfwam-global-france.md).
- **Bathymetry:** SHOM, developed under HOMONIM.

---

## Data assimilation
- **Assimilates wave observations:** **No.** No assimilation is described for the coastal WW3 configurations; the wave state enters through MFWAM boundary conditions.
- **Validation:** the documentation reports hindcasts of 11 storm events plus a one-year simulation against coastal buoys and satellite altimetry, with satisfactory behaviour and a tendency to **underestimate significant wave height at some peaks**.

---

## What it provides
**18 data variables**, all on `(time, node)` — an identical set to [WW3-MARO](./ww3-maro-france.md):

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

> **Five variables are published but undocumented**, identically to the Mediterranean packages. Both PDFs describe 13 parameters; the files carry 18. The extras are `hsts`, `spr`, and `pt0m10`/`pt0m11`/`pt0m12` (mean period per partition, where the documentation lists only peak period per partition).

No wave spectra and no water-level or current fields are distributed, despite water level being an active forcing input.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence 2.0** (Etalab) — attribution required, no share-alike. Declared as `lov2` in the portal API.
- **High Value Dataset:** Yes — carries the `hvd` badge under the EU Open Data Directive.
- **Is the data downloadable?** Yes, no registration or API key
- **Data formats:** NetCDF-4
- **Official landing page:**
  https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-warp-resolution-0-1deg
- **Portal dataset id:** `65bd197cd4222b0c96db759e`

### Access paths
**The portal exposes the latest cycle only** — a single NetCDF resource replaced in place each run, plus the two documentation PDFs.

**The object store carries the rolling archive** across all four cycles:

```
https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/<CYCLE>/vague-surcote/WW3-WARP/001/SP1/<FILE>
```

> **Enumerate a completed cycle, not the current one.** These files publish about T+4h57m, roughly 30 minutes behind the MFWAM GRIB2 files. Because the object store has no real directories — a prefix exists only once a key beneath it does — a listing of the in-progress cycle shows `MFWAM` and `HYCOM2D-*` but no `WW3-*` prefix at all.

### Retention — 15 days from cycle time, expiring at cycle granularity
Objects appear to expire exactly **15 days after their nominal cycle time**, not 15 days after they were written. Observed directly during verification on 2026-08-10:

- At 04:45 UTC, the `2026-07-26T06:00:00Z` cycle was present and `2026-07-26T00:00:00Z` was gone
- At 06:12 UTC, `2026-07-26T06:00:00Z` had also gone, while `2026-07-26T12:00:00Z` remained

`2026-07-26T06:00Z` + 15 days = `2026-08-10T06:00Z`, which falls between the two observations. The boundary advanced by exactly one cycle, and did so simultaneously for MFWAM, WW3-MARO, WW3-MARP and WW3-WARP — a **bucket-wide lifecycle policy**, not a per-product setting. Note that this makes the oldest available cycle a moving target within the day: a harvester that lists at 05:00 and fetches at 07:00 can find its oldest listed cycle already deleted.

### Publication latency (verified)
Measured from `LastModified` on 2026-08-09:

| Cycle | Published | Latency |
|---|---|---|
| 00Z | 04:58 | T+4h58m |
| 06Z | 11:49 | T+5h49m |
| 12Z | 16:40 | T+4h40m |
| 18Z | 23:49 | T+5h49m |

Consistently the **last** of the five `vague-surcote` wave products to publish at each cycle — about 9 minutes behind WW3-MARO and 30 behind MFWAM GLOB01.

---

## Related systems

### The three WW3 coastal packages

| Package | Domain | Wind forcing | Mesh | Range | HYCOM2D forcing |
|---|---|---|---|---|---|
| [WW3-MARO](./ww3-maro-france.md) | Mediterranean | AROME | 89,695 nodes | 51 h | No |
| [WW3-MARP](./ww3-marp-france.md) | Mediterranean | ARPEGE | 89,695 nodes | TBD | No |
| **WW3-WARP** (this entry) | Atlantic | ARPEGE | 92,757 nodes | 72 h | **Yes** |

WARP is the only one of the three with tidal water-level forcing and the only one on the Atlantic mesh, so it has no direct forcing-comparison partner the way MARO and MARP do.

> **There is no WW3-WARO.** The four-way `MARO`/`MARP`/`WARO`/`WARP` naming applies to the **HYCOM2D storm surge** packages in the same tree, not to WW3.

### Other French wave products
- [MFWAM FRANGP0025](./mfwam-hr-france.md) — 0.025° regular grid covering both this domain and the Mediterranean, AROME-forced, GRIB2. Simpler to consume; no coastal physics, no tides.
- [MFWAM GLOB01](../../global/france/mfwam-global-france.md) — global 0.1°, this model's boundary-condition source.
- [IBIWAM](../spain/ibiwam.md) — Copernicus Marine, 1/36°, covers the same Atlantic waters with altimeter and spectral assimilation and a 10-day range, but on a regular grid without wetting and drying.

---

## Notes
- **This is the only open French wave product that resolves intertidal wetting and drying.** For work in estuaries, tidal flats or strong-current channels — the Mont-Saint-Michel bay, the Gironde and Loire approaches, the Alderney Race — no other freely available product represents the moving waterline. That capability is also why the validity mask cannot be precomputed.

- **Documentation is shared verbatim across all three WW3 datasets.** `descriptif-modele-ww3.pdf` and `description-paquets-modele-ww3.pdf` attached here are **byte-identical** (MD5 `98cf98df…` and `1f379289…`) to the copies on the MARO and MARP datasets, despite separate upload timestamps. Read them once. They describe all three configurations in a single three-column table, which extracts poorly — the per-configuration figures are easy to misattribute.

- **Portal metadata is stale**: `temporal_coverage` reads 2024-03-05 to 2024-03-06, from shortly after the dataset's February 2024 creation, and does not track the rolling window.

- **Water level is an input but not an output.** The model consumes HYCOM2D water height and current, and its results visibly depend on them, but neither field is republished here. Users needing the water level itself must go to the HYCOM2D surge packages, not yet catalogued.

---

## Recent version history

No formal version history is published. The following is from the model documentation.

### July 2017 — HYCOM2D current and water-level forcing added
The Atlantic configuration began ingesting barotropic current and water height from HYCOM2D forced by ARPEGE, improving sea-state simulation over intertidal zones and in strong-current areas. This is the change that distinguishes WARP from the Mediterranean configurations, and its effect is measurable in the published `MAPSTA = 15` node behaviour.

### 18 March 2015 — operational
The SHOM/Météo-France coastal WW3 system entered operations under the HOMONIM project.

---

## Official documentation
- Dataset landing page: https://www.data.gouv.fr/datasets/paquets-de-modele-de-vagues-haute-resolution-ww3-warp-resolution-0-1deg
- Model descriptif (FR, shared across all three WW3 datasets; includes mesh maps): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-warp-resolution-0-1deg/20240228-172845/descriptif-modele-ww3.pdf
- Package descriptif (FR, shared; per-configuration parameter and cadence tables): https://static.data.gouv.fr/resources/paquets-de-modele-de-vagues-haute-resolution-ww3-warp-resolution-0-1deg/20240228-164215/description-paquets-modele-ww3.pdf
- SHOM: https://www.shom.fr/
- Météo-France: https://meteofrance.com/
- Etalab Open Licence 2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

### Cited literature
- Tolman, H. L. (2002) — WAVEWATCH III model description
- Roland, A. et al. (2014) — unstructured-mesh wave modelling
- Ardhuin, F. et al. (2010) — wave dissipation parametrisation shared with MFWAM
- Janssen, P. et al. (2014) — wave physics shared with MFWAM

---

*Live-verified 2026-08-10 against the 2026-08-10T00:00:00Z and 2026-08-09T06/12/18Z cycles (NetCDF decoded with netCDF4-python), with cycle enumeration, mesh statistics, tidal mask analysis and publication timestamps sampled 2026-07-26 to 2026-08-10.*
