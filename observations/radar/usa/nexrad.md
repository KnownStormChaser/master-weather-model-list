# NEXRAD (WSR-88D) — Single-Site Level II / Level III

## What this is
NEXRAD (Next Generation Weather Radar) is the United States network of WSR-88D
Doppler weather radars. This entry covers the **single-site radar data** — the
raw per-radar output, as opposed to a national mosaic. It comes in two processing
levels: **Level II**, the original-resolution base moments in the radar's native
polar coordinate system, and **Level III**, the derived meteorological analysis
products generated from Level II. Both the full historical archive (from June
1991) and a real-time feed are openly available from Amazon S3 (managed by NSF
Unidata) and Google Cloud Storage, with NOAA/NCEI as the authoritative archive.

NEXRAD is an observational dataset, not a forecast product. It is also the raw
network that underlies [MRMS](./mrms.md): MRMS mosaics NEXRAD (together with
other sensors) into a gridded CONUS composite, whereas the data here is the
per-radar volume data itself. It is the US counterpart to the single-site volume
family distributed through Europe's OPERA/ORD.

---

## Who operates it
- **Operator / coordinating programme:** The radar network is operated by NOAA's National Weather Service (NWS), the Federal Aviation Administration (FAA), and the U.S. Air Force (USAF). Data are archived and disseminated by NOAA's National Centers for Environmental Information (NCEI). Open cloud distribution is managed by NSF Unidata (on AWS) under NOAA's Open Data Dissemination / Big Data Program, and mirrored on Google Cloud via an NCEI–Google partnership.
- **Country / region:** United States — CONUS, Alaska, Hawaii, U.S. territories, and select overseas DoD sites.
- **Data distributor:** Amazon S3 (Unidata-managed buckets) and Google Cloud Storage; NCEI is the system of record.

---

## Network composition
160 WSR-88D radars — S-band (~10 cm), Doppler, dual-polarization since 2011 —
operated by NWS (most sites), the FAA, and the USAF. Volume-scan cadence is
4.5, 5, 6, or 10 minutes depending on the selected Volume Coverage Pattern (VCP).
Level II is recorded at all NWS and most FAA/USAF sites. The data are per-radar
**polar volumes** (values indexed by azimuth, range, and elevation angle), not a
regular latitude/longitude grid.

---

## Products
- **Level II (base data):** original-resolution base moments in native radar coordinates — reflectivity, mean radial velocity, and spectrum width — plus, since the 2011 dual-polarization upgrade, differential reflectivity (`ZDR`), correlation coefficient (`CC`/`RhoHV`), and differential phase (`PhiDP`). Encoded in the NEXRAD Archive Level II format (super-resolution message-31 records since 2008; readers must also handle legacy message-1 records for the older archive).
- **Level III (derived products):** analysis products computed from Level II — base and composite reflectivity, storm-relative velocity, echo tops, VIL, one-hour and storm-total precipitation, and dual-pol products (hydrometeor classification, digital precipitation), among many others. Encoded in the NEXRAD Level III product format. The AWS bucket carries a **real-time select** subset; the complete Level III archive is held at NCEI.

---

## Data availability
- **Is the data free?** Yes — no account on AWS (anonymous); free on GCP (console access requires sign-in, but CLI/API-link access does not).
- **Is the data downloadable?** Yes.
- **Access tier:** Open (AWS no-account anonymous; GCP public, no-cost).
- **Data formats:** Level II — NEXRAD Archive Level II binary (bzip2 for real-time chunks; gzip or uncompressed in the archive). Level III — NEXRAD Level III product binary. Readable with Py-ART, MetPy, and `nexradaws`.
- **Update cadence:** Real-time (one volume scan every ~4.5–10 min per VCP); archive from June 1991 to ~1 day behind present.
- **Primary access:**
  - **AWS (Unidata / NSF, `us-east-1`, no account):**
    - Level II archive: `s3://unidata-nexrad-level2` — layout `/<YYYY>/<MM>/<DD>/<SSSS>/`, one object per volume scan. Browse: https://unidata-nexrad-level2.s3.amazonaws.com/index.html
    - Level II real-time chunks: `s3://unidata-nexrad-level2-chunks` — layout `/<SSSS>/<volume>/<YYYYMMDD-HHMMSS-CHUNKNUM-CHUNKTYPE>`, where `<volume>` cycles 0–999 (e.g. `KDFX/602/20190510-143508-028-I`). bzip2-compressed; sub-volume chunks, lowest latency
    - Level III real-time select: `s3://unidata-nexrad-level3`. Browse: https://unidata-nexrad-level3.s3.amazonaws.com/index.html
    - Example: `aws s3 ls --no-sign-request s3://unidata-nexrad-level2/2019/05/06/KTLX/`
    - Filename convention: `<SSSS><YYYYMMDD>_<HHMMSS>[_V06][.gz]` (e.g. `KTLX20130420_205120_V06`). Files add `_V06` from 2015; the `.gz` suffix was dropped after 2016-06-02; some pre-2015 data is also in `.tar`.
  - **Google Cloud Storage (public, no-cost):**
    - Level II: `gs://gcp-public-data-nexrad-l2` — layout `/<YYYY>/<MM>/<DD>/<SSSS>/`, with each radar's volume scans **bundled into hourly tar files** (e.g. `NWS_NEXRAD_NXL2DP_KAMA_20160503000000_20160503005959.tar`).
    - Level III: `gs://gcp-public-data-nexrad-l3`
    - Example: `gcloud storage ls gs://gcp-public-data-nexrad-l2/2016/05/03/KAMA/`
  - **NCEI:** authoritative archive and order interface, including the full Level III product archive.
- **New-data notifications (AWS SNS):**
  - Level II real-time, filterable: `arn:aws:sns:us-east-1:684042711724:NewNEXRADLevel2ObjectFilterable` (message-filterable on `SiteID`, `VolumeID`, `ChunkID`, `ChunkType`, `L2Version`)
  - Level II archive: `arn:aws:sns:us-east-1:684042711724:NewNEXRADLevel2Archive`
  - Level III: `arn:aws:sns:us-east-1:684042711724:NewNEXRADLevel3Object`
- **Other mirrors:** Unidata operates a demonstration THREDDS Data Server over the archive (access restricted to `.edu` domains; not guaranteed long-term).
- **Archive depth:** June 1991 to present (Level II). Level III on AWS is real-time select; the complete Level III archive is at NCEI.
- **Licence:** NOAA Open Data Dissemination (NODD) — open to the public, usable as desired; a U.S. Government work, effectively public domain. Attribution requested; no stating or implying NOAA endorsement. NCEI citation DOI for Level II: `10.7289/V5W9574V`.

---

## Scope note
- **Observation, not forecast.** Raw single-site base data (Level II) and derived analysis products (Level III) — pure observation, clean fit.
- **Polar, not gridded.** Level II is per-radar polar/radial volume data in a standard open binary format, not a regular lat-lon grid. It enters on the same basis as the OPERA single-site volume family — raw structured observational data through a permanent open channel — and should not be conflated with the gridded MRMS / OPERA *composites*.
- **Relationship to MRMS.** NEXRAD is the raw per-radar network; MRMS blends it (plus gauges, lightning, satellite, and model fields) into the gridded CONUS mosaic. The two US radar entries are complementary — raw single-site here, multi-sensor composite in `mrms.md`.

---

## Notes
- **Bucket rename (current).** The former AWS Level II archive bucket `noaa-nexrad-level2` was deprecated and discontinued on 1 September 2025; use `unidata-nexrad-level2`. Only the bucket name and the SNS account number (now `684042711724`) changed — object paths and data format are unchanged. Note that AWS's own access README still shows the pre-migration bucket name and the old archive SNS account (`811054952067`); the registry entry is the authoritative source for these values.
- **AWS vs GCP layout.** AWS stores one object per volume scan; GCP bundles each radar's scans into hourly `.tar` files. Same underlying data, different packaging — code written for one won't directly read the other.
- **Real-time chunks vs volume scans.** `unidata-nexrad-level2-chunks` streams sub-volume chunks as they arrive (fastest latency, may be partial) before they are aggregated into complete volume scans in the archive bucket.
- **Reading tools.** Py-ART and MetPy (Python), `nexradaws` (S3 query/download), and language-specific libraries (e.g. `nexrad-level-2-data` for JS). Message-31 (super-resolution) vs legacy message-1 records both appear across the historical archive.
- **Data feed vs viewer.** Rendered NEXRAD imagery (the NWS RIDGE / RIDGE II tile services) is viewer-only and out of scope; the Level II/III binaries here are the data feed.

---

## Recent version history
- **1 September 2025 — AWS Level II archive bucket renamed** `noaa-nexrad-level2` → `unidata-nexrad-level2` (old bucket discontinued; SNS account → `684042711724`).
- **2011–2013 — dual-polarization upgrade** across the network (added `ZDR`, `CC`, `PhiDP`).
- **2 June 2016 — archive filenames drop the `.gz` suffix**; **2015 —** filenames add `_V06`.
- **2008 — super-resolution** (message-31) records introduced.
- **June 1991 — start of the archive.**

---

## Official documentation
- AWS registry: https://registry.opendata.aws/noaa-nexrad/
- AWS access docs (buckets, paths, SNS): https://github.com/awslabs/open-data-docs/tree/main/docs/noaa/noaa-nexrad
- GCP Level II (marketplace): https://console.cloud.google.com/marketplace/product/noaa-public/nexrad-l2
- GCP Level III (marketplace): https://console.cloud.google.com/marketplace/product/noaa-public/nexrad-l3
- NCEI Level II landing page (DOI `10.7289/V5W9574V`): https://www.ncei.noaa.gov/access/metadata/landing-page/bin/iso?id=gov.noaa.ncdc:C00345
- Level II ICD, message data formats (Build 18): https://www.roc.noaa.gov/wsr88d/PublicDocs/ICDs/2620002R.pdf
- Level III ICD, message data formats (Build 18): https://www.roc.noaa.gov/wsr88d/PublicDocs/ICDs/2620001X.pdf
- Ansari & Del Greco (2018), *Unlocking the Potential of NEXRAD Data through NOAA's Big Data Partnership*, BAMS: https://doi.org/10.1175/BAMS-D-16-0021.1
