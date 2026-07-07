# Satellites

This section catalogues **operational weather satellites with freely downloadable raw data**. It applies the same scope filter as the rest of the repository — open data, downloadable, no paywalls or commercial licensing — to the spaceborne observation systems that feed the forecast models documented elsewhere.

The satellite section is intentionally smaller and more selective than the model sections. Many weather satellites operate worldwide, but only a subset distribute raw Level 1 or Level 2 data openly. This index is the place to start when looking for what's freely available.

---

## What this section includes

- **Geostationary weather satellites** providing continuous regional imagery from ~36,000 km
- **Polar-orbiting weather satellites** providing global twice-daily coverage from low Earth orbit
- Operational systems with Level 1 (calibrated radiances) or Level 2 (geophysical retrievals) raw data freely available
- Both no-account-needed access (AWS Open Data, NOAA NOMADS) and free-with-registration access (EUMETSAT Data Store, equivalent national portals)

## What this section does NOT include

- Commercial weather satellites (e.g., Spire, Tomorrow.io, GeoOptics)
- Research and demonstration satellites without operational distribution
- Communications satellites or navigation satellites
- Earth observation missions outside the operational weather/atmospheric scope (e.g., Landsat, Sentinel-1/2 unless meteorologically relevant)
- Satellites without raw data access (imagery-viewer-only systems)
- Reanalysis-relevant altimetry and ocean observation missions (Jason, Sentinel-3/6, SMOS, SMAP) — these appear in the model entries as data assimilation inputs rather than as standalone satellite entries

---

## Access tiers

Satellite data access varies meaningfully across operators. To make the practical differences obvious, entries use one of three access-tier labels:

- **Open S3** — direct download from AWS S3 (or equivalent cloud bucket), no account, no registration. Examples: GOES, Himawari (via NOAA), JPSS.
- **Open with registration** — free user account required, no usage approval gate, no commercial restrictions. Examples: Meteosat, MetOp (via EUMETSAT Data Store), Fengyun (via NSMC, when added).
- **Open with restrictions** — free but with conditions on use, geographic limitations, or approval requirements. Examples likely to apply if INSAT or Elektro-L are added.

The first tier is the lowest-friction access available for operational weather satellite data and represents the bulk of what's documented here.

---

## Quick reference — Open S3 access

For users wanting the most frictionless access, the following constellations distribute raw data via AWS Open Data with no account or registration required:

| Constellation | Operator | S3 buckets | Coverage |
|---|---|---|---|
| [GOES-R Series](./geostationary/usa/goes-r-series.md) | NOAA | `s3://noaa-goes16/`, `s3://noaa-goes17/`, `s3://noaa-goes18/`, `s3://noaa-goes19/` | Western Hemisphere geostationary |
| [Himawari-8/9](./geostationary/japan/himawari.md) | JMA (distributed by NOAA) | `s3://noaa-himawari8/`, `s3://noaa-himawari9/` | Western Pacific geostationary |
| [JPSS](./polar_orbiting/usa/jpss.md) | NOAA | `s3://noaa-nesdis-n21-pds/`, `s3://noaa-nesdis-n20-pds/`, `s3://noaa-nesdis-snpp-pds/` | Polar orbiting, twice-daily global |

All AWS buckets are in the `us-east-1` region. Each constellation also publishes SNS topics for new-data notifications (Lambda/SQS protocols only).

---

## Quick reference — Open with registration

The following constellations require a free user account but otherwise distribute openly:

| Constellation | Operator | Distribution channel | Coverage |
|---|---|---|---|
| [Meteosat (MSG and MTG)](./geostationary/eu/meteosat.md) | EUMETSAT | EUMETSAT Data Store | Europe, Africa, Atlantic, Indian Ocean geostationary |
| [MetOp (First and Second Generation)](./polar_orbiting/eu/metop.md) | EUMETSAT | EUMETSAT Data Store | Polar orbiting, mid-morning twice-daily global |

EUMETSAT data is also available operationally via EUMETCast (DVB-S satellite broadcast) for users with appropriate receiving equipment.

---

## Folder structure

Entries are organized by orbit type and operating country/region, mirroring the structure used elsewhere in this repository:

```
satellites/
  geostationary/
    usa/         — GOES-R Series
    japan/       — Himawari-8/9
    eu/          — Meteosat (MSG and MTG)
  polar_orbiting/
    usa/         — JPSS
    eu/          — MetOp (First and Second Generation)
```

Each entry covers a constellation or satellite series rather than individual spacecraft — GOES-R Series rather than separate GOES-16 and GOES-19 entries, MetOp rather than separate MetOp-B/C entries. This reflects how the operators themselves distribute the data and avoids duplication when operational role assignments shift between spacecraft within a series.

---

## What's intentionally missing (for now)

The current section covers the operationally significant Western and European weather satellite systems with truly open data access. Several other operational systems exist with free-but-registered access that may be added later:

- **Fengyun (FY-4 geostationary, FY-3 polar-orbiting)** — China Meteorological Administration, distributed via NSMC portal with registration
- **GEO-KOMPSAT-2A (GK-2A)** — Korea Meteorological Administration, distributed via NMSC portal with registration
- **INSAT-3D series** — India Meteorological Department, distributed via MOSDAC with registration and some restrictions on non-Indian users
- **Elektro-L series** — Roshydromet (Russia), distributed via NTSOMZ with intermittent availability

These are operationally important satellites and the data is genuinely free, but each has access friction worth documenting carefully. They're left for a future expansion of this section.

---

## Related sections of this repository

Satellite observations feed many of the models documented elsewhere in this repository:

- [`AI_MODELS.md`](../AI_MODELS.md) — AI weather models trained partially on satellite-derived analyses
- [`COPERNICUS.md`](../COPERNICUS.md) — Copernicus Marine and Atmosphere services that ingest satellite observations (notably Sentinel-4 and Sentinel-5 hosted on MTG and MetOp-SG)
- Air quality model entries — CAMS Global, NAQFC, RAQDPS all assimilate satellite composition retrievals
- Ocean physics model entries — GLO12, GIOPS, RTOFS all assimilate altimeter SWH, SST, and sea ice products
- NWP model entries — operational global NWP systems (GFS, IFS, GEM, ICON, others) assimilate IASI, ATMS, CrIS, AMSU, MHS, GPS-RO, AMV, and scatterometer winds from the satellites documented here

---

## Contributing

Additions and corrections to satellite entries follow the same conventions as the rest of the repository:

1. Verify the data is genuinely free and downloadable
2. Confirm operational status from official agency sources
3. Use the satellite template ([`templates/satellite.template.md`](../templates/satellite.template.md)) as a starting point
4. Place new entries in the appropriate `satellites/<orbit_type>/<country>/` directory
5. Update this README's quick-reference tables when adding new entries

The constellation-based grouping convention should be maintained — one entry per satellite series rather than per individual spacecraft.

---

## A note on operational role rotation

Operational role assignments rotate over time as new spacecraft commission, older ones move to standby, and oldest retire. Entries in this section document role assignments as of the last update but may need refreshing when:

- A new GOES, Meteosat, or Himawari launches and takes over a primary role
- JPSS or MetOp primary/secondary/tertiary designations shift
- A satellite's operational status changes (extended life, anomaly, retirement)

When updating role information, update both the relevant satellite entry and the quick-reference tables in this README.
