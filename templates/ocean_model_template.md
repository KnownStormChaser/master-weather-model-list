# [Model Name] ([System Name or Acronym])

## What this model is
[1–3 paragraphs describing what the model is, what makes it distinct, and what its operational role is. State up front whether it is global or regional, deterministic or ensemble, physics-based or AI-based, and what its primary use case is — standalone forecasts, boundary conditions for downstream regional systems, both, etc. If the system has a commonly-used short name (e.g., GLO12, RTOFS), introduce it here.]

[If the product has a formal Copernicus Marine product identifier, NOAA designation, or similar, state it explicitly here. If the system has multiple naming conventions in active use across documentation, list them.]

---

## Who runs it
- **Production Unit:** [Operating institute]
- **Country:** [Country or "Intergovernmental"]
- **Programme or coordinating body:** [e.g., Copernicus Marine MFC, NOAA NCEP, US Navy FNMOC, BLUElink]
- **Role in any larger system:** [If the model serves as boundary conditions for downstream regional models, or is part of a coupled prediction system, note it here]

---

## What area it covers
- **Coverage:** [Global, regional name, or basin-specific]
- **Domain bounds:** [Lat/lon bounds, or "global"]
- **Grid dimensions:** [If known]
- **Special masked or excluded regions:** [e.g., specific seas masked, ice-covered regions handled separately]

---

## Basic details
- **Model type:** [Global/regional ocean physics; deterministic or ensemble; coupled or standalone]
- **Core ocean model:** [NEMO, HYCOM, MOM6, ROMS, FESOM, etc., with version]
- **Sea ice model:** [CICE, LIM, SI3, none, etc., with version — only if applicable]
- **System name:** [Operational system identifier]
- **Horizontal resolution:** [Native and delivered, if different]
- **Vertical levels:** [Number and depth range; note coordinate system — z-level, sigma, hybrid, etc.]
- **Vertical coordinate:** [Z-level / sigma / hybrid / isopycnal]
- **Forecast length:** [Days, hours]
- **Update frequency:** [Daily, twice daily, etc.]
- **Production cycles:** [Cycle times in UTC]
- **Target delivery time:** [When the product is typically available]
- **Temporal output resolution:** [Hourly, 3-hourly, daily mean, etc.]
- **Archive availability:** [Rolling window length, if any]
- **Bathymetry source:** [GEBCO, ETOPO, EMODnet, etc.]

---

## Forcing
- **Atmospheric forcing:** [Source model, resolution, frequency — e.g., ECMWF IFS at 0.1°, 3-hourly]
- **River runoff:** [Climatology, dynamic, or none]
- **Lateral boundary conditions:** [For regional models — parent global system, frequency]
- **Tidal forcing:** [If applicable — explicit tides, parameterized, or none]
- **Ice forcing or coupling:** [If applicable]
- **Initial conditions:** [How the model is initialized at each cycle]

---

## Coupling
[Describe what the model is coupled to and the nature of the coupling. Common cases:]
- **Standalone ocean physics:** No coupling; receives atmospheric forcing as one-way fluxes.
- **One-way coupled to waves:** Receives Stokes drift / wave-modified stress as one-way input.
- **Two-way coupled to waves:** Bidirectional exchange via OASIS3-MCT or similar coupler.
- **Coupled to sea ice:** Online or offline; describe coupling frequency.
- **Coupled to atmosphere:** For seamless prediction systems.

[For each coupled component, list: which fields are exchanged, in which directions, at what frequency, via which coupler.]

---

## Data assimilation
- **DA scheme:** [3DVar, 4DVar, EnKF, SEEK filter, OI, hybrid, none]
- **Update cycle:** [Frequency and window length]
- **Increment application:** [IAU, direct, FGAT, etc.]

### Assimilated observations
- **Sea surface height (altimetry):** [Missions: Jason-3, Sentinel-3A/B, Sentinel-6A, CryoSat-2, SWOT, HY-2B, etc.]
- **Sea surface temperature:** [Sources: AVHRR, VIIRS, AMSR2, in-situ blends like OSTIA or NOAA OISST]
- **In-situ temperature and salinity profiles:** [Argo, XBT, CTD, gliders, moored arrays]
- **Sea ice concentration:** [Source — e.g., OSI SAF, NSIDC]
- **Sea ice thickness:** [If applicable — CryoSat-2, SMOS]
- **Sea surface salinity:** [If applicable — SMOS, SMAP, Aquarius]
- **Other:** [HF radar surface currents, drifters, etc.]

[Note: Some operational ocean systems use a frozen analysis from a partner organization rather than running their own DA — for example, RTOFS historically used NAVOCEANO's analysis. Document this explicitly if applicable.]

---

## What it provides
[Group variables by category. Ocean physics products typically deliver more variable categories than wave or atmospheric products.]

### 3D ocean fields
- Temperature
- Salinity
- Zonal velocity (eastward current component)
- Meridional velocity (northward current component)
- Vertical velocity (if provided)
- Mixed layer depth

### Surface fields
- Sea surface height (above geoid, above mean sea level — clarify reference)
- Sea surface temperature
- Sea surface salinity
- Surface currents
- Surface stress (if provided)

### Sea ice fields (if applicable)
- Sea ice concentration
- Sea ice thickness
- Sea ice velocity
- Sea ice age (if provided)
- Snow thickness on sea ice

### Special derived products (if applicable)
[Some systems deliver merged products like SMOC (Surface Merged Ocean Current) that combine Eulerian currents with Stokes drift and tidal drift. Document any such derived datasets here.]

### Static fields
- Bathymetry
- Land-sea mask
- Grid cell dimensions

---

## Data availability
- **Is the data free?** [Yes / Yes with registration / Conditional]
- **Is the data downloadable?** [Yes / Yes via specific channels]
- **Data formats:** [NetCDF-4, GRIB2, etc., and CF conventions if relevant]
- **Product identifier:** [Formal product ID if applicable]
- **Dataset identifiers:** [List individual datasets — e.g., 3D fields, surface fields, static]
- **File naming:** [Pattern with placeholders for date/cycle]
- **File size:** [Approximate per-file size]
- **Official access:** [URL]
- **DOI:** [If available]
- **Delivery mechanism:** [Toolbox, NOMADS, AWS Open Data, FTP, HTTPS, etc.]
- **Licence:** [Specific licence — Copernicus Marine, NOAA public domain, CC-BY, etc.]

---

## Version history

[Document major operational changes. Ocean physics systems often have important upgrades to model resolution, vertical levels, DA scheme, and assimilated observations. Match the level of detail you've used in the wave entries.]

### [Most recent version date — Issue X.Y]
- [Change description]

### [Previous version date]
- [Change description]

[Continue chronologically backward as far as documentation allows.]

---

## Relationship to other ocean products

### Companion products from same operator
[List sister products from the same Production Unit — e.g., a wave product, biogeochemistry product, regional downscaling, or reanalysis counterpart. Cross-link to existing repository entries.]

### Peer global ocean physics systems
[For global systems, list peer operational systems with similar capability. Examples: GLO12 ↔ Global RTOFS ↔ Navy Global HYCOM ↔ BLUElink OceanMAPS. For regional systems, list global parents and overlapping regional systems.]

### Regional models nested inside this system
[If this is a global system that provides boundary conditions for regional downscalings, list them. If this is a regional system, identify its parent global product.]

### AI-based counterparts
[If applicable — many physics-based ocean systems now have neural-network counterparts trained on their reanalyses (e.g., GLONET trained on GLORYS12). Cross-reference.]

---

## Notes
[Use this section for operationally meaningful details that don't fit elsewhere:]
- [Wetting-and-drying treatment in shallow areas]
- [Known biases or model limitations documented in the QUID or equivalent]
- [Vertical coordinate quirks affecting interpretation]
- [Reference frame for sea level — geoid, mean sea surface, equilibrium tide]
- [Timezone or time-stamping conventions]
- [Whether currents include or exclude tidal components]
- [Whether currents are Eulerian-only or include Stokes drift]
- [Operational use cases cited by the operator]

---

## Official documentation
- Product page: [URL]
- Product User Manual (PUM) or equivalent: [URL]
- Quality Information Document (QUID) or equivalent validation report: [URL]
- DOI: [URL]
- Operating institute: [URL]
- Programme: [URL]

### Key references
- [Author et al. (Year). Title. Journal, vol(issue), pages. DOI]
- [Continue chronologically or by topic]
