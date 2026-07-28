# CWA OCM (Taiwan Operational Ocean Current Model)

## What this model is
OCM is the Central Weather Administration's operational ocean forecast system for
the seas around Taiwan. It produces hourly forecasts of sea surface temperature,
surface currents, sea surface height, and salinity out to 96 hours on a 0.1°
regional grid spanning the Taiwan Strait, the Bashi Channel, the northern South
China Sea, the East China Sea shelf, and the Kuroshio as it passes east of Taiwan.

CWA distributes it under the open-data product code **M-B0071**. The metadata
sidecar describes the system as 三維海流作業化模式 — a *three-dimensional*
operational ocean current model — but the publicly distributed NetCDF contains
**surface fields only**, with no depth dimension. See *Notes*.

The system is referred to only as "OCM" in the distributed metadata (the `Model`
field of the sidecar); CWA does not publish a longer system name, an English name,
or a product reference document for it.

---

## Who runs it
- **Production Unit:** Central Weather Administration (交通部中央氣象署)
- **Country:** Taiwan
- **Programme or coordinating body:** Taiwan national open data programme
  (data.gov.tw); distributed via the AWS Open Data Registry
- **Role in any larger system:** TBD — no documented downstream coupling. CWA's
  marine point-forecast products (`F-A0037`, `M-B0078`) publish current speed and
  direction at coastal stations and may be derived from this system, but the
  linkage is not stated.

---

## What area it covers
- **Coverage:** Regional — seas surrounding Taiwan
- **Domain bounds:** 110.0°E – 126.0°E, 7.0°N – 36.0°N (live-verified from the
  NetCDF coordinate variables; matches the sidecar `StartPoint`/`EndPoint` fields)
- **Grid dimensions:** 161 (lon) × 291 (lat), regular latitude–longitude
  - Grid points increase **west to east** and **south to north**. The sidecar
    `GridDimensionX = 161` / `GridDimensionY = 291` maps correctly onto the
    NetCDF `lon` / `lat` dimensions — no transposition, unlike some other
    CWA products.
- **Special masked or excluded regions:** Land is masked with IEEE NaN.
  **60.8% of grid cells are ocean**; the mask is identical across all seven
  variables and all 97 timesteps.

---

## Basic details
- **Model type:** Regional ocean physics, deterministic
- **Core ocean model:** **TBD** — not documented. CWA publishes no product
  reference for M-B0071 and the NetCDF carries no global attributes identifying
  the core.
- **Sea ice model:** Not applicable (subtropical domain)
- **System name:** OCM (as given in the sidecar `Model` field)
- **Horizontal resolution:** 0.1° × 0.1° (~11 km)
- **Vertical levels:** **TBD.** The distributed product is surface-only; the
  number and spacing of the model's internal levels is not documented.
- **Vertical coordinate:** TBD
- **Forecast length:** **96 hours (4 days)** — 97 hourly timesteps, T+0 to T+96
- **Update frequency:** Once daily (inferred — see *Notes*)
- **Production cycles:** 00 UTC. The NetCDF `time` axis is explicitly stamped
  `hours since {YYYY-MM-DD} 00:00:00 UTC`, and the sidecar `InitialTime` is
  `{YYYYMMDD}0000`.
- **Target delivery time:** ~04:40 UTC (T+4 h 40 m). The S3 object
  `Last-Modified` for the 2026-07-28 00 UTC run was 04:42:30 UTC; the sidecar
  `sent` timestamp was 12:30:24 +08:00 (= 04:30 UTC).
- **Temporal output resolution:** Hourly
- **Archive availability:** **None.** The bucket is latest-only — a single fixed
  key overwritten in place each cycle. No date partitioning, no rolling window.
  Users needing a time series must poll and retain their own copies.
- **Bathymetry source:** TBD

---

## Forcing
- **Atmospheric forcing:** TBD. CWA operates WRF at 15 km and 3 km over an
  overlapping domain (see [CWA Taiwan Regional WRF](../../../nwp_models/regional/taiwan/cwa-regional.md)),
  which is the plausible driver, but this is **not documented** — do not assume.
- **River runoff:** TBD. Minimum salinity in the distributed field reaches
  **11.4** (practical salinity), which is far fresher than open-ocean values and
  implies some freshwater source is represented — most likely the Yangtze and
  Pearl River plumes. Whether this is dynamic runoff or climatology is unknown.
- **Lateral boundary conditions:** TBD — parent global or basin-scale system not
  documented.
- **Tidal forcing:** **Yes — explicit tides are present.** Live-verified: SSH at
  mid-Taiwan Strait (24.5°N, 119.5°E) has a dominant period of **12.12 h** and a
  **4.39 m** range over the forecast, with two clear highs and lows per day.
  Currents show tidal reversal (Penghu Channel `UC` ranges −0.74 to +0.58 m/s).
  Semi-diurnal signal also dominates at the East China Sea shelf and east of
  Taiwan; the deep South China Sea and Penghu Channel points show a diurnal
  (~24.25 h) dominant period, consistent with the region's mixed tidal regime.
- **Ice forcing or coupling:** Not applicable
- **Initial conditions:** TBD

---

## Coupling
TBD. No coupling is documented. The presence of SST, salinity, sea level, and
currents in a single product is consistent with a standalone ocean physics model
receiving one-way atmospheric forcing, but this is not stated by the operator.

---

## Data assimilation
- **DA scheme:** TBD
- **Update cycle:** TBD
- **Increment application:** TBD

### Assimilated observations
TBD — no assimilation documentation is published for this product.

---

## What it provides

All fields are **two-dimensional surface fields** on the `(time, lat, lon)` grid.
Seven variables, 97 hourly timesteps each.

### 3D ocean fields
None distributed. Despite the "three-dimensional" description in the sidecar, the
public NetCDF has no depth dimension.

### Surface fields
| Variable | Description | Inferred unit |
|---|---|---|
| `SST` | Sea surface temperature | °C |
| `UC` | Zonal (eastward) surface current component | m s⁻¹ |
| `VC` | Meridional (northward) surface current component | m s⁻¹ |
| `SPD` | Current speed — exactly `sqrt(UC² + VC²)` | m s⁻¹ |
| `DIR` | Current direction, 0–360° | degrees |
| `SSH` | Sea surface height | m |
| `SAL` | Salinity | practical salinity (PSU) |

**Units are inferred from value ranges, not declared.** No variable carries a
`units` attribute except `time`. Observed ranges over a full 96 h forecast
(2026-07-28 00 UTC): SST 19.36–32.46; UC −1.81–2.27; VC −1.74–2.22;
SPD 0.00–2.60; DIR 0.00–360.00; SSH −2.66–3.58; SAL 11.42–34.70.

- **`DIR` convention — live-verified:** `DIR` equals `atan2(UC, VC) mod 360`
  exactly, i.e. the direction the current is flowing **toward**, in degrees
  clockwise from true north (oceanographic convention, *not* the meteorological
  "direction from"). The variable's `long_name` is a leftover GrADS expression
  (`IF DIR0 GT 360 THEN DIR0-360 ELSE DIR0`) and carries no convention
  information.
- **`SPD` is fully redundant** — agrees with `sqrt(UC² + VC²)` to within
  1.6 × 10⁻⁷. It can be ignored or recomputed.
- **Currents are total (tidal + subtidal)** and, as far as can be determined,
  Eulerian only — no Stokes drift component is indicated.

### Sea ice fields
Not applicable.

### Special derived products
None.

### Static fields
None distributed. No bathymetry or land–sea mask variable is included; the mask
must be derived from the NaN pattern of any field.

---

## Data availability
- **Is the data free?** Yes — anonymous S3 access, no registration or AWS account
- **License:** Taiwan Open Government Data License, version 1.0
  (政府資料開放授權條款) — https://data.gov.tw/en/license. Attribution required;
  commercial use permitted.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (HDF5 disk format).
  ⚠️ **No CF conventions.** The file carries **zero global attributes** — no
  `Conventions`, `title`, `institution`, or `source`. Variables have no `units`,
  `standard_name`, or `_FillValue`; missing data is bare IEEE NaN. CF-aware
  tooling (xarray decoding, cf-python, THREDDS aggregation) will need manual
  metadata injection.
- **Product identifier:** `M-B0071` (CWA open data product code)
- **Dataset identifiers:** `M-B0071-000` — the only live dataset for this product
- **File naming:** `M-B0071-000.nc` — a **fixed key, overwritten in place** each
  cycle. There is no date or cycle token anywhere in the path.
- **File size:** ~127 MB per cycle (all 7 variables × 97 timesteps in one file)
- **Official access:**
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - Direct: `https://cwaopendata.s3.ap-northeast-1.amazonaws.com/Model/M-B0071-000.nc`
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 cp --no-sign-request s3://cwaopendata/Model/M-B0071-000.nc .`
  - Metadata sidecars: `M-B0071-000.json` and `M-B0071-000.xml` (grid geometry,
    `InitialTime`, issue timestamp, resource URL)
  - **CWA Open Data API:** TBD. The CWA platform serves most product codes through
    a REST datastore endpoint requiring a free API key; whether M-B0071-000 is
    exposed there has not been verified. The S3 route needs no key and should be
    preferred regardless.
- **DOI:** None
- **Delivery mechanism:** AWS Open Data (S3, anonymous)

---

## Version history

No version history is published. CWA provides **no product reference document**
for M-B0071 — the `opendatadoc/Model/M-B0071.pdf` path returns 404, in contrast
to `M-A0060.pdf` and `M-A0061.pdf` which are both live.

The only dated marker available is the legacy JSON/XML distribution
(`M-B0071-001` … `-072`, see *Notes*), whose files are frozen at **2024-03-06**,
suggesting the NetCDF product superseded it around that date.

---

## Notes

- **"3-D" claim vs. surface-only distribution.** The sidecar `resourceDesc` reads
  三維海流作業化模式 (three-dimensional operational current model), and the AWS
  registry inherits similar framing. The distributed NetCDF has dimensions
  `(time: 97, lat: 291, lon: 161)` — no depth axis, no subsurface fields. The
  underlying model is presumably 3-D; the **public product is a surface extract**.
  Flagged as an open discrepancy.

- **Legacy JSON/XML distribution is stale — do not use.** `M-B0071-001` through
  `M-B0071-072` still exist in the bucket as ~50 MB JSON and ~62 MB XML files
  (one per forecast hour), containing the same grid serialised point-by-point.
  These are **frozen at 2024-03-06** and carry a truncated `dataid` of `B0071`.
  Only `M-B0071-000.nc` is live. The stale files still list plausible-looking
  variable names (海表溫度, 橫向流速, …) and could easily be mistaken for current
  data.

- **Update frequency is inferred, not documented.** The 00 UTC base time, the
  96 h span, and the single observed publication at 04:42 UTC are all consistent
  with a once-daily cycle, but this is a single-snapshot inference. Confirming it
  requires polling the object's `Last-Modified` header across several days.
  **TBD.**

- **No archive.** Unlike most AWS-hosted model data, `s3://cwaopendata/` retains
  nothing. Each cycle overwrites the previous one at the same key. There is no
  `LatestVersion`, no versioning, and no date-partitioned tree — building a
  hindcast or a verification series requires the user to run their own scheduled
  retrieval.

- **Sea level reference frame is not stated.** `SSH` values run from −2.66 m to
  +3.58 m over the domain and forecast. Given that explicit tides are present, the
  bulk of this range is tidal, but whether the field is referenced to the geoid,
  a mean sea surface, or a model datum is undocumented. **TBD** — this matters for
  anyone comparing against tide-gauge records.

- **Salinity floor of 11.4** in the northern domain indicates substantial
  freshwater influence (Yangtze / Pearl River plumes). Users should not assume
  open-ocean salinity ranges when setting colour scales or QC thresholds.

- **Related CWA entries.**
  [CWA Taiwan Regional WRF](../../../nwp_models/regional/taiwan/cwa-regional.md)
  (M-A0061 15 km / M-A0064 3 km) and
  [GFS (CWA Redistribution)](../../../nwp_models/regional/taiwan/gfs-cwa.md)
  (M-A0060) share the same bucket and licence. Note that the WRF and GFS products
  are GRIB2 under `Model/`; OCM is the only NetCDF product in that directory.

---

## Official documentation
- Product page: None published. CWA's dataset browser
  (https://opendata.cwa.gov.tw/) is a JavaScript application with no static page
  for this code.
- Product User Manual (PUM) or equivalent: **None** —
  `https://opendata.cwa.gov.tw/opendatadoc/Model/M-B0071.pdf` returns 404
- Quality Information Document (QUID) or validation report: **None published**
- DOI: None
- Operating institute: https://www.cwa.gov.tw/
- CWA open data platform: https://opendata.cwa.gov.tw/
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
- Licence: https://data.gov.tw/en/license

### Key references
None identified. No peer-reviewed description of CWA's operational OCM system was
located. Published Taiwan-area operational ocean modelling literature (e.g. the
ROMS-based Taiwan Strait Nowcast/Forecast System, TFOR) describes **other**
systems and should not be cited as documentation for this product.
