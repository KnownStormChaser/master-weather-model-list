# Radar

This section catalogues **operational weather-radar networks and composites with freely downloadable gridded data**. It applies the same scope filter as the rest of the repository — open data, downloadable, no paywalls or approval gates beyond simple registration — to ground-based radar remote sensing, the counterpart to the [satellite](../satellites/README.md) observation systems.

Two distinctions shape what belongs here. First, **observation, not forecast**: this section documents what the radars are seeing now — reflectivity, precipitation rate, accumulation — while radar-derived *nowcasts* that project precipitation forward belong in the nowcasting model section. Second, **data, not imagery**: gridded or structured radar data (GRIB2, NetCDF, ODIM HDF5, native binary) is in scope, while rendered radar maps, web loops, and tile services are not, however widely available.

---

## What this section includes

- **Radar composites / mosaics** — national and multi-national reflectivity and precipitation composites
- **Quantitative precipitation estimation (QPE)** — radar-derived rain rate and gauge-adjusted accumulations
- **Single-site polarimetric data** — per-radar moment and volume data (the radar counterpart to satellite Level 1)
- Both no-account access (cloud buckets, HTTP directories, THREDDS) and free-with-registration access

## What this section does NOT include

- Rendered radar imagery, animations, and tile/WMS viewers without downloadable gridded data (e.g. NWS RIDGE, national radar web loops, MSC GeoMet WMS)
- Radar-derived **nowcast / forecast** products — these project future precipitation and belong in the nowcasting model section (e.g. DWD RADVOR and RADOLAN-RV, KONRAD3D cell nowcasts)
- **Non-precipitation radar** — HF (ocean-surface-current) radar, wind-profiler radar, and other non-weather radar
- Object / feature detection distributed only as XML or point lists (cell tracks, mesocyclone lists) rather than gridded fields
- Real-time analyses distributed only through viewer front-ends

---

## Access tiers

Radar data access is described with the same three tier labels used across the observation sections:

- **Open (no account)** — direct download from a cloud bucket, HTTP directory, or THREDDS server, no account or registration. This is the bulk of the section.
- **Open with registration** — free account or access key required, no approval gate, no commercial restriction (e.g. the Italian DPC SRI *live* feed; its static archive is no-account).
- **Open with restrictions** — free but with conditions, geographic limits, or approval requirements (none currently documented; retained for completeness).

Most operational radar data documented here is in the first tier.

---

## Quick reference — all networks

| Network | Operator | Scope | Type | Format | Access | Licence |
|---|---|---|---|---|---|---|
| [MRMS](./usa/mrms.md) | NOAA / NSSL | US (CONUS + OCONUS) | Multi-sensor mosaic | GRIB2 | Open (no account) | Public domain |
| [NEXRAD](./usa/nexrad.md) | NOAA / NWS · Unidata | US | Single-site (L2 / L3) | NEXRAD L2/L3 binary | Open (no account) | Public domain |
| [OPERA](./eu/opera-composite.md) | EUMETNET | Pan-European | Composite | ODIM HDF5 / GeoTIFF | Open (no account) | CC BY 4.0 |
| [DPC SRI](./italy/dpc-sri.md) | DPC · FBK (Italy) | Italy | Composite | Zarr / GRIB / GeoTIFF | Open — anon archive; account for live | CC BY-SA 4.0 |
| [UK Met Office SRR](./uk/ukmo-rainrate-composite.md) | Met Office | UK (international) | Composite | ODIM HDF5 | Open (no account) | CC BY-SA 4.0 |
| [MET Norway](./norway/metno-radar.md) | MET Norway | Nordic + Norway | Composite | NetCDF (CF) | Open (no account) | CC BY 4.0 |
| [DWD](./germany/dwd-radar.md) | DWD | Germany | Composite + single-site | RADOLAN binary / ODIM HDF5 | Open (no account) | GeoNutzV (≈ CC BY 4.0) |
| [FMI](./finland/fmi-radar.md) | FMI | Finland | Composite + single-site | GeoTIFF / ODIM HDF5 | Open (no account) | CC BY 4.0 |
| [ČHMÚ (CZRAD)](./czechia/chmi-radar.md) | ČHMÚ | Czechia | Composite + single-site | ODIM HDF5 | Open (no account) | CC BY 4.0 |
| [HungaroMet](./hungary/hungaromet-radar.md) | HungaroMet | Hungary | Composite (CMax / PseudoCAPPI / 3D) | NetCDF (zipped) | Open (no account) | CC BY-SA 4.0 |
| [SHMÚ](./slovakia/shmu-radar.md) | SHMÚ | Slovakia | Composite + single-site | ODIM HDF5 | Open (no account) | CC BY 4.0 |

Note the licence spread: US products are public domain (NODD); OPERA and MET Norway are CC BY 4.0; the UK and Italian composites are CC BY-**SA** 4.0 (ShareAlike); DWD is GeoNutzV with mandatory attribution.

---

## Quick reference — open (no-account) endpoints

For the most frictionless access, these networks distribute gridded data with no account or registration:

| Network | No-account endpoint(s) | Host / region |
|---|---|---|
| [MRMS](./usa/mrms.md) | `s3://noaa-mrms-pds`; https://mrms.ncep.noaa.gov/ | AWS `us-east-1` / NCEP |
| [NEXRAD](./usa/nexrad.md) | `s3://unidata-nexrad-level2` (+ `-chunks`, `unidata-nexrad-level3`); `gs://gcp-public-data-nexrad-l2` / `-l3` | AWS `us-east-1` / Google Cloud |
| [OPERA](./eu/opera-composite.md) | ECMWF EWC S3 (anonymous) + MeteoGate ORD API | European Weather Cloud |
| [DPC SRI](./italy/dpc-sri.md) | ECMWF EWC S3 (anonymous); Zenodo `10.5281/zenodo.18637608` | EWC / Zenodo |
| [UK Met Office SRR](./uk/ukmo-rainrate-composite.md) | `s3://met-office-radar-obs-data` | AWS `eu-west-2` |
| [MET Norway](./norway/metno-radar.md) | https://thredds.met.no/ (OPeNDAP / HTTP / WCS / WMS) | MET Norway THREDDS |
| [DWD](./germany/dwd-radar.md) | https://opendata.dwd.de/weather/radar/ | DWD Open Data |
| [FMI](./finland/fmi-radar.md) | `s3://fmi-opendata-radar-geotiff`, `s3://fmi-opendata-radar-volume-hdf5` | AWS `eu-west-1` |
| [ČHMÚ (CZRAD)](./czechia/chmi-radar.md) | https://opendata.chmi.cz/meteorology/weather/radar/ | ČHMÚ Open Data |
| [HungaroMet](./hungary/hungaromet-radar.md) | https://odp.met.hu/weather/radar/composite/nc/ | HungaroMet ODP |
| [SHMÚ](./slovakia/shmu-radar.md) | https://opendata.shmu.sk/meteorology/weather/radar/ | SHMÚ Open Data |

The US buckets and the UK bucket take `--no-sign-request`; OPERA and the Italian archive use anonymous S3 on the ECMWF European Weather Cloud; MET Norway, DWD, ČHMÚ, HungaroMet, and SHMÚ are plain THREDDS / HTTPS.

---

## Folder structure

Entries are organized by operating country or organization, mirroring the structure used elsewhere in the repository:

```
observations/radar/
  usa/       — MRMS (multi-sensor mosaic), NEXRAD (single-site L2/L3)
  eu/        — OPERA (pan-European composite)
  italy/     — DPC SRI (national composite)
  uk/        — UK Met Office SRR (national / international composite)
  norway/    — MET Norway (Nordic reflectivity + Norwegian accumulation)
  germany/   — DWD (RADOLAN, reflectivity composites, single-site)
  finland/   — FMI (composites, single-radar products, ODIM volumes)
  czechia/   — ČHMÚ (CZRAD: single-site volumes + national composites)
  hungary/   — HungaroMet (national composites: CMax, PseudoCAPPI, 3D)
  slovakia/  — SHMÚ (CZRAD-style: single-site volumes + national composites)
```

Each entry covers a network or composite product rather than an individual radar — MRMS as one mosaic, NEXRAD as one single-site archive — with the single exception that a national service publishing genuinely distinct products (e.g. DWD's composites and single-site sweeps) documents them together in one entry.

---

## What's intentionally missing (for now)

Several systems were evaluated but not given full entries, for reasons worth recording. This catalog's radar entries are reserved for **real-time (or near-real-time) operational feeds**; genuinely open datasets that are only delayed or archival are noted here instead.

- **Canada (ECCC / MSC)** — viewer/imagery only. The open channels are rendered GIF (Datamart) and WMS map images (GeoMet); there is no open gridded feed. Worth revisiting if a WCS coverage or GRIB/GeoTIFF path appears (a "Radar product guide" is pending on the MSC side).
- **MET Norway HF-radar** — High-Frequency radar measuring **ocean surface currents**, not precipitation. A different instrument class; not weather radar.
- **Rendered image services** — `api.met.no` radar, NWS RIDGE / RIDGE II tiles, and national WMS/viewer loops return images, not gridded data.
- **Radar-derived nowcasts** — DWD RADVOR and RADOLAN-RV, KONRAD3D, and the nowcast fields bundled inside MRMS (FLASH, ANC) project future states; they are flagged for the **nowcasting model** section rather than documented here.
- **IDEAM (Colombia)** — genuine open single-site Level II data in Sigmet/IRIS format (`s3://s3-radaresideam`, `us-east-1`, CC BY 4.0), with a historical archive back to 2018. Noted here rather than given a full entry because the recent feed is **delayed and intermittent** rather than real-time: checked against the live bucket, the newest data typically runs 1–2 days behind, with whole days absent and only part of the network present on a given day. Valuable as a historical Level II archive — worth a full entry if IDEAM moves to a reliable real-time feed.

Other national radar services with open **real-time** gridded feeds may be added as they are verified.

---

## Related sections of this repository

- [`../satellites/README.md`](../satellites/README.md) — the parallel observation section (spaceborne remote sensing). Radar and satellite are the two observation branches under `observations/`.
- [`../../models/nowcasting_models/`](../../models/nowcasting_models/) — radar-derived nowcasts (DWD RADVOR / RADOLAN-RV, KONRAD3D, and similar) live here rather than in this section.
- **NWP entries that assimilate this data** — MRMS and NEXRAD reflectivity feed the US convection-allowing models (HRRR, RAP); OPERA reflectivity is assimilated by several European limited-area models (e.g. ALADIN-HR, CHMI-LAM). The radar entries here are the observational sources those model entries reference.

---

## Contributing

Additions and corrections to radar entries follow the same conventions as the rest of the repository:

1. Verify the data is genuinely free and downloadable through a permanent open channel (free registration is acceptable; paywalls and approval gates are not), and that it is a real-time or near-real-time operational feed — delayed-only or archive-only datasets are noted under "What's intentionally missing" rather than given full entries
2. Confirm it is an **observation**, not a nowcast/forecast — radar-derived nowcasts belong in the nowcasting model section
3. Confirm the data is **gridded or structured** (GRIB2, NetCDF, ODIM HDF5, native binary), not rendered imagery
4. Use the radar template ([`templates/radar.template.md`](../../templates/radar.template.md)) as a starting point
5. Place new entries in the appropriate `observations/radar/<country-or-region>/` directory
6. Update this README's quick-reference tables when adding new entries

---

## A note on the observation–forecast boundary

Radar data servers routinely mix observation and nowcast in a single tree — DWD is the clearest case, but MRMS bundles derived nowcast fields too. This section documents the observational products (reflectivity, precipitation rate, accumulation, single-site moments); where a feed also carries radar-derived nowcasts, the entry documents the observational part and flags the nowcast part for the nowcasting section.

Radar feeds are also comparatively volatile: buckets get renamed (NEXRAD's Level II archive moved from `noaa-nexrad-level2` to `unidata-nexrad-level2`), access gates change (OPERA's IP whitelisting was removed in 2026), and many feeds are short rolling windows rather than deep archives. Entries note these where known, but the live endpoint is always the authoritative source — verify against it before relying on a path.
