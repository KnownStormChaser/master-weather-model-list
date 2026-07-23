# <MODEL NAME>

## What this model is
<Plain-language description of the wave model and what it forecasts.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## What area it covers
- **Coverage:** <Global / Basin / Coastal domain>
- **Domain details (optional):** <grid name / bounds / key seas>

---

## Basic details
- **Model type:** <Deterministic wave model / Ensemble wave model / Deterministic + ensemble>
- **Grid system:** <Single regular grid / multi-grid mosaic (e.g., WW3 `mult_1`) / unstructured mesh> (TBD)
- **Core wave model:** <e.g., WAVEWATCH III (WW3) / MFWAM / SWAN> (TBD)
- **Horizontal resolution:** <e.g., 0.25° / 0.1° / 0.025°> (domain-dependent if needed)
- **Forecast length:** <e.g., 240 h / 48 h>
- **Update frequency / cycles:** <e.g., 2× daily (00/12 UTC)>
- **Temporal output resolution:** <e.g., 1h / 3h / 6h> (TBD)

---

## Forcing and nesting
- **Wind forcing:** <source model + wind level (usually 10 m)> (TBD)
- **Ice forcing (if applicable):** <source + rules/thresholds> (TBD)
- **Current forcing (if applicable):** <source + one-way forcing vs two-way coupling> (TBD)
- **Nested inside / parent for:** <optional>

---

## Ensemble configuration (ensemble systems only)
- **Ensemble size:** <e.g., 30 perturbed + 1 control> (TBD)
- **Source of perturbations:** <usually inherited from the driving atmospheric ensemble — name it; state explicitly if the wave model itself is also perturbed, which is uncommon> (TBD)
- **Resolution / output differences vs deterministic sibling:** <ensemble wave output is often coarser and less frequent than the deterministic run> (TBD)
- **Member packaging:** <separate file per member vs member dimension in one file; GRIB2 `perturbationNumber` / `typeOfEnsembleForecast` encoding> (TBD)
- **Derived products distributed:** <ensemble mean, spread, SWH exceedance probabilities, percentiles — list only what is actually published as raw data> (TBD)

---

## Data assimilation (optional)
- **Assimilates wave observations:** <Yes/No> (TBD)
- **Observation sources (if yes):** <e.g., satellite altimeter SWH, SAR wave spectra; name missions if documented> (TBD)
- **Method / cadence (optional):** <e.g., Optimal Interpolation, daily cycle> (TBD)

---

## What it provides
Wave forecasts including (as available):
- significant wave height
- peak/mean period
- wave direction
- swell partitions (if available)
- additional spectral parameters (if available)

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Copernicus Marine Service licence (registration required) / CC BY 4.0 / Public domain (U.S. government work; CC0-equivalent) / Etalab Open Licence> (note attribution or share-alike obligations if applicable; TBD)
- **Is the data downloadable?** Yes / No
- **Data formats:** <GRIB2 / NetCDF / both>
- **Official download location:**  
  <URL>

---

## Notes
- <Any quirks: regional domains, coastal grids, multiple nested products, public subset vs full ops.>
- <Coupling and family relationships: two-way wave–ocean coupling, parent global model, regional nests, or distribution variants of the same core model (e.g., a Copernicus Marine distribution vs the operator's own distribution).>
- <For AI-based or hybrid wave systems, note the AI approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the model has documented operational upgrades worth tracking — wave systems change operator, driving NWP model, assimilation setup, and coupling often enough that this is frequently warranted. See `amm15-ww3-uk.md` for an example documenting an operator and assimilation-methodology change. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
