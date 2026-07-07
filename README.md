# master-weather-model-list

This repository is a **simple, human-readable catalog of publicly available forecast models and the satellite data that feeds them**.

Its primary focus is **numerical weather prediction (NWP) and related atmospheric forecast systems** whose data can be **freely downloaded** by anyone. It also includes a smaller catalog of **operational weather satellites with openly distributed raw data**, since these observation systems are foundational inputs to the models documented here.

The goal is to make it easy to discover:
- which forecast models exist
- who produces them
- what regions they cover
- where their data can be downloaded
- and which weather satellites provide raw data that can be freely accessed

No meteorology background is required to use this repository.

---

## What this repository includes
- **Numerical Weather Prediction (NWP) models** (deterministic)
- **Ensemble forecast models** (global and regional)
- **Nowcasting systems** (0–6 h prediction by observation extrapolation, seamless extrapolation–NWP blending, or machine learning)
- **Wave forecast models**
- **Ocean physics models** (temperature, salinity, currents, sea level, sea ice — distinct from wave models)
- **Tropical cyclone / hurricane models**
- **Air quality and atmospheric composition models**
- **Long-range forecast systems** (sub-seasonal, seasonal, and interannual prediction)
- **AI-based and hybrid physics–AI forecast systems**
- **Operational weather satellites** with openly distributed raw data (Level 1 calibrated radiances, Level 2 retrievals)
- **Free and publicly downloadable data**, including:
  - Data with no account required (e.g., NOAA NOMADS, AWS Open Data buckets)
  - Data requiring free registration or a free API key (e.g., EUMETSAT Data Store, KNMI Data Platform, Météo-France public API)
  - Data distributed under free open licenses (e.g., Creative Commons Attribution 4.0, Etalab Open Licence, Copernicus Marine Service licence, NOAA public domain)
- Models provided by government, publicly funded, or military institutions that distribute output openly
- **Global**, **regional**, **coastal**, and **storm-centered** forecast systems
- **Experimental and pre-operational systems** that are publicly distributed by their operating agency. These are clearly labelled as experimental in their entries (see [`STATUS.md`](./STATUS.md) for the current list)

---

## What this repository does NOT include
- Models or satellite data behind paywalls, commercial licensing, or usage-fee structures
- Data that requires approval gates, vetted-user programs, or commercial-use restrictions beyond simple registration
- Models that only provide maps or viewers without downloadable data (this excludes the many nowcasting products distributed only as web radar loops or viewer animations)
- Research-only or one-off model runs that are not distributed by an operating agency
- Climate **reanalysis** and other historical-only datasets (e.g., ERA5, CFSR) — note that forward-looking long-range *forecast* systems (sub-seasonal, seasonal, and interannual prediction) **are** in scope and are documented under `models/climate_models/`; only reanalyses and historical-only products are excluded
- Commercial weather satellites and Earth observation missions outside the operational weather/atmospheric scope

A note on free registration: some operators (notably EUMETSAT and KNMI) require a free account to download data. These are not paywalls — there is no fee, no approval gate, and no commercial-use restriction. Entries clearly note when registration is required so users know what to expect.

A note on experimental systems: some entries document systems their operators explicitly label as experimental. These are included when the operating agency distributes the data through an operational or near-operational channel (e.g., ECCC's MSC datamart, NOAA NOMADS). Pure research code, one-off runs, and systems without ongoing public distribution are not included.

---

## Repository structure

Forecast models are organized by:
- **Model type** (weather, ensemble, nowcasting, wave, ocean, tropical cyclone, air quality, long-range)
- **Geographic scope** (global vs regional)
- **Country or organization of origin**

Each model has its own file describing:
- what the model is
- who runs it
- what area it covers
- basic resolution and update information
- where to download the data

Satellites are organized in a parallel structure under `observations/satellites/` by:
- **Orbit type** (geostationary vs polar-orbiting)
- **Country or organization of origin**

Each satellite entry covers a constellation or series (e.g., GOES-R Series rather than separate GOES-16 and GOES-19 entries) and follows the same general format as the model entries. See [`observations/satellites/README.md`](./observations/satellites/README.md) for the index of currently documented satellite systems.

---

## Finding models by topic

In addition to the directory structure, several index files make specific slices easier to discover:

- [`AI_MODELS.md`](./AI_MODELS.md) — index of all AI-based and hybrid physics–AI forecast systems documented in the repository, organized by how AI is used (standalone deterministic, standalone ensemble, hybrid, input-consumer)
- [`STATUS.md`](./STATUS.md) — tracker for upcoming implementations, scheduled retirements, experimental systems, version upgrades, and format changes
- [`COPERNICUS.md`](./COPERNICUS.md) — index of all products distributed through the Copernicus Marine Service and Copernicus Atmosphere Monitoring Service, organized by operator and coverage
- [`UFS.md`](./UFS.md) — index of all NOAA models that are part of, being consolidated into, or being retired by the Unified Forecast System, with the full programme context and rollout sequencing
- [`observations/satellites/README.md`](./observations/satellites/README.md) — index of operational weather satellites with openly downloadable raw data, with quick-reference tables for access tiers

These exist because the country-and-type directory structure doesn't surface every useful view on its own. Users arriving with "show me all the AI models" or "what's being retired this year" or "which satellites have no-account-needed S3 access" can go straight to the relevant index.

---

## Why this exists

Information about forecast models and weather satellites is often:
- scattered across many websites
- written for experts only
- difficult to compare or discover

This repository aims to provide a **clear, neutral, and beginner-friendly index** of what is publicly available.

---

## Supporting this project

This repository is maintained as independent research. Everything stays free and openly accessible — no paywalls, accounts, ads, or affiliate links.

If you've found the catalog useful in your work or research, you can optionally support ongoing maintenance through any of the following:

- [GitHub Sponsors](https://github.com/sponsors/KnownStormChaser)
- [Ko-fi](https://ko-fi.com/knownstormchaser)
- [Buy Me a Coffee](https://buymeacoffee.com/sebastianpd)
- [Liberapay](https://liberapay.com/KnownStormChaser)

Sponsorship helps support the time this takes: tracking operational changes, verifying sources, documenting upgrades and retirements, and adding new models and satellites as they become publicly available. Even small contributions are meaningful and appreciated — but the catalog will always be free for everyone.

---

## Disclaimer

This repository is for **information and discovery only**.
Always consult the official provider's documentation for:
- data usage terms
- licensing
- operational or safety-critical use

---

## A note on accuracy

I try to document each model and satellite as accurately as possible based on
official product user manuals, technical documentation, agency publications, and
peer-reviewed references. However, **information in this repository may be
incomplete, out of date, or inaccurate.** This is particularly true for:

- Models or satellites with limited English-language documentation
- Systems operated by agencies that do not publish detailed technical
  specifications publicly
- Recent operational upgrades that may not yet be reflected in published
  documentation
- Data distribution details (access URLs, file formats, update schedules) that
  change without announcement
- Operational role assignments for satellite constellations, which rotate over
  time as new spacecraft commission and older ones retire

Operational NWP and satellite systems evolve continuously. Resolution changes,
physics upgrades, ensemble size changes, instrument decommissioning, and
distribution channel changes can happen with little public notice, and the gap
between documented and operational configurations can be months or years for
some systems.

If you spot an error, know of a more recent version of a model's documentation,
or have corrections to contribute, please see the [Contributions](#contributions)
section — corrections and updates are genuinely welcome and are the main way this
catalogue stays useful over time.

---

## Contributions

Corrections, additions, and improvements are welcome.

If you add a model or satellite, please ensure:
- the data is genuinely free and downloadable (free registration is acceptable; paywalls and approval gates are not)
- links point to official sources
- descriptions remain simple, factual, and non-promotional
- the entry uses one of the templates in [`templates/`](./templates/) as a starting point
