# NAM Nest (North American Mesoscale – High Resolution)

> ⚠️ **Scheduled for retirement.** The NAM Nest is proposed for discontinuation and replacement by the [RRFS](./rrfs.md) per [NWS Public Information Statement 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025). All 3 km nested domains (CONUS, Alaska, Hawaii, Puerto Rico) are included in the retirement.

## What this model is
A high-resolution regional weather forecast model that predicts weather conditions such as temperature, wind, precipitation, and pressure over selected parts of the United States.

It is a nested, higher-resolution configuration of the NAM system, designed to better represent thunderstorms and other small-scale weather features.

---

## Who runs it
- **Organization:** : NOAA / National Weather Service
- **Country / region:** United States

---

## What area it covers
- **Coverage:** United States (CONUS, Alaska, Hawaii, Puerto Rico)

---

## Basic details
- **Model type:** Regional
- **Typical resolution:** 3 km
- **Forecast length:** Up to ~60 hours
- **Update frequency:** 4× daily

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://www.ncei.noaa.gov/products/weather-climate-models/north-american-mesoscale

---

## Notes
The NAM Nest replaced the older ~5 km NAM nest and provides convection-allowing forecasts.  
It updates less frequently than HRRR but typically runs farther into the future.

Lists of retained NAM Nest output grids that will continue to be produced from RRFS output:
- https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/namnest_grids.txt

---

## Status
- Proposed for full retirement in NWS Public Information Statement 25-41 (June 26, 2025), as part of the broader NAM system retirement.
- All 3 km nested domains (CONUS, AK, HI, PR) are included.
- Replacement deterministic guidance at convection-allowing scales is provided by RRFS (3 km CONUS/AK, 2.5 km HI/PR).
- Some NAM Nest output grids will continue to be produced from RRFS output via post-processing; others will be fully discontinued.
- Originally targeted for retirement in early 2026 alongside RRFSv1 operational implementation; timeline has slipped.

## Official documentation
- https://www.weather.gov/sti/stimodeling_nam
