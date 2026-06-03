# REFS (RRFS Ensemble Forecast System)

## What this model is
The RRFS Ensemble Forecast System (REFS) is the **ensemble component** of NOAA's next-generation Rapid Refresh Forecast System (RRFS).

REFS is a regional, convection-allowing ensemble designed to provide probabilistic short-range forecast guidance for high-impact weather across North America. It is built on the UFS framework and is intended to replace the legacy HREF, SREF, and NARRE ensemble systems.

REFS is scheduled to become operational on **August 31, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026), subject to the standard CWD/ECE postponement contingency, alongside the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md). A pre-implementation real-time parallel feed is expected on NOMADS on or about July 7, 2026.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**  
  REFS output is provided on the same subset grids as the deterministic RRFS:
  - CONUS (3 km)
  - Alaska (3 km)
  - Hawaii (2.5 km)
  - Puerto Rico (2.5 km)

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-allowing)
- **Model system / core:** RRFS (UFS / FV3-LAM in v1)
- **Dynamical formulation:** Non-hydrostatic, finite-volume on the cubed-sphere limited-area grid (inherited from RRFS / FV3-LAM)
- **Convection-allowing:** Yes — deep convection is explicitly resolved at the 3 km CONUS/AK and 2.5 km HI/PR domains; no cumulus parameterization is used in the RRFS members. The HRRR members (contributing to the CONUS and Alaska REFS domains) are also convection-allowing at 3 km on WRF-ARW.
- **Horizontal resolution:**  
  - 3 km (CONUS, Alaska)  
  - 2.5 km (Hawaii, Puerto Rico)
- **Forecast length:** 60 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly through 60 h

---

## Data assimilation
- **Data assimilation:** REFS does not perform its own data assimilation.
- **Method:** REFS inherits the analyses of its contributing systems — primarily the hourly cycling **GSI-based hybrid 3DEnVar** analysis from the deterministic and ensemble [RRFS](../../../nwp_models/regional/usa/rrfs.md) (current and 6 h time-lagged cycles), and additionally the HRRR hourly cycling hybrid ensemble-variational analysis for the two HRRR members contributed to the CONUS and Alaska REFS domains. See the RRFS and [HRRR](../../../nwp_models/regional/usa/hrrr.md) entries for the analysis system details.

---

## Ensemble methodology

REFS is an **ensemble product generation system** that combines forecasts from the deterministic and ensemble components of RRFS, time-lagged across cycles, with HRRR as an additional contributor for two of the four domains.

For each REFS cycle, the membership is drawn from:

- **Current-cycle RRFS deterministic** forecast (run hourly; the cycle-current run feeds REFS)
- **Current-cycle RRFS ensemble members** — RRFS produces 5 ensemble members at the 00/06/12/18 UTC cycles, using different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast
- **6-hour-old RRFS deterministic** forecast (time-lagged)
- **6-hour-old RRFS ensemble members** (time-lagged)
- **For CONUS and Alaska only:** two additional HRRR members — the current cycle and a 6-hour-old cycle

The HRRR contribution to CONUS and Alaska REFS is operationally notable: HRRR is not a UFS system and is not retired in the first wave, so REFS will rely on HRRR as an explicit input during the RRFSv1 era. When HRRR is eventually retired alongside RAP under the future RRFSv2 transition, the REFS membership composition will change correspondingly.

The exact member composition and perturbation strategy are expected to evolve as RRFS transitions from v1 to v2 (MPAS-based).

---

## What it provides
Probabilistic regional forecasts of:
- Precipitation amount, type, and thresholds
- Severe convection indicators
- Near-surface temperature, wind, and humidity
- Neighborhood and probability-based guidance for high-impact weather

REFS provides the primary short-range, convection-allowing probabilistic guidance across CONUS, Alaska, Hawaii, and Puerto Rico.

### Ensemble product types
Per SCN 26-48, REFS ensemble products are organized under `refs.YYYYMMDD/CC/ensprod/` with file pattern `refs.tCCz.${type}.fFF.${dom}.grib2`, where `dom` is one of `conus`, `ak`, `hi`, `pr`. The product types are:

| Type | Description |
|---|---|
| `mean` | Simple ensemble mean |
| `sprd` | Ensemble spread |
| `pmmn` | Probability-matched mean |
| `lpmm` | Localized probability-matched mean |
| `avrg` | Combination of the pmmn and mean fields |
| `prob` | Probabilistic output |
| `eas` | Ensemble agreement scale probabilistic output |

In addition, a CONUS-only product carries flash flood and recurrence interval exceedance probabilities:

- `refs.tCCz.ffri.fFF.conus.grib2`

Full descriptions of variables and encoding are at https://www.nco.ncep.noaa.gov/pmb/products/refs.

---

## Relationship to other models
REFS is intended to fully replace the following legacy NCEP ensemble systems on August 31, 2026:
- **HREF** (High-Resolution Ensemble Forecast)
- **SREF** (Short-Range Ensemble Forecast)
- **NARRE** (North American Rapid Refresh Ensemble)

Compared to the legacy systems:
- REFS extends forecasts to **60 hours** (HREF ran to 48 hours)
- REFS provides **00, 06, 12, and 18 UTC** cycles for all regions, including non-CONUS domains (HREF only ran twice daily for AK, HI, and PR — at 06Z/18Z for AK and PR, and at 00Z/12Z for HI)
- NARRE's hourly 12-hour ensemble guidance is replaced by REFS's 60-hour forecasts updated every 6 hours
- SREF, which had already lost its largest downstream consumer when NBM v5.0 (May 2026) eliminated SREF as an input, is folded into the REFS replacement set under SCN 26-48

REFS shares similarities with HREF in product types (mean, spread, PMM, LPMM, probabilities, EAS), but differs in membership composition and ensemble design. Where HREF was a "post-processing ensemble of opportunity" combining whatever convection-allowing models happened to be operationally available, REFS is built around the deterministic and ensemble components of a single UFS-based system (RRFS) with HRRR contributing supplemental members for CONUS/AK.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS (pre-implementation parallel feed, on or about July 7, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/para/
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/refs
  - **NOMADS (post-implementation, August 31, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/prod/
  - **AWS Open Data:** https://registry.opendata.aws/noaa-rrfs/ (prototype data, including individual member output and combined ensemble products)

---

## Status
- Proposed retirement of HREF and NARRE was announced in NWS Public Information Statement 25-41 (June 26, 2025); SREF was added to the same retirement wave by SCN 26-48.
- Targeted for operational implementation alongside the deterministic RRFS, originally "early 2026"; slipped through pre-operational evaluation.
- **NWS Service Change Notice 26-48 (May 12, 2026)** scheduled REFS operational implementation for August 31, 2026 at 12 UTC, with HREF, SREF, and NARRE retiring on the same day. Per SCN 26-48, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday.
- Pre-implementation parallel data feed expected on NOMADS on or about July 7, 2026.
- 2025 NOAA Hazardous Weather Testbed Spring Forecasting Experiment evaluations indicated REFS performed competitively with HREF for Day 1 and Day 2 forecasts, and slightly better for some objective metrics including deep convection (>40 dBZ) prediction. This supported the decision to proceed with HREF→REFS replacement.

---

## Notes
- In the prototype AWS distribution, individual ensemble members are stored under directories such as `rrfs_a/rrfsens.<date>/<cycle>/m###`, while combined ensemble products are stored under `rrfs_public/refs.<date>/<cycle>/ensprod/` directories reflecting the planned operational NOMADS structure.
- As with all ensemble systems, REFS output should be interpreted probabilistically rather than as a single deterministic forecast.
- The member composition and ensemble design are expected to evolve as RRFS transitions to v2 with the MPAS dynamical core; when HRRR is eventually retired under that transition, the HRRR member contributions to the CONUS/AK REFS domains will change as well.

---

## Official documentation
- NWS Service Change Notice 26-48 (RRFS and REFS implementation, May 12, 2026):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- REFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/refs
- HREF-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
- NARRE-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/narre_replacement.txt
- NOAA RRFS/REFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
