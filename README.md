# master-weather-model-list

This repository is a **simple, human-readable catalog of publicly available forecast models**.

It focuses on **numerical weather prediction (NWP) and related atmospheric forecast systems** whose data can be **freely downloaded** by anyone.

The goal is to make it easy to discover:
- which forecast models exist
- who produces them
- what regions they cover
- and where their data can be downloaded

No meteorology background is required to use this repository.

---

## What this repository includes
- **Numerical Weather Prediction (NWP) models** (deterministic)
- **Ensemble forecast models** (global and regional)
- **Wave forecast models**
- **Tropical cyclone / hurricane models**
- **Air quality and atmospheric composition models**
- **AI-based and hybrid physics–AI forecast systems**
- **Free and publicly downloadable data**
- Models provided by government, publicly funded, or military institutions that distribute output openly
- **Global**, **regional**, **coastal**, and **storm-centered** forecast systems

---

## What this repository does NOT include
- Paid, restricted, or licensed-only models
- Models that only provide maps or viewers without downloadable data
- Research-only, experimental, or one-off model runs that are not operationally distributed
- Climate reanalysis or historical-only datasets
- Ocean circulation models (for now)

---

## Repository structure

Models are organized by:
- **Model type** (weather, ensemble, wave, tropical cyclone, air quality)
- **Geographic scope** (global vs regional)
- **Country or organization of origin**

Each model has its own file describing:
- what the model is
- who runs it
- what area it covers
- basic resolution and update information
- where to download the data

---

## Finding models by topic

In addition to the directory structure, two index files make specific slices easier to discover:

- [`AI_MODELS.md`](./AI_MODELS.md) — index of all AI-based and hybrid physics–AI forecast systems documented in the repository, organized by how AI is used (standalone deterministic, standalone ensemble, hybrid, input-consumer)
- [`STATUS.md`](./STATUS.md) — tracker for upcoming implementations, scheduled retirements, experimental systems, version upgrades, and format changes

These exist because the country-and-type directory structure doesn't surface every useful view on its own. Users arriving with "show me all the AI models" or "what's being retired this year" can go straight to the relevant index.

---

## Why this exists

Information about forecast models is often:
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

Sponsorship helps support the time this takes: tracking operational changes, verifying sources, documenting upgrades and retirements, and adding new models as they become publicly available. Even small contributions are meaningful and appreciated — but the catalog will always be free for everyone.

---

## Disclaimer

This repository is for **information and discovery only**.
Always consult the official provider's documentation for:
- data usage terms
- licensing
- operational or safety-critical use

---

## Contributions

Corrections, additions, and improvements are welcome.

If you add a model, please ensure:
- the data is genuinely free and downloadable
- links point to official sources
- descriptions remain simple, factual, and non-promotional
- the entry uses one of the templates in [`templates/`](./templates/) as a starting point
