# JMA GSM (Global Spectral Model)

## What this model is
The Global Spectral Model (GSM) is the operational global deterministic numerical weather prediction system run by the Japan Meteorological Agency (JMA).

It is JMA's primary medium-range global model, used for tropical cyclone information, daily forecasts, and one-week forecasts out to 11 days. GSM also provides initial and boundary conditions for several of JMA's downstream systems, including the Meso-Scale Model (MSM) over Japan and the Global Ensemble Prediction System (GEPS).

---

## Who runs it
- **Organization:** Japan Meteorological Agency (JMA)
- **Country / region:** Japan

---

## What area it covers
- **Coverage:** Global
- **Domain details:**  
  Public distribution is provided on regular latitude–longitude grids:
  - Global: 90°S–90°N, full longitude
  - WMO Region II subset: 5°S–90°N, 30°E–165°W (i.e. 30°E through 180° to 195°E)

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** GSM (spectral, hydrostatic)
- **Native horizontal resolution:** ~13 km (TQ959) — upgraded from ~20 km in 2023
- **Public output grids:**  
  - 0.25° latitude–longitude (1440 × 721 global; 661 × 381 RA-II)  
  - 0.5° latitude–longitude (720 × 361 global; 331 × 191 RA-II)
- **Vertical levels:** 128
- **Model top:** 0.01 hPa
- **Forecast length:**  
  - 264 hours (11 days) for 00 and 12 UTC cycles  
  - 132 hours (5.5 days) for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Initial conditions:** Hybrid 4D-Var analysis (uses LETKF ensemble for background error covariances)

---

## What it provides

### Surface fields (11 elements)
U and V wind components, temperature, relative humidity, surface pressure, mean sea level pressure, total precipitation, and total / upper / middle / lower cloud cover.

### Pressure levels (22 levels: 1000, 975, 950, 925, 900, 850, 800, 700, 600, 500, 400, 300, 250, 200, 150, 100, 70, 50, 30, 20, 10 hPa)
U and V wind components, temperature, relative humidity, geopotential height, and vertical velocity at every level. The 850 hPa and 200 hPa levels additionally include stream function and velocity potential. The 500 hPa level additionally includes relative vorticity.

Surface topography (geopotential of the model surface) is also distributed as a separate file for both the global and RA-II domains at 0.25° resolution.

---

## Data availability
- **Is the data free?** Yes — but registration is required for subscription/automated access
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2

GSM data is distributed through JMA's **GISC Tokyo / WIS-JMA** service. There are two access paths, both reaching the same underlying directory tree:

### 1. Interactive download (no registration)
The product viewer at https://www.wis-jma.go.jp/cms/gsm/download.html allows files to be browsed and downloaded individually through the browser.

### 2. Direct directory access and subscription (registration required)
The data tree can also be accessed directly at:

```
https://www.wis-jma.go.jp/d/c/RJTD/GRIB/Global_Spectral_Model/Latitude_Longitude/
```

The directory hierarchy is:
```
{grid_interval}/{region_bounds}/{layer}/{YYYYMMDD}/{HH0000}/{filename}
```

For example, the global 0.5° surface fields for the 12 UTC cycle on 1 January 2026 would be at:
```
0.5_0.5/90.0_-90.0_0.0_359.5/Surface_layers/20260101/120000/
```

with filenames following the pattern `GSM_GPV_Rgl_Gll0p5deg_Lsurf_FDXXxx_grib2.bin` (where `Rgl` denotes global vs. `Rra2` for RA-II, and `FDXXxx` encodes the forecast lead time as days and hours).

Programmatic and bulk access is intended to be done through the **GISC Tokyo subscription service** at https://www.wis-jma.go.jp/data/subscribe, which generates a sample shell script (`.sh` for UNIX, `.ps1` for Windows) that automatically downloads new GSM files as they are published. Users select grid resolution, area, and levels through the form before generating the script. See pages 29–38 of the [Users' Guide](https://www.wis-jma.go.jp/cms/file/Introduction_to_DAR_GISC_Tokyo_Rev04.pdf) for setup details.

To register and obtain access credentials, users must contact the Administrator of GISC Tokyo:

**wis-jma@met.kishou.go.jp**

Full instructions are documented in the [WIS-JMA tutorial](https://www.wis-jma.go.jp/cms/gsm/tutorial.html).

---

## Update schedule

GSM products are published on the GISC Tokyo server several hours after each initialisation:

| Initial time (UTC) | Forecast range covered | Publication time (UTC) |
|---|---|---|
| 00 | 0–132 h | ~04 |
| 00 | 138–264 h | ~07 |
| 06 | 0–132 h | ~10 |
| 12 | 0–132 h | ~16 |
| 12 | 138–264 h | ~19 |
| 18 | 0–132 h | ~22 |

The split publication for 00 and 12 UTC reflects the two-stage release of the early portion of the forecast first, followed by the extended range a few hours later.

---

## Notes
- The 11-day forecast range applies only to the 00 and 12 UTC cycles. The 06 and 18 UTC cycles run to 5.5 days. This was extended from 7.5 days (132 h) at 00 UTC to 11 days in 2020.
- The vertical layer count and model top were upgraded from 100 levels / 0.1 hPa top to 128 levels / 0.01 hPa top in 2021.
- The native resolution was upgraded from ~20 km to ~13 km (TQ959) in 2023.
- GSM is the deterministic counterpart to JMA's Global Ensemble Prediction System (GEPS).
- JMA also distributes a separate operational ensemble system (GEPS) and regional models (MSM, LFM) which are not part of this entry.

---

## Official documentation
- WIS-JMA GSM service home: https://www.wis-jma.go.jp/cms/gsm/
- Tutorial (interactive, direct, and subscription access): https://www.wis-jma.go.jp/cms/gsm/tutorial.html
- Product information (grids, levels, file naming, update schedule): https://www.wis-jma.go.jp/cms/gsm/product_information.html
- JMA NWP activities (model specifications and operational history): https://www.jma.go.jp/jma/en/Activities/nwp.html
- GISC Tokyo subscription portal: https://www.wis-jma.go.jp/data/subscribe
- Users' Guide (PDF): https://www.wis-jma.go.jp/cms/file/Introduction_to_DAR_GISC_Tokyo_Rev04.pdf
