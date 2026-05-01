# HARMONIE-AROME Caribbean (KNMI – Cy43 BES)

## What this model is
HARMONIE-AROME Caribbean is a **regional numerical weather prediction (NWP) model** operated by KNMI for the Caribbean and northern South American region. It is a configuration of the HARMONIE-AROME modelling system at Cycle 43, run on the same UWC-West supercomputer infrastructure as the European and Dutch HARMONIE products but on a separate Caribbean domain operated by KNMI for forecasting in the Dutch Caribbean (Bonaire, St. Eustatius, Saba — together "BES") and surrounding regions.

The dataset documented here — `harmonie_arome_cy43_bes` — is the **current** operational Caribbean HARMONIE distribution. It replaces the older `uwcw_extra_ha43_bess_0p05deg` (note: extra "s") dataset, which was deprecated on **27 April 2026** and will stop updating entirely on **30 June 2026**. Users still pointing at the old dataset URL or path should migrate before that date — there is no transition period after sunset.

---

## Who runs it
- **Organization:** KNMI (Royal Netherlands Meteorological Institute)
- **Model lineage:** Shares Cycle 43 modelling code with the UWC-West partnership (KNMI, Iceland, Denmark, Ireland), but the Caribbean domain itself is operated by KNMI rather than as a joint UWC-West product
- **Country / region:** Netherlands (operating); Dutch Caribbean and surrounding regions (forecast area)

---

## What area it covers
- **Coverage:**
  - Dutch Caribbean (Bonaire, St. Eustatius, Saba)
  - Windward and Leeward Islands
  - Suriname
  - Adjacent Caribbean Sea, Gulf of Mexico waters, and western tropical Atlantic
- **Domain name:** BES (Caribbean)
- **Distribution grid bounds:**
  - North: 22.45°N
  - South: 3.0°S
  - West: 79.5°W
  - East: 42.55°W

The current `bes` dataset's domain is slightly smaller in the north than the deprecated `bess` predecessor (which extended to 24.45°N). Users porting code over should check whether their area of interest still falls inside the new domain.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** HARMONIE-AROME
- **Model version:** Cycle 43 (Cy43); the current Caribbean dataset has been published since 18 June 2024 and replaced an earlier Cy40 BES product
- **Horizontal resolution:** ~0.05° (~5.5 km) on a **regular** lat-lon grid (interpolated bi-linearly from the native Lambert conformal model grid). Note that unlike the previous dataset, the new metadata does not encode the resolution in the dataset name; the 0.05° figure is consistent with the predecessor and has not changed in this transition.
- **Forecast length:** Up to ~60 hours
- **Update frequency:** Hourly (1× per hour)
- **Temporal output resolution:** 1 hour
- **Run type:** Deterministic

---

## Model characteristics
This configuration is **not convection-permitting** at its public ~5.5 km resolution — meaningfully coarser than the 2 km Dutch (P1) and European (P3) HARMONIE distributions, and reliant on parameterised deep convection rather than explicit resolution. It is appropriate for:
- tropical convection at the parameterised scale
- trade-wind regimes
- heavy-rainfall and tropical-disturbance synoptic guidance
- pre-tropical-cyclone environmental fields

For finer-scale, explicitly convection-resolving guidance over the same region, users typically combine HARMONIE-BES with downstream products or NOAA's [HiresW](../usa/hiresw-guam.md)-style or [HRRR](../usa/hrrr.md)-equivalent systems where they overlap.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0); attribution to KNMI required
- **Data formats:** GRIB1 (the deprecated `bess` predecessor was distributed in NetCDF — this is a real format change in the migration, not just a name change)
- **Data retention:** Only the most recent **72 hours** are kept on the KNMI Data Platform. There is no rolling archive. For older Caribbean HARMONIE archives, contact the KNMI licensing office at `licentiebureau@knmi.nl` — this is a paid, personalised product.
- **Dataset landing page:**
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-bes-1-0

### How to access — KNMI Open Data API

KNMI distributes its open data exclusively through the **KNMI Data Platform Open Data API**. There is no anonymous FTP or open S3 bucket for HARMONIE; an API key is always required.

**API endpoint pattern:**

```
https://api.dataplatform.knmi.nl/open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files
```

To download a specific file, list the dataset to discover filenames, then request a presigned download URL:

```
GET /open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files
GET /open-data/v1/datasets/harmonie_arome_cy43_bes/versions/1.0/files/{filename}/url
```

The API key is passed in the `Authorization` HTTP header (no `Bearer` prefix).

### API key options

There are two flavours of API key, both free:

1. **Anonymous key** — published directly in the [Open Data API documentation](https://developer.dataplatform.knmi.nl/open-data-api). Shared across all anonymous users, rate-limited globally, and reissued annually with a fixed expiry date (the current key is valid until **1 July 2026**, after which a new key is published on the same page). Suitable for one-off downloads and exploration; not recommended for unattended scripts because it will silently stop working when it expires.

2. **Registered key** — obtained by creating a free account on the [KNMI Developer Portal](https://developer.dataplatform.knmi.nl/) and clicking *Request API Key* for the Open Data API. Tied to a verified email address, does **not** expire, and provides a private quota (currently **1,000 requests per hour**, increased from 200 in late 2022). Recommended for any automated or production use.

For full-dataset bulk downloads that exceed the standard quota, KNMI also issues **bulk keys** on request (email `opendata@knmi.nl` with subject "KDP complete dataset download" and the dataset name and version, using the same email address registered on the Developer Portal).

### Update notifications
A separate **Notification Service** (also accessible via a Developer Portal API key) can push events when new files are published, removing the need for polling. Note that as of mid-2024 KNMI announced the Notification Service is being deprecated and is **not accepting new key requests**, while a replacement is being scoped — for new integrations, polling the Open Data API is currently the only supported pattern.

---

## Migration from the deprecated `bess` dataset
Anyone with existing integrations against `uwcw_extra_ha43_bess_0p05deg` should plan to migrate before **30 June 2026**. Material changes between the two:

- **Dataset name:** `uwcw_extra_ha43_bess_0p05deg` → `harmonie_arome_cy43_bes` (note the dropped trailing `s` and the dropped resolution suffix)
- **File format:** NetCDF → GRIB1 (downstream consumers expecting NetCDF will need an alternative reader path such as `cfgrib`/`pygrib` or `wgrib2`-based conversion)
- **Northern bound:** 24.45°N → 22.45°N (slightly smaller domain)
- **Variable packaging:** the deprecated dataset published 9 single-parameter files per hour; the new dataset uses a different packaging convention — verify the exact file inventory for your variables of interest using the API listing endpoint above
- **Naming convention in URLs:** the new dataset path is shorter — note KNMI's URL kebab-cases it as `harmonie-arome-cy43-bes-1-0` rather than `harmonie_arome_cy43_bes` (the underscored form is what appears in API paths and filenames; the kebab-cased form is for the human-facing dataset page)

---

## Notes
- Although the modelling code lineage is shared with UWC-West (KNMI's three partners — Iceland, Denmark, Ireland — also use Cy43), the Caribbean domain is **operated as a KNMI product**, not a UWC-West joint product like P1 (Netherlands) and P3 (Europe). The KNMI dataset abstract correspondingly refers to it as "the Caribbean KNMI HARMONIE-AROME Cy43 model" rather than the "UWC-West HARMONIE-AROME Cy43 model" used in the P1/P3 abstracts.
- The Caribbean configuration does **not** share the convection-permitting resolution of the Dutch and European HARMONIE configurations. At ~0.05° on a tropical domain, deep convection is parameterised rather than explicitly resolved.
- The grid is **regular** lat-lon (matching P1, in contrast to P3's rotated grid), so wind components are aligned with true geographic east/north and no rotation correction is needed.
- GRIB files are encoded in GRIB **edition 1**, with parameters identified by `indicatorOfParameter`. KNMI publishes a parameter code table for the BES domain (under the heading "HARMONIE Cy43 BESS") at https://english.knmidata.nl/open-data/harmonie. Note that despite the deprecated dataset name change, the parameter table reference still uses "BESS".

---

## Official documentation
- KNMI Data Platform — current dataset page:
  https://dataplatform.knmi.nl/dataset/harmonie-arome-cy43-bes-1-0
- Deprecation notice for the predecessor `bess` dataset:
  https://developer.dataplatform.knmi.nl/deprecation-uwcw_extra_ha43
- KNMI Open Data API documentation:
  https://developer.dataplatform.knmi.nl/open-data-api
- KNMI Developer Portal:
  https://developer.dataplatform.knmi.nl/
- KNMI HARMONIE overview and parameter tables:
  https://english.knmidata.nl/open-data/harmonie
