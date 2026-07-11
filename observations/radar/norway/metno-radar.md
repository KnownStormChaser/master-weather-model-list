# MET Norway Radar (Nordic Reflectivity Composite + Norwegian Accumulation)

## What this is
The Norwegian Meteorological Institute (MET Norway) publishes open, gridded
weather-radar products through its THREDDS data server. Two products are in
scope here: a **Nordic radar reflectivity mosaic** and a **Norwegian radar
precipitation-accumulation** product. Both are observational, CF-compliant
NetCDF, openly downloadable with no account.

The reflectivity mosaic is a Nordic composite (Norway plus neighbouring
countries' radars), and — as with the UK composite — it is the region's own
product rather than an OPERA gap-filler, since the Nordic countries are OPERA
members. Two other MET Norway "radar" routes are explicitly **out of scope** and
documented in the Scope note: the HF-radar archive (ocean surface currents, a
different instrument) and the `api.met.no` radar service (rendered images and
animations, not gridded data).

---

## Who operates it
- **Operator / coordinating programme:** Norwegian Meteorological Institute (MET Norway / met.no). The Nordic reflectivity composite combines MET Norway's radars with those of neighbouring national services (Sweden / SMHI, Finland / FMI) under Nordic radar cooperation.
- **Country / region:** Norway (accumulation product); Nordic — Norway, Sweden, Finland (reflectivity composite).
- **Data distributor:** MET Norway THREDDS server (`thredds.met.no`).

---

## Network composition
MET Norway operates roughly a dozen C-band dual-polarisation radars distributed
along the length of Norway (the current roster is on MET Norway's radar pages).
The Nordic reflectivity composite additionally ingests Swedish and Finnish
radars. Products are gridded mosaics (pseudo-CAPPI), not single-site volumes.
The reflectivity grid is a Lambert Conformal Conic projection (latitude of
origin 63°N, central meridian 15°E, standard parallels 63°N, spherical Earth
R = 6 371 km) at 1 km; the accumulation grid is UTM zone 33N at 1 km.

---

## Products
- **Nordic reflectivity mosaic** (`reflectivity-nordic`): pseudo-CAPPI at the lowest level, every 5 minutes, ~12 MB per NetCDF. Variables include `equivalent_reflectivity_factor` (radar reflectivity, dBZ), `lwe_precipitation_rate` (derived precipitation rate, mm/h), precipitation phase (flag: rain / sleet / snow), and a convective/stratiform classification — plus a full set of per-pixel quality fields (geometrical blocking %, sea/ground/other clutter flags, probability of clutter %, low/high elevation flags, no-data).
- **Norwegian precipitation accumulation** (`radaraccr`): SRI-derived 1-hour rainfall accumulation, 1 km UTM33, one NetCDF per day (each ~12–22 MB, holding that day's hourly accumulation fields).

---

## Data availability
- **Is the data free?** Yes — no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** NetCDF (CF-1.6).
- **Update cadence:** Reflectivity every 5 minutes; accumulation hourly (one file per day).
- **Primary access (MET Norway THREDDS, `thredds.met.no`):** each dataset is exposed via OPeNDAP (server-side subsetting), HTTPServer (whole-file download), WCS, and WMS. (MET Norway advises the WMS service is for simple demonstrations only, not production use.)
  - **Reflectivity (Nordic):**
    - Catalog: https://thredds.met.no/thredds/catalog/remotesensing/reflectivity-nordic/latest/catalog.html
    - Direct download: `https://thredds.met.no/thredds/fileServer/remotesensing/reflectivity-nordic/latest/<file>.nc`
    - OPeNDAP: `https://thredds.met.no/thredds/dodsC/remotesensing/reflectivity-nordic/latest/<file>.nc`
    - Filename: `yrwms-nordic.mos.pcappi-0-dbz.<processing-flags>.nordiclcc-1000.<YYYYMMDD>T<HHMMSS>Z.nc`
  - **Accumulation (Norway):**
    - Catalog: `https://thredds.met.no/thredds/catalog/remotesensingradaraccr/<YYYY>/<MM>/catalog.html`
    - Filename: `norway.mos.sri-acrr-1h.<processing-flags>.utm33-1000.<YYYYMMDD>.nc`
- **New-data notifications:** No push/SNS feed; poll the `latest` catalog (reflectivity) or the dated catalog structure.
- **Fair-use terms:** MET Norway's THREDDS is a shared public service with an explicit fair-use policy — do not run parallel OPeNDAP sessions or file downloads (wait for each request to finish before the next), and they reserve the right to block IPs that cause traffic overload. Planned-maintenance/incident status is at status.met.no; operational users are asked to contact thredds@met.no.
- **Archive depth:** A dated catalog structure (`YYYY/MM[/DD]`) provides a multi-year archive; reflectivity also has a rolling `latest` folder for recent files. (Exact archive start is visible in the dated catalog.)
- **Licence:** CC BY 4.0 (MET Norway's standard free-data policy; the radar image service states there are no usage restrictions). The NetCDF files carry no explicit `license` attribute, so this reflects MET Norway's data policy rather than a per-file tag — attribution to MET Norway.

---

## Scope note
- **Observation, not forecast.** A reflectivity mosaic and a rainfall-accumulation product — clean fit.
- **HF-radar route is out of scope.** `remotesensinghfradar/` (files like `RDLm_TORU_YYYY_MM_DD.nc` for sites TORU, SLAT, KRAK, JOMF, FEDJ) is **High-Frequency radar measuring ocean surface currents** (CODAR radial data), not precipitation. It is a different instrument class and does not belong in the weather-radar section.
- **The image API is out of scope.** `api.met.no/weatherapi/radar/2.0` returns **static images and animations** (PNG/GIF) for map areas — its own documentation says so — with types like `reflectivity`, `accumulated_01h…24h`, `preciptype`. That is rendered viewer output, excluded on the same rule as other rendered radar imagery; the gridded equivalent is the THREDDS NetCDF above.
- **Nordic composite vs OPERA.** Norway, Sweden, and Finland are OPERA members; this Nordic reflectivity mosaic is their own shared composite (hosted by MET Norway), complementary to and finer over the Nordics than the OPERA pan-European product.

---

## Notes
- **Rich reflectivity files.** Beyond reflectivity and derived rain rate, each file carries precipitation-phase and convective/stratiform classification plus per-pixel quality flags (blocking %, clutter flags and probability, elevation flags) — useful for QC-aware workflows.
- **Projections differ between the two products** — reflectivity in Nordic LCC (63°N / 15°E, spherical Earth), accumulation in UTM33N — so reproject before combining them.
- **Naming decode.** `yrwms-nordic` = MET Norway ("Yr" weather-map-service) Nordic; `mos` = mosaic; `pcappi-0-dbz` = pseudo-CAPPI lowest level, reflectivity; `sri-acrr-1h` = Surface-Rainfall-Intensity-derived 1-hour accumulation; the grid tag (`nordiclcc-1000`, `utm33-1000`) encodes projection + 1 km.
- **THREDDS services.** OPeNDAP allows grabbing a spatial/temporal subset without downloading the whole file; HTTPServer serves the full NetCDF; WCS and WMS are also exposed.
- **Data feed vs viewer.** The `api.met.no` radar service and Yr's radar map are viewers; the THREDDS NetCDF is the data.

---

## Official documentation
- Reflectivity catalog (Nordic): https://thredds.met.no/thredds/catalog/remotesensing/reflectivity-nordic/latest/catalog.html
- Accumulation catalog (Norway): https://thredds.met.no/thredds/catalog/remotesensingradaraccr/catalog.html
- MET Norway radar image API (rendered — reference only): https://api.met.no/weatherapi/radar/2.0/documentation
- MET Norway free data / licence (CC BY 4.0): https://www.met.no/en/free-meteorological-data
