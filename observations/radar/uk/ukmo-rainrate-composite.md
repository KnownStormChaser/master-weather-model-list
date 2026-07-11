# UK Met Office Radar Composite (Surface Rain Rate Estimate)

## What this is
The United Kingdom Composite Surface Rain Rate Estimate (SRR) is the Met Office's
national weather-radar mosaic: a radar-reflectivity-derived estimate of
near-surface rain rate (mm/h) across the UK, produced on a 1 km grid every 15
minutes and distributed as ODIM HDF5. It is an observational precipitation
product — the UK counterpart to the other national/continental composites in this
catalog (MRMS, OPERA, DPC SRI).

The Met Office describes it as an *international* composite: beyond the Met Office
and partner-owned radars in Great Britain and Northern Ireland, it incorporates
Met Éireann radars in the Republic of Ireland (Dublin, Shannon) and the Jersey
Met radar in the Channel Islands. Note that the UK is itself a EUMETNET OPERA
member — its radars contribute to the pan-European OPERA composite — so this is
the Met Office's own higher-detail national product, distinct from and
complementary to OPERA over the UK, rather than a gap-filler. A two-year rolling
archive is openly available on AWS with anonymous access.

---

## Who operates it
- **Operator / coordinating programme:** Met Office (UK). Central processing is the **Radarnet** system at Exeter (on-site radar software is the Met Office's in-house "Cyclops"). The radar network is a consortium — some radars are owned by the Environment Agency and Natural Resources Wales, with the Met Office providing technical and operational support.
- **Country / region:** United Kingdom, with contributing radars in the Republic of Ireland and the Channel Islands.
- **Data distributor:** Met Office, via AWS Open Data.

---

## Network composition
Up to 16 radars contribute to the 1 km composite, giving coverage of over 99% of
the UK. The radars are C-band; the network became Doppler-capable across the
board in 2012 and completed its dual-polarisation upgrade in 2018. Each radar
scans five to ten low-elevation angles (0.5–9.0°) every 5 minutes, giving good
quantitative data to ~75 km and useful qualitative data to ~255 km. Operational
sites include Clee Hill, Hameldon Hill, Chenies, Castor Bay, Predannack, Ingham,
Crug-y-Gorllwyn, Hill of Dudwick, Druim a'Starraig, Cobbacombe Cross, Thurnham,
Dean Hill, Holehead, Munduff Hill, and High Moorsley, plus the non-Met-Office
contributors noted above (La Moye / Channel Islands via Jersey Met; Dublin and
Shannon via Met Éireann). The composite is gridded on the British National Grid
(OSGB, Cartesian) at 1 km — the `ng` in the product filenames. Radarnet
processing includes ground-clutter removal; attenuation, beam-blockage, and
height corrections; bright-band and (NWP-based) orographic-enhancement
corrections; reflectivity-to-rain-rate conversion; and polar-to-National-Grid
resampling.

---

## Products
- **Surface Rain Rate Estimate (SRR)** — near-surface rain rate (mm/h), radar-reflectivity-derived, as a 1 km UK composite updated every 15 minutes. This is the single product on the AWS open feed (`rainrate_composite_1km_UK`, ODIM HDF5).

The wider Radarnet system also produces dual-polarisation variables, Doppler wind
profiles, and precipitation accumulations, but the open AWS channel carries the
1 km SRR composite specifically (see Notes).

---

## Data availability
- **Is the data free?** Yes — anonymous, no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** ODIM HDF5 (`.h5`).
- **Update cadence:** Four images per hour (every 15 minutes); available within ~20 minutes of the product's validity time.
- **Primary access:**
  - **AWS:** `s3://met-office-radar-obs-data` (region `eu-west-2`, no account: `aws s3 ls --no-sign-request s3://met-office-radar-obs-data/`). Browse: https://met-office-radar-obs-data.s3.eu-west-2.amazonaws.com/index.html
  - **Layout:** `radar/YYYY/MM/DD/<filename>.h5`
  - **Filename convention:** `<YYYYMMDDhhmm>_ODIM_ng_radar_rainrate_composite_1km_UK.h5` (timestamp in UTC; `ng` = National Grid).
    - Example: `radar/2024/11/21/202411210930_ODIM_ng_radar_rainrate_composite_1km_UK.h5` (~1.4 MB)
    - List a day: `aws s3 ls --no-sign-request s3://met-office-radar-obs-data/radar/2024/11/21/`
- **New-data notifications:** SNS `arn:aws:sns:eu-west-2:633885181284:met-office-radar-obs-data-object_created`
- **Archive depth:** A **2-year rolling archive** (older data ages out). This is not a deep historical archive; for longer records the Met Office / CEDA archives are the route.
- **Licence:** **CC BY-SA 4.0** (British Crown copyright, the Met Office). ShareAlike — attribution required and derivatives must be shared alike.

---

## Scope note
- **Observation, not forecast.** SRR is an observed rain-rate composite — clean fit.
- **National composite, complementary to OPERA.** The UK is an OPERA member, so unlike the Italian DPC SRI this is *not* filling an OPERA gap; it is the Met Office's own national 1 km product, finer over the UK than the pan-European OPERA composite.
- **ShareAlike obligation.** CC BY-SA 4.0 carries a ShareAlike requirement (as with the Italian DPC SRI, and unlike OPERA's CC BY 4.0) — relevant for redistributors.
- **Non-operational service.** The Met Office frames this AWS channel as a non-operational, best-effort service (limited support SLA); it is a permanent open channel but not guaranteed operational-grade availability.

---

## Notes
- **International composite.** Includes Met Éireann (Dublin, Shannon) and Jersey Met (La Moye / Channel Islands) radars in addition to the Met Office / Environment Agency / Natural Resources Wales network — hence "international."
- **ODIM HDF5.** OPERA Data Information Model HDF5 — readable with `wradlib`, `xradar`, `h5py`, or Py-ART. The exact grid definition (British National Grid / OSGB, EPSG:27700) is carried in the file's ODIM `/where` group.
- **Open feed vs full suite.** Radarnet produces dual-pol moments, Doppler winds, and accumulations, but the AWS open feed is the 1 km SRR composite only.
- **Data feed vs viewer.** The Met Office's public rainfall radar map and rendered imagery are viewers; the gridded feed is the ODIM HDF5 here.

---

## Recent version history
- **2018 — network dual-polarisation upgrade completed** (improves precipitation typing, clutter removal, and rain-rate estimates).
- **2012 — network became Doppler-capable** across all sites.

---

## Official documentation
- AWS registry: https://registry.opendata.aws/met-office-uk-radar-observations/
- Met Office external data channels: https://www.metoffice.gov.uk/services/data/external-data-channels
- Met Office radar factsheet 15 (weather radar): https://www.metoffice.gov.uk/binaries/content/assets/metofficegovuk/pdf/research/library-and-archive/library/publications/factsheets/factsheet_15-weather-radar-2020_2023.pdf
