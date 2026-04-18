# NAM (North American Mesoscale)

> ⚠️ **Scheduled for retirement.** The NAM is proposed for discontinuation and replacement by the [RRFS](./rrfs.md) per [NWS Public Information Statement 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025). Both the 12 km parent domain and all 3 km nests (CONUS, Alaska, Hawaii, Puerto Rico, fire weather) are included in the retirement.

## What this model is
A regional weather forecast model that predicts weather conditions such as temperature, wind, precipitation, and pressure over North America.

It is designed for short- to medium-range forecasting at higher resolution than global models.

---

## Who runs it
- **Organization:** : NOAA / National Weather Service
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America

---

## Basic details
- **Model type:** Regional
- **Typical resolution:** ~12 km
- **Forecast length:** Up to 84 hours
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
The NAM is commonly used for regional forecasting and as a bridge between global and very high-resolution models.

Lists of retired and retained NAM output grids and products are documented by NCEP EMC:
- NAM grids being retired: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retiredgrids.txt
- NAM products being discontinued: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retirements.txt

---

## Status
- Proposed for full retirement in NWS Public Information Statement 25-41 (June 26, 2025).
- Retirement covers the 12 km parent domain, all 3 km nests (CONUS, AK, HI, PR), and the fire-weather nest.
- Originally targeted for retirement in early 2026 alongside RRFSv1 operational implementation; timeline has slipped and retirement is expected to follow RRFSv1 going fully operational.
- The NAM has been frozen since its last major upgrade in 2017 and has not been actively developed since.
- Some NAM output grids will continue to be produced from RRFS output; others will be fully discontinued.

## Official documentation
- https://www.weather.gov/sti/stimodeling_nam
