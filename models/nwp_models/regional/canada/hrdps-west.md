# HRDPS-West (High-Resolution Deterministic Prediction System – West)

> ⚠️ **Experimental system.** HRDPS-West is distributed as experimental data through the MSC alpha datamart, not the standard operational MSC datamart. It is not a replacement for the operational [HRDPS](./hrdps.md), which continues to provide 2.5 km guidance across Canada including Southern BC.

## What this model is
HRDPS-West is an experimental kilometric-scale numerical weather prediction system operated by Environment and Climate Change Canada (ECCC), providing ~1 km horizontal resolution forecasts over Southern British Columbia.

It is built on the Global Environmental Multiscale (GEM) atmospheric model and is piloted by the operational HRDPS national system, which provides initial and boundary conditions for the atmospheric fields including hydrometeors. Surface initial conditions come from the 2.5 km Canadian Land Data Assimilation System (CaLDAS), which is coupled to the National HRDPS. HRDPS-West provides numerical weather forecasting guidance to the Pacific Storm Prediction Centre (PSPC), the Prairie and Arctic Storm Prediction Centre (PASPC), and the Canadian Meteorological Aviation Centre West (CMAC-West).

The current experimental version is HRDPS-West 1.5.0.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Southern British Columbia (with the domain extending into adjacent regions of Alberta, Washington State, and the northeast Pacific)
- **Grid:** Latitude–longitude grid, 1350 × 1200 points at uniform 0.00899° (~1 km) resolution

---

## Basic details
- **Model type:** Experimental deterministic NWP (convection-permitting)
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2.0
- **Dynamical formulation:** Non-hydrostatic primitive equations
- **Convection-allowing:** Yes (~1 km horizontal resolution)
- **Horizontal resolution:** ~1 km (0.00899°)
- **Vertical levels:** 62 staggered hybrid levels with SLEVE coordinate (Husain et al., 2020). Lowest momentum level at ~40 m
- **Model top:** 10 hPa, with a 4-level sponge layer at the model lid
- **Forecast length:** Up to 48 hours
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Time step:** 30 seconds
- **Time integration:** Implicit, semi-Lagrangian (3D), 2 time-level
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** No — HRDPS-West is a pure dynamical downscaling system. It does not run its own atmospheric analysis. Initial atmospheric fields (including hydrometeors) come directly from the National HRDPS, which performs its own continuous-cycle 4DEnVar assimilation.

---

## Initial and boundary conditions
- **Atmospheric initial conditions (including hydrometeors):** From the National HRDPS
- **Lateral boundary conditions:** Provided hourly by an early forecast from the operational National HRDPS at 2.5 km resolution, including hydrometeor fields
- **Surface initial conditions:** From the 2.5 km Canadian Land Data Assimilation System (CaLDAS), which is coupled to the National HRDPS
- **Sea ice thickness and sea ice temperature:** Initialized from the 0-h prog of the National HRDPS
- **Sea ice cover and sea-surface temperature:** Specified from the 0-h prog of the National HRDPS, which itself uses data from the GDPS analysis (G6 component)

> Note on pilot version: the HRDPS-West 1.5.0 technical specification document names HRDPS national **v6.1.0** as the pilot (the document also says "based on GEM version 5.2.0," which is the GEM version introduced in HRDPS national v7.0.0 — suggesting the pilot description in the spec may not have been fully updated for the June 11, 2024 IC-4 release). Operationally, since June 11, 2024 the pilot has been HRDPS national v7.0.0, which runs concurrently.

---

## What it provides
Experimental high-resolution deterministic forecasts of:
- Near-surface temperature, wind, and humidity
- Precipitation, with diagnostic precipitation types from Bourgouin (2000)
- Cloud condensate, cloud amount, boundary layer height
- Mean sea level pressure, relative humidity, QPF, precipitation rate, omega
- Localized hazardous weather over complex terrain
- Boundary-layer and topographically-driven effects (relevant for BC's mountain ranges, fjords, and coastal interfaces)

The 1 km resolution is intended to better resolve mesoscale features in Southern BC's complex topography that the operational 2.5 km HRDPS cannot fully represent.

HRDPS-West provides forecasting guidance to:
- Pacific Storm Prediction Centre (PSPC)
- Prairie and Arctic Storm Prediction Centre (PASPC)
- Canadian Meteorological Aviation Centre West (CMAC-West)

---

## Surface and physics
- **Surface scheme:** Mosaic approach with 4 surface types — land, water, sea ice, and glacier. ISBA (Bélair et al., 2003a, 2003b) for land; TEB (Masson, 2000) for urban areas of the pan-Canadian domain
- **Subgrid-scale orography:** No orographic gravity wave drag, non-orographic gravity wave drag, or low-level blocking schemes — appropriate for the convection-permitting scale where these effects are largely resolved
- **Surface roughness over water:** Charnock formulation for momentum; Deacu formulation for thermal roughness
- **Radiation:** Li–Barker correlated-k distribution (called every 450 seconds)
- **Boundary-layer mixing:** TKE-based (Benoît et al., 1989; Delage, 1988a, 1988b) with statistical subgrid-scale clouds (Mailhot & Bélair, 2002; Bélair et al., 2005); Blackadar mixing length; Richardson number hysteresis (McTaggart-Cowan & Zadra, 2015)
- **Stable precipitation:** Predicted Particle Properties (P3) bulk microphysics (Morrison & Milbrandt, 2015; Milbrandt & Morrison, 2016), with diagnostic precipitation types from Bourgouin (2000)
- **Shallow convection:** Kuo Transient scheme (Bélair et al., 2005)
- **Deep convection:** Not used (N/A) — the resolution is fine enough that deep convection is treated as resolved

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC alpha datamart)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Distribution channel:** MSC alpha datamart (separate from the standard operational MSC datamart used by HRDPS, RDPS, GDPS, etc.)
- **Official download location (alpha/experimental):** https://dd.alpha.weather.gc.ca/model_hrdps/west/1km/grib2/
- **Documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart-alpha_en/
- **Changelog:** https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/changelog_hrdps-west_en/

---

## Notes
- HRDPS-West is convection-permitting at 1 km resolution and does not use a deep convection parameterization.
- The Southern BC domain reflects the operational priorities of the region: complex terrain, atmospheric river events, mountain meteorology, and dense population in the Lower Mainland and southern Vancouver Island.
- HRDPS-West is a pure dynamical downscaling system — it does not perform its own data assimilation. All atmospheric initial fields come from the National HRDPS, which means HRDPS-West's analysis quality is bounded by the National HRDPS's analysis quality. The value-add is purely from higher resolution, the SLEVE coordinate over complex terrain, and the 30-second time step.
- As an experimental system, configuration, domain, resolution, and product availability may change without the formal change-management process used for operational ECCC systems.
- The "alpha" designation in the datamart URL indicates the distribution channel itself is experimental, not just the model — users building automated pipelines should be aware that alpha endpoints may change without notice.
- HRDPS-West is the convection-permitting western counterpart to the pan-Canadian operational [HRDPS](./hrdps.md). Whether HRDPS-West eventually becomes operational, expands to additional regional domains (e.g., a hypothetical HRDPS-East), or is folded into a future revision of HRDPS itself is not publicly documented.

---

## Status
- HRDPS-West is distributed as **experimental data** via the MSC alpha datamart.
- It is not operationally supported in the same sense as HRDPS, GDPS, RDPS, or other operational ECCC prediction systems.
- Users requiring operational guidance over Southern BC should continue to use the operational [HRDPS](./hrdps.md) at 2.5 km resolution.
- HRDPS-West is one of several experimental ECCC systems alongside [GDPS-SN](../../global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn) — see the repository's [`STATUS.md`](../../../STATUS.md) for the current list.

---

## Version history

### HRDPS-West 1.5.0 — implemented June 11, 2024 (current)
The HRDPS-West 1.5.0 technical specification document names June 11, 2024 as the implementation date at CMC operations — coincident with the IC-4 release of GDPS 9.0.0, RDPS 9.0.0, and HRDPS national 7.0.0. The MSC alpha datamart distribution was announced separately on November 6, 2024.

Headline changes from earlier versions:
- **GEM upgraded to version 5.2.0**
- **SLEVE vertical coordinate** introduced (Husain et al., 2020) — particularly relevant for HRDPS-West given the mountainous terrain across Southern BC
- **Bourgouin (2000) approach** adopted for surface precipitation phase partitioning, with diagnostic precipitation types
- **Pilot system** aligned with the IC-4 HRDPS national release
- **Updated geophysical fields** for better representation of surface features and properties
- **Sea/lake ice** initialization and processing changes (now sourced from the 0-h prog of National HRDPS for thickness/temperature, and from the GDPS G6 analysis via National HRDPS for cover/SST)

### Earlier versions
- HRDPS-West has been running 2× daily at 00 and 12 UTC, integrated for 48 hours, since the original implementation of HRDPS-West v1.3.0.

---

## Official documentation
- HRDPS-West alpha datamart README: https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart-alpha_en/
- HRDPS-West changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/changelog_hrdps-west_en/
- Technical specifications (HRDPS-West 1.5.0): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_HRDPS-WEST_1_5_0_e.pdf
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Bélair, S., L.-P. Crevier, J. Mailhot, B. Bilodeau, and Y. Delage (2003a). Operational implementation of the ISBA land surface scheme in the Canadian regional weather forecast model. Part I: Warm season results. *J. Hydromet.*, 4, 352–370.
- Bourgouin, P. (2000). A method to determine precipitation types. *Wea. Forecasting*, 15, 583–592.
- Côté, J., et al. (1998). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Husain, S. Z., C. Girard, L. Separovic, A. Plante, and S. Corvec (2020). On the Progressive Attenuation of Finescale Orography Contributions to the Vertical Coordinate Surfaces within a Terrain-Following Coordinate System. *Mon. Wea. Rev.*, 148, 4143–4158.
- Masson, V. (2000). A physically-based scheme for the urban energy budget in atmospheric models. *Bound.-Layer Meteor.*, 94, 357–397.
- Morrison, H. and J. A. Milbrandt (2015). Parameterization of cloud microphysics based on the prediction of bulk ice particle properties. Part I: Scheme description and idealized tests. *J. Atmos. Sci.*, 72, 287–311.
