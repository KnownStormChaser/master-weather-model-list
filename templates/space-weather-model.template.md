# <MODEL NAME>

## What this model is
<Plain-language description of the system and the terrestrial effect it predicts — ionospheric electron content, auroral precipitation, HF radio absorption, thermospheric neutral density, or the ground-level geomagnetic and geoelectric response. Name the physical quantity a user would actually retrieve, not the storm that drives it.>

---

## Who runs it
- **Organization:** <Agency / operator>
- **Country / region:** <Country or multi-national org>

---

## Scope qualification (fill this in first)

<This category has two boundaries that most other categories do not, and both are easier to settle before drafting than after. Answer both here; if either answer disqualifies the system, it belongs on the Wiki's "Systems Not in the Catalog" page instead of in an entry.>

- **Reference body:** <**Earth** or **Sun**. Only Earth-referenced systems are in scope. Heliospheric models grid a volume in heliocentric coordinates — typically a spherical shell from roughly 0.1 to 2 AU — in which Earth is a single moving point. They produce no terrestrial grid and are excluded regardless of how much their output is used for terrestrial forecasting.> (TBD)
- **Forecast or specification:** <**Forecast**, **nowcast**, or **data-assimilative specification of the present**. Many space weather products described as models are real-time analyses that assimilate ground or satellite observations and carry no lead time at all. State the lead time explicitly, in minutes or hours. Zero-lead specifications are closer to this repository's gridded analysis products than to its forecast models, and may belong under `observations/` rather than here — resolve this before drafting rather than filing by the operator's own labelling.> (TBD)

---

## What area it covers
- **Coverage:** <Global / hemispheric / polar cap / regional>
- **Altitude or vertical extent (3D systems):** <e.g., 90 km to ~10,000 km / 100–1000 km / single ionospheric shell height> (TBD)
- **Latitude limits (optional):** <auroral and polar products are frequently restricted to magnetic latitudes above a threshold rather than covering the globe> (TBD)

---

## Basic details
- **Model type:** <Physics-based / empirical / data-assimilative / hybrid>
- **Core model:** <e.g., WAM-IPE / OVATION Prime / D-RAP / SWMF-Geospace / NeQuick> (TBD)
- **Forecast length:** <e.g., 2 days / 3 hours / 30 minutes> (TBD)
- **Update frequency / cycles:** <e.g., 4× daily at 00/06/12/18 UT / continuous / every 10 minutes> (TBD)
- **Temporal output resolution:** <e.g., 5 min / 10 min / 1 min — cadences in this category are much finer than in meteorological products and often differ between the 2D and 3D output streams of the same run> (TBD)

---

## Reference frame and grid geometry
<The most important section in this template and the one most likely to be wrong if taken from documentation. Coordinate systems in this category are unfamiliar and rarely stated in the file itself.>

- **Horizontal coordinate system:** <Geographic latitude/longitude / magnetic local time and magnetic latitude (AACGM) / geomagnetic solar magnetospheric (GSM) / projected grid> (TBD)
- **Grid dimensions:** <e.g., 360 × 181 / 96 MLT bins × 80 magnetic latitudes / 72 × 72> (TBD)
- **Horizontal resolution:** <e.g., 1° × 1° / 2.5° lat × 5° lon / 0.25 h MLT × 0.5° magnetic latitude> (TBD)
- **Vertical coordinate (3D systems):** <geometric altitude / pressure level / flux tube / fixed shell height> (TBD)
- **Conversion requirements:** <If the native frame is not geographic, state what a user needs in order to place the output on Earth — magnetic coordinate model and epoch, or the solar wind conditions defining the frame. Output in magnetic coordinates cannot be overlaid on a geographic map without this.> (TBD)

---

## Drivers and inputs
- **Solar wind input:** <real-time upstream monitor and its source, e.g., a spacecraft at L1; note whether the input is measured, propagated, or supplied by an upstream heliospheric model> (TBD)
- **Geomagnetic and solar indices used:** <e.g., Kp, F10.7, hemispheric power> (TBD)
- **Lower atmospheric forcing (optional):** <whole-atmosphere systems are driven from below by a meteorological analysis — name it and cross-link if it has an entry> (TBD)
- **Assimilated observations (data-assimilative systems):** <e.g., ground GNSS TEC, ionosondes, magnetometer observatories, satellite in-situ measurements; give the number of contributing stations if the file records it> (TBD)
- **Upstream model dependency (optional):** <If this system is driven by a heliospheric model that is itself out of scope, say so and note that the dependency does not bring the upstream model into scope.> (TBD)

---

## Ensemble configuration (ensemble systems only)

<**Delete this entire section for deterministic entries.** Do not leave it in place filled with TBDs — an empty ensemble block in a deterministic entry reads as missing research rather than as "not applicable.">

- **Ensemble size:** (TBD)
- **Source of perturbations:** <e.g., perturbed solar wind input, perturbed CME cone parameters> (TBD)
- **Member packaging:** (TBD)
- **Derived products distributed:** (TBD)

---

## What it provides
Terrestrial space weather forecasts including (as available):

**Ionosphere**
- total electron content (TEC)
- peak F2 density (NmF2) and height (hmF2)
- 3D electron and ion densities
- ion and electron temperatures, ion drifts
- scintillation and rate-of-change indices

**Thermosphere**
- neutral density (commonly at a reference altitude for orbit prediction)
- column-integrated O/N2 ratio
- neutral temperature and winds

**Auroral and magnetospheric**
- auroral energy flux or power
- probability of visible aurora
- ionospheric conductance and field-aligned currents

**Ground effects**
- HF radio absorption, often as a highest affected frequency
- geomagnetic field perturbation components
- geoelectric field components

---

## Data availability
- **Is the data free?** Yes / No / Partial
- **License:** <e.g., Public domain (U.S. government work; CC0-equivalent) / CC BY 4.0 / operator-specific terms> (note attribution obligations, and flag any non-commercial restriction explicitly — several space weather providers grant free scientific use only, which is not an open licence; TBD)
- **Registration required:** <Yes / No> (TBD)
- **Is the data downloadable?** Yes / No
- **Output geometry:** <Gridded fields / scattered grid points / station or point series> (TBD)
- **Data formats:** <NetCDF / JSON / ASCII table / GeoTIFF> — see the scope note below
- **Retention:** <these feeds are frequently short-window rolling directories with no long-term archive at the same endpoint; note the archive location separately if one exists> (TBD)
- **Official download location:**
  <URL>

> **Scope note.** Distribution formats in this category diverge sharply from the rest of the repository. Genuinely gridded forecast output is published as JSON coordinate lists and as fixed-width ASCII tables at least as often as it is published as NetCDF, and a product's format says little about whether it is a real grid. Judge the geometry from the data — whether values sit on a regular coordinate mesh with a defined frame and resolution — rather than from the container, and record the format plainly so the reader knows what parsing is required. Rendered maps, movie loops and web viewers remain out of scope as always, which excludes the great majority of what operational space weather centres publish.

---

## Notes
- <Operational status (active / experimental / research-to-operations transition), and how to interpret availability. Experimental endpoints are common here and are often served from a separate path than operational ones.>
- <Whether the operator runs the model or merely redistributes another agency's output — several national centres render models developed and run elsewhere, which is a redistribution relationship rather than an independent system.>
- <Timestamp semantics: separate observation time and forecast valid time are frequently both present and easily confused, and the difference between them is the actual lead time.>
- <Relationship to other models: the driving heliospheric model if any, the sibling nowcast or forecast configuration, and any archive product covering the same quantity.>
- <If the public data is a subset of the full operational output, state that here.>
- <For AI-based or hybrid systems, note the approach here and update [`AI_MODELS.md`](../AI_MODELS.md).>

---

## Recent version history (optional)
<Include this section if the system has documented operational upgrades worth tracking. Otherwise omit.>

---

## Official documentation
- <URL>
- <URL>
