# <MODEL NAME>

## What this model is
<Plain-language description of the ensemble and what uncertainty/probabilities it provides.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Global / Region name>
- **Domain details (optional):** <bounds / grid name / notable notes>

---

## Basic details
- **Model type:** Ensemble NWP
- **Model system / core:** <e.g., IFS/EPS, GEFS, ICON-EPS, HARMONIE-AROME ensemble>
- **Dynamical formulation:** <Hydrostatic / Non-hydrostatic> (TBD)
- **Convection-allowing:** <Yes / No> (typically Yes when horizontal resolution is ≤ ~4 km; TBD)
- **Ensemble size:** <e.g., 20 perturbed + 1 control> (TBD)
- **Horizontal resolution:** <native mesh and/or published grid>
- **Grid dimensions (optional):** <e.g., 1024 × 768 grid points> (may instead be noted under "What area it covers"; TBD)
- **Vertical levels:** <TBD>
- **Model top (optional):** <e.g., ~75 km> (TBD)
- **Forecast length:** <e.g., 16 days> (mention extended products if applicable)
- **Update frequency / cycles:** <e.g., 2× daily (00/12 UTC)>
- **Temporal output resolution:** <e.g., 3h / 6h> (TBD)

---

## Data assimilation (optional)
- **Data assimilation:** <Yes/No> (TBD)
- **Method / cadence (optional):** <e.g., EnKF, EDA, hybrid 4D-Var> (TBD)

---

## Initial and boundary conditions (for limited-area ensembles)
- **Initial conditions:** <e.g., IFS-ENS / GEFS / ICON-EPS> (TBD)
- **Boundary conditions:** <source + update frequency; note if BCs are also perturbed> (TBD)

---

## Perturbations and design
- **Initial condition perturbations:** <e.g., singular vectors, EDA, bred vectors> (TBD)
- **Model/physics perturbations:** <TBD>
- **Stochastic schemes (optional):** <e.g., SPP/SPPT> (TBD)

---

## What it provides
Ensemble outputs such as:
- ensemble member forecasts
- ensemble mean / spread
- probabilities (optional, if distributed)
- percentile products (optional, if distributed)
- <add notable specialty variables or derived products if relevant>

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., CC BY 4.0 / CC BY-SA 4.0 / Etalab Open Licence / Public domain (U.S. government work; CC0-equivalent) / KNMI Open Data licence / Copernicus Marine Service licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Data formats:** <GRIB2 / NetCDF / both> (include compression notes if relevant)
- **Official download location:**  
  <URL>

---

## Notes
- <Relationship to deterministic counterpart (e.g., GEFS vs GFS, ICON-EPS vs ICON).>
- <Relationship to siblings: regional ensemble nests, multi-center combinations (NAEFS-style), AI-hybrid grand ensembles.>
- <If public data is a subset of full ops ensemble, note it.>
- <Anything else important: subsets, grid quirks, changes in ops, time-lagging conventions, etc.>
- <For AI-based or hybrid ensembles, note the AI approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the ensemble has documented operational upgrades worth tracking. See `gefs.md`, `mogreps-g.md`, `ifs-ens.md` for examples. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
