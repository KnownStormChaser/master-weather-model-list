# SWAN-EST (Estonian Nearshore Wave Model)

## What this model is
SWAN-EST is a regional wave forecasting system operated by the Estonian Meteorological and Hydrological Institute, based on the SWAN (Simulating WAves Nearshore) model.

SWAN is a third-generation spectral wave model that allows the calculation of wind wave parameters in shallow coastal waters and inland waters. The Environmental Agency uses the SWAN-EST configuration adapted specifically for Estonian and Baltic Sea conditions.

The model is based on the two-dimensional spectral wave action balance equation and is designed to represent nearshore wave processes with high physical realism.

---

## Who runs it
- **Organization:** Estonian Meteorological and Hydrological Institute
- **Country / region:** Estonia

---

## What area it covers
- **Coverage:** Baltic Sea
- **Primary focus:** Estonian coastal and nearshore waters

---

## Basic details
- **Model type:** Regional deterministic wave model
- **Model system:** SWAN (Simulating WAves Nearshore)
- **Horizontal resolution:**  
  Regional coastal-scale grid (resolution varies by sub-region)
- **Forecast length:**  
  Short-range forecasts (typically several days)
- **Update frequency / cycles:**  
  2× daily (00, 12 UTC)
- **Temporal output resolution:**  
  Model-dependent (typically hourly)

---

## Physical processes represented
The SWAN-EST configuration accounts for bottom friction and represents the following physical processes:

- Generation of waves by wind
- Propagation of the wave field in time and space
- Changes in the wave profile due to variations in water depth (shoaling)
- Energy exchange between waves through quadruple and triple wave–wave interactions
- Refraction of waves due to decreasing water depth
- Refraction due to seabed topography and currents
- Partial dissipation of wave energy in deep water due to whitecapping

---

## What it provides
SWAN-EST produces both spectral and integrated wave parameters, including:
- Two-dimensional wave spectrum
- Significant wave height
- Mean and maximum wave period
- Mean wave propagation direction
- Root-mean-square velocity of near-bottom orbital water particle motion

These products are optimized for coastal forecasting, marine safety, and environmental applications.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download location:**  
  https://avaandmed.keskkonnaportaal.ee/dhs/Active/documentList.aspx?ViewId=319c5400-af7c-43cc-ba6e-94428d1dbc2a

---

## Notes
- SWAN-EST focuses on coastal and nearshore wave dynamics rather than deep-ocean wave forecasting.
- Model configuration and spatial resolution may vary across different Baltic Sea sub-domains.
- Products are intended for marine forecasting, coastal management, and environmental monitoring.

---

## Official documentation
- Keskkonnaportaal: *Ilma mudeliandmete kirjeldus* (Weather model data description)
