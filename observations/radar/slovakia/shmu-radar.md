# SHMÚ Radar (Slovakia — National Network: Volumes & Composites)

## What this is
The Slovak Hydrometeorological Institute (Slovenský hydrometeorologický ústav,
SHMÚ) publishes its national weather-radar output openly through the SHMÚ Open
Data server (`opendata.shmu.sk`). This entry covers both the **single-site 3D
polarimetric volume data** from each of the four radars and the national **2D
composite products** (column-max reflectivity, 2 km pseudo-CAPPI, echo-top
height, and a 1-hour radar precipitation accumulation) — all freely
downloadable with no account, in ODIM HDF5.

Everything in this tree is observational and gridded/structured HDF5: there is
no rendered-imagery channel and no extrapolation-forecast product here (a
cleaner split than the neighbouring ČHMÚ feed). Slovakia is a EUMETNET OPERA
member, so these SHMÚ products are the national counterpart to the OPERA
pan-European composite.

---

## Who operates it
- **Operator:** Slovak Hydrometeorological Institute (Slovenský hydrometeorologický ústav, SHMÚ / SHMI).
- **Country / region:** Slovakia.
- **Data distributor:** SHMÚ Open Data (`opendata.shmu.sk`); WMO location indicator **LZIB** (Bratislava).

---

## Network composition
SHMÚ operates a **four-radar C-band (λ ≈ 5.35 cm) dual-polarisation Doppler**
network (Selex/Leonardo, RAINBOW processing):

| Site | Code | Position | Antenna height | Bulletin suffix |
|---|---|---|---|---|
| **Malý Javorník** | `skjav` | 48.2556°N, 17.1524°E | 600 m | `41` |
| **Kojšovská hoľa** | `skkoj` | 48.7827°N, 20.9873°E | 1256 m | `51` |
| **Kubínska hoľa** | `skkub` | 49.2717°N, 19.2494°E | 1425 m | `60` |
| **Španí laz** | `sklaz` | 48.2404°N, 19.2573°E | 661 m | `70` |

Each radar delivers the **full eight-moment polarimetric set** — corrected
reflectivity **DBZh**, uncorrected reflectivity **DBuZh** (Th), radial velocity
**V**, spectrum width **W**, differential reflectivity **ZDR**, differential
phase **PhiDP**, **specific differential phase KDP**, and correlation
coefficient **RhoHV**. (Distributing KDP directly is notable — many networks
compute it but publish only PhiDP.) Volume scans run **12 elevations** (0.0°,
0.5°, 1.0°, 1.5°, 2.0°, 2.7°, 3.4°, 4.4°, 7.0°, 11.4°, 18.3°, 26.7°) to a
**240 km** range on a 5-minute cycle; 250 m radial resolution, 1° azimuthal,
0.92° beamwidth.

---

## Products
**Single-site volume data** (`volume/<site>/<moment>/<YYYYMMDD>/`) — 3D polar
volumes, ODIM HDF5, per moment, date-partitioned, 5-minute:
- Moment folders: `dBZ` (DBZh), `dBuZ` (DBuZh), `V`, `W`, `ZDR`, `PhiDP`, `KDP`, `RhoHV`.

**Composite / mosaic products** (`composite/skcomp/<product>/<YYYYMMDD>/`) — 2D
grids, ODIM HDF5, Mercator, ~330×330 m, generated every 5 minutes as long as at
least one radar is measuring:
- **ZMAX** (`zmax`) — column-maximum reflectivity.
- **CAPPI 2km** (`cappi2km`) — reflectivity at 2 km AMSL (read from the nearest measured level).
- **ECHO TOP** (`etop`) — maximum height of ≥6 dBZ reflectivity.
- **PAC01** (`pac01`) — 1-hour (sliding) accumulated precipitation estimate from radar.

---

## Data availability
- **Is the data free?** Yes — no account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account).
- **Data formats:** ODIM HDF5. (Files self-declare `ODIM_H5/V2_1` / `H5rad 2.1`; the dataset description cites v2.4 — see *Notes*.)
- **Update cadence:** Every 5 minutes (volumes per radar; composites whenever ≥1 radar is scanning). Near-real-time.
- **Primary access:** plain directory tree (curl/wget) — https://opendata.shmu.sk/meteorology/weather/radar/
  - Volumes: `volume/<site>/<moment>/<YYYYMMDD>/` — filename `T_PA<code>Z<suffix>_C_LZIB_<YYYYMMDDhhmmss>.hdf`, where the moment code is `G` (DBZh), `J` (DBuZh), `H` (V), `I` (W), `K` (ZDR), `Q` (PhiDP), `R` (KDP), `L` (RhoHV) and `<suffix>` is the radar's `41/51/60/70`; e.g. `T_PAGZ41_C_LZIB_20260712180500.hdf` = Malý Javorník corrected reflectivity.
  - Composites: `composite/skcomp/<product>/<YYYYMMDD>/` — `T_PA{B,N,D,S}V22_C_LZIB_<YYYYMMDDhhmmss>.hdf` (ZMAX `B`, CAPPI2km `N`, ECHO TOP `D`, PAC01 `S`).
  - Timestamp is the date/time of the lowest measured elevation (start of the measurement), UTC.
- **New-data notifications:** None (no S3/SNS/API); poll the date-partitioned tree.
- **Other mirrors:** None known (single origin).
- **Archive depth:** Date-partitioned (`YYYYMMDD`) rolling window — observed ≈32 days for both volumes and composites at time of verification (deeper than the Czech/Hungarian near-real-time feeds, but still a rolling window, not a full archive).
- **Licence:** **CC BY 4.0**, attribution to SHMÚ (no registration). Stated in the server README (`https://opendata.shmu.sk/README.txt`). SHMÚ notes it retains client IP addresses ≤30 days for operational/security purposes.

---

## Scope note
- **Clean observational fit.** Single-site polarimetric volumes plus national reflectivity/echo-top/accumulation composites — all gridded HDF5, no rendered imagery and no forecast/nowcast products in this tree.
- **HTTPS certificate caveat.** The server is served over HTTPS (and also responds over plain HTTP). Its TLS certificate chain can omit an intermediate, which trips some strict verifiers / proxies ("unable to get local issuer certificate") even though browsers validate fine — scrapers hitting that can supply the intermediate or fall back to HTTP.

---

## Notes
- **Observation, not forecast.** Volumes and composites are observational. **PAC01 is radar-only** QPE accumulation (no rain-gauge merge — unlike ČHMÚ's MERGE or DWD's RADOLAN RW), so it is a pure radar precipitation estimate.
- **Relationship to OPERA.** Slovakia is a EUMETNET OPERA member; these radars contribute to the OPERA pan-European composite, and this feed is the national-resolution counterpart.
- **ODIM version discrepancy.** The dataset description cites ODIM HDF5 **v2.4**, but the live files self-identify as `ODIM_H5/V2_1` (`/what` version `H5rad 2.1`). Read with `wradlib`, `xradar`, `h5py`, or Py-ART regardless; geometry is in the ODIM `/where` group and radar parameters (e.g. wavelength) in `/how`.
- **Documentation vs. live tree.** Two paths in the dataset description don't match the server: the **RhoHV** location URL points at a `KDP/` folder (copy-paste error — the data is under `RhoHV/`), and **ECHO TOP** is served from `etop/`, not `etops/` as written. The live tree is authoritative.
- **Fine composite grid.** The national composites are ~330 m — considerably finer than the 1 km grids common elsewhere in this section.
- **Data feed vs viewer.** SHMÚ's public radar map at shmu.sk is rendered; this HDF5 tree is the data.

---

## Official documentation
- SHMÚ radar open-data tree: https://opendata.shmu.sk/meteorology/weather/radar/
- Dataset description (SK): https://opendata.shmu.sk/meteorology/weather/radar/volume/metadata/Radar_udaje_SHMU_SK.pdf
- SHMÚ Open Data terms / README (CC BY 4.0): https://opendata.shmu.sk/README.txt
- National open-data catalog: https://data.slovensko.sk
- ODIM HDF5 v2.4 spec (cited by SHMÚ): https://www.eumetnet.eu/wp-content/uploads/2021/07/ODIM_H5_v2.4.pdf
