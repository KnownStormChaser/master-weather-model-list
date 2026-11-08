# Run Your Own Model

This page lists weather, ocean, wave and climate models whose code (and, for AI models, trained weights) is freely available, so you can run forecasts or simulations yourself. It is meant for anyone from a hobbyist with one PC in the basement to a university group or a national weather service.

It is a companion to the main catalog, not part of it. The rest of this repository documents freely downloadable *model output*. This page documents the *software*, with a short description and links to official documentation and source code for each one. Where output from a model is already documented elsewhere in the repository, the entry links to it.

Last updated: November 2026.

---

## What is listed here

A model is listed when all of the following are true:

- **It is actively developed or maintained.** Retired, discontinued, deprecated or archived models are not listed, the same as in the rest of the repository.
  - An independent project that actively maintains a model its original developer has retired counts as maintained (for example [WOOF-NAM](#woof-nam)).
  - AI models are the one exception. A set of trained weights released once, alongside a paper, is listed as a **Research release** as long as the code still runs, the documentation is still available and the licence is still open.
- **It can be downloaded directly.** Anyone can download the code and weights without filling in a request form, signing an agreement or waiting for approval.
- **Its licence is open or noncommercial.** Code and weights published under a licence that clearly allows reuse, including licences that allow noncommercial use only. Models under unclear licences, or custom licences more restrictive than these, are not listed.

## How to read an entry

- **Licence** shows how the code or weights may be used.
  - **Open** means commercial use is allowed (for example public domain, MIT, BSD, Apache-2.0, LGPL, GPL, CeCILL, CC BY 4.0).
  - **Open, with conditions** means commercial use is allowed, but the licence adds conditions that conventional open licences don't have, such as use rules or the licensor's right to change the terms. Currently this applies only to the NVIDIA Open Model License.
  - **Non-commercial** means research, teaching and personal use only (for example CC BY-NC-SA 4.0).
  - Code and weights are listed separately when their licences differ.
- **Status** is **Active** (ongoing development or releases) or **Research release** (AI weights published once and not updated since).
- **Typical scale** is a rough guide to the hardware needed:
  - **PC:** a single desktop or laptop, enough for small domains or coarse resolutions.
  - **GPU workstation:** one modern GPU, typically with 16–24 GB of memory or more.
  - **Cluster / HPC:** many CPU cores or GPUs across several machines.
  - Most physics-based models range from PC to HPC depending on domain size and resolution.
- **Starting data** is what the model needs to start a run. No model in this list creates its own forecast from nothing. Physics-based regional models also need boundary data from a larger model, and most AI models start from an analysis produced by a global centre.

## What this page does not cover

- Data assimilation systems, pre- and post-processing tools, and visualisation software.
- Retired, deprecated or archived models.
- Models whose code or weights are only available on request, under a signed agreement, or to partner institutions.
- Models under unclear licences.

---

## Contents

- [Regional and limited-area atmospheric models](#regional-and-limited-area-atmospheric-models)
- [Global and multi-scale atmospheric models](#global-and-multi-scale-atmospheric-models)
- [Ocean, wave and storm surge models](#ocean-wave-and-storm-surge-models)
- [Climate and Earth system models](#climate-and-earth-system-models)
- [AI weather models](#ai-weather-models)
- [AI frameworks and toolkits](#ai-frameworks-and-toolkits)

---

## Regional and limited-area atmospheric models

### WRF (Weather Research and Forecasting Model)
The most widely used regional weather model, with a very large research and operational user community. The usual starting point for running your own regional forecasts.
- **Developer:** NSF NCAR, with NOAA and community contributors — United States
- **Type:** Regional NWP (limited-area), research and operational
- **Licence:** Open (public domain)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** Global model or reanalysis fields, such as GFS, ECMWF open data or ERA5, prepared with the WRF Preprocessing System (WPS)
- **Documentation:** https://www2.mmm.ucar.edu/wrf/users/
- **Source:** https://github.com/wrf-model/WRF
- **In this catalog:** operational WRF configurations such as [WRF (SMN, Argentina)](./models/nwp_models/regional/argentina/wrf-smn.md), [WRF (CPTEC, Brazil)](./models/nwp_models/regional/brazil/wrf-cptec.md), [WRF (IDEAM, Colombia)](./models/nwp_models/regional/colombia/wrf-ideam.md), [WRF (Hungary)](./models/nwp_models/regional/hungary/wrf-hungary.md) and [WRF (TMD, Thailand)](./models/nwp_models/regional/thailand/wrf-tmd.md)

### Méso-NH
The French research community's non-hydrostatic mesoscale model, covering scales from synoptic down to large-eddy simulation, with strong cloud, chemistry and atmospheric electricity options. Parts of its physics are shared with Météo-France's operational AROME.
- **Developer:** Laboratoire d'Aérologie (CNRS / Université de Toulouse) and CNRM (CNRS / Météo-France) — France
- **Type:** Regional research model (mesoscale to large-eddy scale)
- **Licence:** Open (CeCILL-C)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** Global or regional model analyses (such as ECMWF or ARPEGE/AROME fields), or idealised initial conditions
- **Documentation:** https://mesonh.readthedocs.io/
- **Source:** https://mesonh.readthedocs.io/en/mnh-6-0/getting_started/download.html (Git instructions and release tarballs on Zenodo)

### WOOF (Weather Observations and Open Forecasting)
A regional weather model with WRF-class physics, written to run on a single NVIDIA GPU. Preprocessing, data assimilation and map plotting are all built into one package, which makes it one of the simplest ways to run high-resolution regional forecasts on home hardware. The same install also includes a preview GPU port of MPAS-Atmosphere (the "hex" model), for regional and global runs.
- **Developer:** Recast Systems — United States
- **Type:** Regional NWP (WRF-ARW-class, with nests and data assimilation). The hex model (MPAS-Atmosphere on the GPU, regional and global) is a preview
- **Licence:** Open (Apache-2.0; the parts transcribed from WRF keep WRF's public-domain terms)
- **Status:** Active (regional model marked production; hex model marked preview)
- **Typical scale:** GPU workstation. It sizes the domain to the card you have, with tiers starting at 12 GB
- **Starting data:** Downloads its own: GFS, GDAS, HRRR, RAP, RRFS, ERA5, ECMWF open data and AIFS, ICON, GEM GDPS and others, with no accounts needed for the open sources. It can also write input files for standard WRF
- **Documentation:** https://github.com/recastsystems/woof (README and `docs/public/`)
- **Source:** https://github.com/recastsystems/woof (also on PyPI as `recast-woof`)

### WOOF-NAM
A GPU port of NOAA/NCEP's North American Mesoscale model (NAM v4.2.11): the NMMB dynamics and the NAM's operational physics, running on one NVIDIA card or several cards in one machine. NCEP retired the NAM on November 3, 2026; WOOF-NAM keeps the model available for anyone to run. It runs the NAM's 12 km North American grid, and also supports nests, for example 3 km.
- **Developer:** Recast Systems (independent; not affiliated with NOAA), porting NOAA/NCEP's NAM code — United States
- **Type:** Regional NWP (limited-area), North America
- **Licence:** Open (Apache-2.0; the ported NAM code is U.S. Government work, public domain in the U.S.)
- **Status:** Active (early releases)
- **Typical scale:** GPU workstation. The 12 km grid needs an NVIDIA card with 24 GB or more, or two or more 32 GB cards in one machine
- **Starting data:** GFS, IFS or ERA5 analyses, for real-time runs or hindcasts; archived NAM analyses for cycles before November 3, 2026. NOAA's data comes from its open-data buckets, with no account needed
- **Documentation:** https://github.com/recastsystems/woof-nam (README)
- **Source:** https://github.com/recastsystems/woof-nam (also on PyPI as `recast-woof-nam`)
- **Related:** [Pirate NAM](https://github.com/Pirate-Weather/pirate-nam) (Apache-2.0) runs WOOF-NAM in a full NAM-style cycle, with NOAA's GSI data assimilation, out to 84 hours
- **In this catalog:** [Pirate NAM](./models/nwp_models/non_governmental/pirate-nam.md)

### CM1 (Cloud Model 1)
A model built for idealised studies of small-scale processes such as thunderstorms, supercells, tropical cyclones and turbulence. It runs from a single sounding instead of real-world weather data, so it is not a forecast model, but it is one of the best tools for learning and studying storm dynamics.
- **Developer:** George Bryan, NSF NCAR — United States
- **Type:** Idealised cloud-resolving and large-eddy research model
- **Licence:** Open (MIT)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** An input sounding or analytic profile, with no real-world weather data needed
- **Documentation:** https://www2.mmm.ucar.edu/people/bryan/cm1/
- **Source:** https://github.com/NCAR/cm1

---

## Global and multi-scale atmospheric models

### MPAS-Atmosphere (Model for Prediction Across Scales)
Runs globally or regionally on an unstructured, variable-resolution mesh, so one area can be refined without separate nested domains. NOAA plans to use MPAS as the dynamical core for the next generation of its regional system, RRFS version 2 (see [`UFS.md`](./UFS.md)).
- **Developer:** NSF NCAR and Los Alamos National Laboratory — United States
- **Type:** Global and regional NWP, variable resolution
- **Licence:** Open (BSD-style)
- **Status:** Active
- **Typical scale:** PC (coarse global meshes) to Cluster / HPC
- **Starting data:** Global model or reanalysis fields, such as GFS or ERA5
- **Documentation:** https://mpas-dev.github.io/
- **Source:** https://github.com/MPAS-Dev/MPAS-Model
- **In this catalog:** [NSSL MPAS](./models/nwp_models/regional/usa/nssl-mpas.md)

### ICON (ICOsahedral Nonhydrostatic)
The model behind DWD's operational global and regional forecasts, also used for climate research. It runs globally, regionally (ICON-LAM) or coupled, and has been open source since 2024.
- **Developer:** ICON partnership — DWD, MPI-M, DKRZ and KIT (Germany) with C2SM at ETH Zurich and MeteoSwiss (Switzerland)
- **Type:** Global and regional NWP and climate model
- **Licence:** Open (BSD-3-Clause)
- **Status:** Active
- **Typical scale:** Workstation (coarse or regional set-ups) to Cluster / HPC
- **Starting data:** ICON or IFS analyses, or reanalysis fields, interpolated to an ICON grid
- **Documentation:** https://www.icon-model.org/
- **Source:** https://gitlab.dkrz.de/icon/icon-model
- **In this catalog:** [ICON Global (DWD)](./models/nwp_models/global/germany/icon-global.md)

### GEM (Global Environmental Multiscale Model)
Environment and Climate Change Canada's operational model, used for its global, regional and high-resolution deterministic systems.
- **Developer:** Environment and Climate Change Canada (ECCC), Meteorological Research Division — Canada
- **Type:** Global and regional NWP
- **Licence:** Open (LGPL-2.1)
- **Status:** Active
- **Typical scale:** Workstation to Cluster / HPC
- **Starting data:** Analyses from a global model, prepared into GEM's input format
- **Documentation:** https://github.com/ECCC-ASTD-MRD/gem (README and wiki)
- **Source:** https://github.com/ECCC-ASTD-MRD/gem
- **In this catalog:** [GDPS / GEM Global](./models/nwp_models/global/canada/gem-global.md), [RDPS](./models/nwp_models/regional/canada/rdps.md), [HRDPS](./models/nwp_models/regional/canada/hrdps.md)

### OpenIFS
The forecast model from ECMWF's Integrated Forecasting System (IFS), packaged for use outside ECMWF. It gives the same forecast capability as the IFS without the data assimilation system, and has been open source since March 2026. Only the GitHub release is covered by the open-source licence; earlier copies obtained under the old OpenIFS licence agreement are not.
- **Developer:** ECMWF — Intergovernmental (Europe)
- **Type:** Global NWP; also includes a single-column model
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** Workstation (low resolutions) to Cluster / HPC
- **Starting data:** Initial conditions from the OpenIFS Data Hub (free registration), or your own initial conditions built from ERA5 on the Copernicus Climate Data Store (free account)
- **Documentation:** https://confluence.ecmwf.int/spaces/OIFS/pages/19661477/OpenIFS+Home
- **Source:** https://github.com/ecmwf-ifs/openifs
- **In this catalog:** [IFS (ECMWF)](./models/nwp_models/global/eu/ifs.md)

### UFS Weather Model (Unified Forecast System)
The community model at the centre of NOAA's move to a single forecasting system. It runs the FV3 dynamical core with CCPP physics and can be coupled to ocean (MOM6), sea ice (CICE6) and waves (WAVEWATCH III). It is the base of GFS version 17 and HAFS. The UFS Short-Range Weather App, previously the main way to run it regionally, was deprecated in October 2026.
- **Developer:** NOAA and the UFS community (with NSF NCAR and other partners), coordinated by NOAA's EPIC program — United States
- **Type:** Global and regional NWP; coupled Earth-system modelling
- **Licence:** Open (mixed: U.S. Government public domain, plus components under LGPL-3.0, Apache-2.0, GPL-3.0 and other open licences; see `LICENSE.md`)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** GFS / GDAS analyses, converted with the UFS preprocessing utilities
- **Documentation:** https://ufs-weather-model.readthedocs.io/
- **Source:** https://github.com/ufs-community/ufs-weather-model
- **In this catalog:** see [`UFS.md`](./UFS.md), including [GFS](./models/nwp_models/global/usa/gfs.md), [RRFS](./models/nwp_models/regional/usa/rrfs.md) and [HAFS](./models/tropical_cyclone_models/hafs.md)

### LFRic Atmosphere
The Met Office's next-generation atmosphere model, built on the GungHo dynamical core and the LFRic infrastructure, and designed to succeed the Unified Model. The model code and its physics components (JULES, SOCRATES, CASIM, UKCA) are all published on GitHub. The example set-ups are aimed at Met Office systems, so running it elsewhere takes more effort than the other models in this section.
- **Developer:** Met Office, within the Momentum® Partnership — United Kingdom
- **Type:** Global and regional NWP and climate model
- **Licence:** Open (BSD-3-Clause)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** Global analyses converted to LFRic's input format
- **Documentation:** https://metoffice.github.io/lfric_apps/
- **Source:** https://github.com/MetOffice/lfric_apps (application) and https://github.com/MetOffice/lfric_core (infrastructure)

### SHiELD (System for High-resolution prediction on Earth-to-Local Domains)
GFDL's model built on the same FV3 dynamical core used by NOAA's UFS. It runs global, regional and nested configurations, from medium-range weather to convection-allowing scales.
- **Developer:** NOAA Geophysical Fluid Dynamics Laboratory (GFDL) — United States
- **Type:** Global and regional NWP, with nesting
- **Licence:** Open (LGPL-3.0)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** GFS analyses or similar global fields
- **Documentation:** https://github.com/NOAA-GFDL/SHiELD_build (README)
- **Source:** https://github.com/NOAA-GFDL/SHiELD_build

---

## Ocean, wave and storm surge models

### MOM6 (Modular Ocean Model 6)
A general ocean circulation model used for both climate and forecasting. It is the ocean component of NOAA's UFS and CESM, among others.
- **Developer:** NOAA GFDL and NSF NCAR, with a multi-institution consortium — United States
- **Type:** Global and regional ocean circulation model
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** Workstation (coarse or regional) to Cluster / HPC
- **Starting data:** Ocean analyses or climatologies, plus atmospheric forcing
- **Documentation:** https://mom6.readthedocs.io/
- **Source:** https://github.com/mom-ocean/MOM6

### NEMO (Nucleus for European Modelling of the Ocean)
The main European ocean modelling framework, covering ocean dynamics, sea ice (SI³) and biogeochemistry (TOP). Many Copernicus Marine and national ocean forecasts are built on it.
- **Developer:** NEMO Consortium (CNRS, Mercator Ocean International, Met Office, CMCC, NOC and others) — Europe
- **Type:** Global and regional ocean, sea-ice and biogeochemistry model
- **Licence:** Open (CeCILL v2.0)
- **Status:** Active
- **Typical scale:** Workstation (regional) to Cluster / HPC
- **Starting data:** Ocean analyses or climatologies, plus atmospheric forcing
- **Documentation:** https://www.nemo-ocean.eu/
- **Source:** https://forge.nemo-ocean.eu/nemo/nemo
- **In this catalog:** for example [NEMO Baltic (FMI)](./models/ocean_models/regional/finland/nemo-baltic-fmi.md), [NEMO (Estonia)](./models/ocean_models/regional/estonia/nemo-est.md) and [BAL MFC NEMO](./models/ocean_models/regional/sweden/bal-mfc-nemo.md)

### ROMS (Regional Ocean Modeling System)
A widely used regional and coastal ocean model, with data assimilation and coupling options.
- **Developer:** Rutgers University and the ROMS community — United States
- **Type:** Regional and coastal ocean circulation model
- **Licence:** Open (MIT)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** Ocean analyses from a larger model, plus atmospheric and tidal forcing
- **Documentation:** https://www.myroms.org/wiki/
- **Source:** https://github.com/myroms/roms
- **In this catalog:** for example [NorKyst v3](./models/ocean_models/regional/norway/norkyst-v3.md), [Norshelf](./models/ocean_models/regional/norway/norshelf.md) and [Barents-2.5km EPS](./models/ocean_models/regional/norway/barents-25km-eps.md)

### CROCO (Coastal and Regional Ocean COmmunity model)
A French regional and coastal ocean model that grew out of ROMS_AGRIF. It adds a non-hydrostatic option and sediment and biogeochemistry modules for very fine coastal scales.
- **Developer:** CROCO Development Team (Inria, Ifremer, SHOM, CNRS, IRD, Université de Toulouse, UBO) — France
- **Type:** Regional, coastal and nearshore ocean model
- **Licence:** Open (CeCILL-2.1)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** Ocean analyses or climatologies, plus atmospheric and tidal forcing (preparation tools are provided)
- **Documentation:** https://croco-ocean.gitlabpages.inria.fr/croco_doc/
- **Source:** https://gitlab.inria.fr/croco-ocean/croco

### SCHISM (Semi-implicit Cross-scale Hydroscience Integrated System Model)
An unstructured-grid model covering the range from creeks and rivers to the open ocean, used for storm surge, compound flooding and coastal circulation.
- **Developer:** Virginia Institute of Marine Science (VIMS) and the SCHISM community — United States
- **Type:** Cross-scale coastal ocean, estuary and storm surge model
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** Workstation to Cluster / HPC
- **Starting data:** Ocean analyses, atmospheric forcing, tides and river flows
- **Documentation:** https://schism-dev.github.io/schism/
- **Source:** https://github.com/schism-dev/schism
- **In this catalog:** [STOFS-3D-Atlantic](./models/storm_surge_models/regional/usa/stofs-3d-atlantic.md)

### ADCIRC (ADvanced CIRCulation model)
A finite-element model for storm surge, tides and coastal flooding, widely used for hurricane surge studies.
- **Developer:** ADCIRC development group (University of North Carolina, University of Notre Dame and others) — United States
- **Type:** Storm surge, tide and coastal circulation model
- **Licence:** Open (LGPL-3.0)
- **Status:** Active
- **Typical scale:** PC (small meshes) to Cluster / HPC
- **Starting data:** Atmospheric wind and pressure forcing, plus tidal forcing
- **Documentation:** https://adcirc.org/
- **Source:** https://github.com/adcirc/adcirc-cg
- **In this catalog:** [STOFS-2D-Global](./models/storm_surge_models/global/usa/stofs-2d-global.md)

### WAVEWATCH III
NOAA's spectral wave model, run operationally by many weather services for global and regional wave forecasts.
- **Developer:** NOAA / NCEP Environmental Modeling Center, with community contributors — United States
- **Type:** Global and regional spectral wave model
- **Licence:** Open (LGPL-3.0)
- **Status:** Active
- **Typical scale:** PC (regional) to Cluster / HPC
- **Starting data:** Surface wind forcing from an atmospheric model (and optionally sea ice and currents)
- **Documentation:** https://github.com/NOAA-EMC/WW3/wiki
- **Source:** https://github.com/NOAA-EMC/WW3
- **In this catalog:** for example [WAVEWATCH III (NOAA)](./models/wave_models/global/usa/ww3-noaa.md) and [MET Norway WW3](./models/wave_models/regional/norway/met-norway-ww3.md)

### SWAN (Simulating WAves Nearshore)
A wave model for coastal regions, lakes and estuaries, where depth and currents strongly affect waves. Pre-compiled versions are available for Windows, Linux and macOS, so it is one of the easiest models in this list to try.
- **Developer:** Delft University of Technology — Netherlands
- **Type:** Coastal and inland-water spectral wave model
- **Licence:** Open (GPL)
- **Status:** Active
- **Typical scale:** PC to Cluster / HPC
- **Starting data:** Wind forcing and bathymetry, plus offshore wave boundary conditions (for example from WAVEWATCH III or ecWAM)
- **Documentation:** https://swanmodel.sourceforge.io/
- **Source:** https://swanmodel.sourceforge.io/download/download.htm
- **In this catalog:** [SWAN (Estonia)](./models/wave_models/regional/estonia/swan-est.md)

### ecWAM
ECMWF's ocean wave model, the wave component of the IFS. It can also run standalone, driven by external forcing in GRIB files.
- **Developer:** ECMWF — Intergovernmental (Europe)
- **Type:** Global and regional spectral wave model
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** Workstation to Cluster / HPC
- **Starting data:** Surface wind forcing in GRIB format (and optionally sea ice and currents)
- **Documentation:** https://github.com/ecmwf-ifs/ecwam (README)
- **Source:** https://github.com/ecmwf-ifs/ecwam
- **In this catalog:** [ECWAM (ECMWF)](./models/wave_models/global/eu/ecwam.md)

---

## Climate and Earth system models

### CESM (Community Earth System Model)
A fully coupled global climate model (atmosphere, ocean, land, sea ice, land ice) and one of the most widely used climate models in research.
- **Developer:** NSF NCAR, with university and U.S. government partners — United States
- **Type:** Global coupled climate / Earth system model
- **Licence:** Open (BSD-style UCAR terms)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** Bundled input datasets and forcing scenarios, downloaded by the model's set-up scripts
- **Documentation:** https://www.cesm.ucar.edu/
- **Source:** https://github.com/ESCOMP/CESM

### E3SM (Energy Exascale Earth System Model)
The U.S. Department of Energy's Earth system model, designed for high resolution on the largest supercomputers.
- **Developer:** U.S. Department of Energy national laboratories — United States
- **Type:** Global coupled climate / Earth system model
- **Licence:** Open (BSD-3-Clause; a few separable parts under other open licences)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** Bundled input datasets and forcing scenarios
- **Documentation:** https://e3sm.org/
- **Source:** https://github.com/E3SM-Project/E3SM

### NorESM (Norwegian Earth System Model)
Norway's Earth system model, based on CESM with its own ocean model (BLOM) and atmospheric chemistry and aerosol developments.
- **Developer:** NorESM consortium (University of Bergen, NORCE, Nansen Center, MET Norway, University of Oslo, NILU, CICERO) — Norway
- **Type:** Global coupled climate / Earth system model
- **Licence:** Open (BSD-style; the BLOM ocean component under LGPL-3.0; CESM parts under CESM terms)
- **Status:** Active
- **Typical scale:** Cluster / HPC
- **Starting data:** Bundled input datasets and forcing scenarios
- **Documentation:** https://noresm-docs.readthedocs.io/
- **Source:** https://github.com/NorESMhub/NorESM

---

## AI weather models

AI models are much cheaper to run than physics-based models: a 10-day global forecast takes minutes on one GPU. They do not produce forecasts from nothing, though. Almost all of them start from an analysis produced by a global centre's physics-based system. For how these models are used operationally, see [`AI_MODELS.md`](./AI_MODELS.md).

### AIFS Single and AIFS ENS (ECMWF)
ECMWF's operational AI models: AIFS Single (deterministic) and AIFS ENS (a 51-member ensemble). They are the most production-ready open AI models available, and run the same way ECMWF runs them.
- **Developer:** ECMWF — Intergovernmental (Europe)
- **Type:** Global AI forecast model, deterministic and ensemble, up to 15 days
- **Licence:** Weights: Open (CC BY 4.0). Code (Anemoi): Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** ECMWF open data, with no account needed
- **Documentation:** https://huggingface.co/ecmwf (model cards with example notebooks)
- **Source:** https://huggingface.co/ecmwf/aifs-single-2.0 and https://huggingface.co/ecmwf/aifs-ens-2.0
- **In this catalog:** [AIFS Single](./models/nwp_models/global/eu/aifs-single.md), [AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)

### GEML (ECCC)
ECCC's AI model, trained from scratch on ERA5 and ECMWF analyses. It uses the same model code as GraphCast, so it runs with that code unchanged. The published weights are GEML version 1.0.
- **Developer:** Environment and Climate Change Canada (ECCC) — Canada
- **Type:** Global AI forecast model, deterministic, 0.25°
- **Licence:** Weights: Open (Open Government Licence – Canada). Inference code (GraphCast): Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** 0.25°, 13-pressure-level atmospheric fields at two consecutive time steps, 6 hours apart (see the model card)
- **Documentation:** https://huggingface.co/ECCC-ASTD-MRD/geml
- **Source:** https://huggingface.co/ECCC-ASTD-MRD/geml
- **In this catalog:** [GEML](./models/nwp_models/global/canada/gdps-geml.md)

### Bris (MET Norway)
MET Norway's AI ensemble model for the Nordic region, built with Anemoi. The checkpoints and their training and inference configurations are published.
- **Developer:** Norwegian Meteorological Institute (MET Norway) — Norway
- **Type:** Regional AI ensemble forecast model
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** As defined in the published Anemoi inference configuration
- **Documentation:** https://huggingface.co/met-no/bris-forecaster
- **Source:** https://huggingface.co/met-no (Bris model repositories)
- **In this catalog:** [Bris](./models/ensemble_models/regional/norway/bris.md)

### WeatherNext 2 (Google DeepMind)
Google DeepMind's ensemble forecast model, including its cyclone-forecasting variants, which also has a lightweight 1° "Mini" version for smaller hardware. The same repository hosts the earlier GraphCast (now "WeatherNext Graph") and GenCast (now "WeatherNext Gen"). Their weights moved from a non-commercial licence to CC BY 4.0 on August 6, 2026.
- **Developer:** Google DeepMind and Google Research — United States / United Kingdom
- **Type:** Global AI forecast model, ensemble, with tropical cyclone tracking
- **Licence:** Weights: Open (CC BY 4.0). Code: Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** GPU workstation. The full 0.25° models need an H100-class GPU (or TPU); the 1° Mini models run on much smaller GPUs, and the demo notebook runs on a free Colab TPU
- **Starting data:** ECMWF HRES initial conditions; sample data is provided
- **Documentation:** https://developers.google.com/weathernext/guides/models
- **Source:** https://github.com/google-deepmind/weathernext
- **In this catalog:** [GraphCast — AIWP archive](./models/nwp_models/global/usa/graphcast-aiwp.md)

### NeuralGCM (Google)
A hybrid model: a conventional physics-based dynamical core combined with machine-learned physics. It can be used for weather forecasting and for multi-year climate simulations.
- **Developer:** Google Research — United States
- **Type:** Global hybrid physics–AI model, weather and climate
- **Licence:** Weights: Open (CC BY-SA 4.0). Code: Open (Apache-2.0)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** ERA5-format atmospheric fields
- **Documentation:** https://neuralgcm.readthedocs.io/
- **Source:** https://github.com/neuralgcm/neuralgcm

### Aurora (Microsoft)
A foundation model for the atmosphere, with checkpoints for medium-range weather, air pollution, ocean waves and tropical cyclone tracks.
- **Developer:** Microsoft Research AI for Science — United States
- **Type:** Global AI foundation model, weather and related Earth-system variables
- **Licence:** Open (MIT, code and weights)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** ERA5 or ECMWF HRES analyses, depending on the checkpoint
- **Documentation:** https://microsoft.github.io/aurora/
- **Source:** https://github.com/microsoft/aurora (code) and https://huggingface.co/microsoft/aurora (weights)
- **In this catalog:** [Aurora — AIWP archive](./models/nwp_models/global/usa/aurora-aiwp.md)

### FuXi 2.1
The current version of the FuXi model, which keeps fine detail in forecast fields longer into the forecast instead of smoothing it out, improving forecasts of heavy rain and strong wind. It produces a 10-day global forecast at 0.25°, in 6-hour steps.
- **Developer:** Fudan University and the Shanghai Academy of Artificial Intelligence for Science (SAIS) — China
- **Type:** Global AI forecast model, deterministic
- **Licence:** Open (CC BY 4.0)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** 0.25° atmospheric fields; a sample input file is included
- **Documentation:** https://huggingface.co/tpys/fuxi-2.1
- **Source:** https://huggingface.co/tpys/fuxi-2.1

### ArchesWeather and ArchesWeatherGen
Compact AI models from France: ArchesWeather (deterministic) and ArchesWeatherGen (an ensemble version), designed to be trained and run on modest computing resources.
- **Developer:** ARCHES team, Inria — France
- **Type:** Global AI forecast model, deterministic and ensemble
- **Licence:** Weights: Open (BSD). Code (geoarches): Open (BSD-3-Clause)
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** ERA5 (see the geoarches documentation)
- **Documentation:** https://github.com/INRIA/geoarches (README and `docs/archesweather/`)
- **Source:** https://github.com/INRIA/geoarches (code) and https://huggingface.co/gcouairon/ArchesWeather (weights)

### Pangu-Weather (Huawei)
One of the first AI models to match physics-based forecasts in skill. It has four models with 1-, 3-, 6- and 24-hour time steps, distributed in ONNX format.
- **Developer:** Huawei Cloud — China
- **Type:** Global AI forecast model, deterministic, 0.25°
- **Licence:** Weights: Non-commercial (CC BY-NC-SA 4.0)
- **Status:** Research release
- **Typical scale:** GPU workstation (CPU inference also works, more slowly)
- **Starting data:** ERA5-format surface and upper-air fields; the AIWP archive runs it from GFS and IFS analyses
- **Documentation:** https://github.com/198808xc/Pangu-Weather (README)
- **Source:** https://github.com/198808xc/Pangu-Weather (download links to the weights)
- **In this catalog:** [Pangu-Weather — AIWP archive](./models/nwp_models/global/usa/pangu-aiwp.md)

### Prithvi WxC (NASA and IBM)
A foundation model for weather and climate, trained on NASA's MERRA-2 reanalysis. It is meant to be fine-tuned for specific tasks such as downscaling or gravity-wave parameterisation, not used as a ready-made forecast model.
- **Developer:** NASA (IMPACT) and IBM Research — United States
- **Type:** AI foundation model for weather and climate applications
- **Licence:** Weights: Open (CDLA-Permissive-2.0 for the base model; Apache-2.0 for the rollout and gravity-wave models). Code: Open (MIT)
- **Status:** Research release
- **Typical scale:** GPU workstation to Cluster / HPC
- **Starting data:** MERRA-2 fields
- **Documentation:** https://github.com/NASA-IMPACT/Prithvi-WxC (README)
- **Source:** https://github.com/NASA-IMPACT/Prithvi-WxC (code) and https://huggingface.co/ibm-nasa-geospatial (weights)

### NVIDIA Earth-2 open models
NVIDIA's family of AI weather models, covering different parts of the forecast chain:
- **Earth-2 Medium Range (Atlas):** global medium-range ensemble forecasting at 0.25° (NVIDIA Open Model License)
- **FourCastNet 3:** global medium-range ensemble forecasting (Apache-2.0)
- **StormScope-GOES-MRMS:** kilometre-scale nowcasting of satellite and radar fields over the continental US (NVIDIA Open Model License)
- **StormScope-Meteosat:** short-range nowcasting of Meteosat Third Generation satellite imagery at 2 km and 10-minute resolution (OpenMDW-1.1)
- **StormScope-Solar (NSRDB):** estimates surface solar irradiance over the continental US from GOES satellite imagery (OpenMDW-1.1)
- **CorrDiff (COSMO-ERA5 and ERA5-HRRR variants):** generative downscaling of coarse ERA5 fields to kilometre-scale grids: a COSMO-based grid, and the HRRR grid over the continental US (OpenMDW-1.1)
- **CorrDiff CMIP6-ERA5:** downscaling of global climate model data (NVIDIA Open Model License)
- **HealDA and HealDA v2:** AI data assimilation, producing initial conditions from satellite and conventional observations (HealDA: NVIDIA Open Model License; HealDA v2: OpenMDW-1.1)

Entry details:
- **Developer:** NVIDIA — United States
- **Type:** Global forecasting, nowcasting, downscaling and data assimilation models
- **Licence:** varies by checkpoint, as listed above:
  - Apache-2.0 and OpenMDW-1.1 (the Linux Foundation's permissive licence for model weights) are Open
  - The NVIDIA Open Model License is Open, with conditions. It allows commercial use and derivative models, but rights end if you remove the model's guardrails, use must follow NVIDIA's Trustworthy AI terms, and NVIDIA can update the licence at any time
- **Status:** Active
- **Typical scale:** GPU workstation
- **Starting data:** Depends on the model: global analyses for Atlas and FourCastNet 3, satellite (and radar) data for StormScope, ERA5 or climate model fields for CorrDiff, observations for HealDA; see each model card
- **Documentation:** https://nvidia.github.io/earth2studio/ (Earth2Studio runs all of these)
- **Source:** https://huggingface.co/nvidia (model repositories: `atlas-era5`, `fourcastnet3`, `stormscope-goes-mrms`, `stormscope-meteosat`, `StormScope-NSRDB`, `corrdiff-cosmo-era5`, `corrdiff-era5-hrrr`, `corrdiff-cmip6-era5`, `healda`, `healda-v2`)
- **In this catalog:** [FourCastNet — AIWP archive](./models/nwp_models/global/usa/fourcastnet-aiwp.md) (earlier FourCastNet version)

---

## AI frameworks and toolkits

These are not models themselves. They are the tools used to run, fine-tune or train the AI models above, and the easiest way to try several models with the same input data.

### Anemoi
An open framework for building, training and running AI weather models. ECMWF uses it to train the AIFS, and several European weather services use it too (for example MET Norway for Bris and DWD for AICON). `anemoi-inference` runs published checkpoints, and the training packages let you train or fine-tune your own models.
- **Developer:** ECMWF with European national weather services — Europe
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Documentation:** https://anemoi.readthedocs.io/
- **Source:** https://github.com/ecmwf/anemoi-core and https://github.com/ecmwf/anemoi-inference

### Earth2Studio
A Python package for running AI weather models through one interface. It fetches input data from sources such as GFS, IFS and ERA5 and runs both NVIDIA's models and third-party models through the same workflow.
- **Developer:** NVIDIA — United States
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Documentation:** https://nvidia.github.io/earth2studio/
- **Source:** https://github.com/NVIDIA/earth2studio

### PhysicsNeMo
NVIDIA's framework for training and fine-tuning physics-AI models, including the Earth-2 weather models.
- **Developer:** NVIDIA — United States
- **Licence:** Open (Apache-2.0)
- **Status:** Active
- **Documentation:** https://docs.nvidia.com/physicsnemo/latest/
- **Source:** https://github.com/NVIDIA/physicsnemo
