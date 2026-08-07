# AROME (IMGW-PIB, Poland)

## What this model is
AROME is the operational convection-permitting regional deterministic numerical weather prediction model run by IMGW-PIB over Poland and surrounding Central Europe. It runs the AROME (ALADIN-NH) system at 2 km and is nested inside the coarser [ALARO (Poland)](./alaro-poland.md) model, which supplies its lateral boundary conditions.

A subset of the model output is published as GRIB through IMGW-PIB's public data portal. This entry documents that public product (`AROME_pub`); the full operational output is larger than what is distributed publicly, and the public files are **regridded and spatially cropped** relative to the operational domain (see *What area it covers* and Notes).

> **Live-verified 2026-08-07** against the `AROME_pub` datastore using ecCodes 2.48.0 on the `fc20260806_06+000gl`, `fc20260806_06+042gl`, `fc20260806_12+002gl`, `fc20260806_12+006gl` and `fc20260806_12+021gl` files, plus HTTP probing of the step and cycle space. Fields marked **(verified)** below were read from the GRIB headers rather than taken from documentation.

---

## Who runs it
- **Organization:** Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy (IMGW-PIB)
- **Country / region:** Poland

---

## What area it covers
- **Coverage:** Poland and surrounding parts of Central Europe

### Operational domain — **P020**
- Lambert conformal projection
- 799 × 799 grid points at 2 km (≈ 1600 km across)

### Public product grid **(verified)**
The distributed files are **not** on the operational Lambert grid. They are interpolated to a plain geographic grid and cropped:

- `gridType = regular_ll` (GRIB1 data representation type 0), spherical earth
- **600 × 600 = 360,000 points**, identical on every message of every file
- Corners: **47.019 N / 9.234 E** (first point) to **57.981 N / 25.766 E** (last point)
- Increments: **0.028° longitude × 0.018° latitude**
- `jScansPositively = 1` (south → north), `iScansNegatively = 0`
- Latitudinal spacing is **2.00 km**, matching the native resolution; longitudinal spacing varies across the box (2.13 km at 47 N, 1.90 km at 52.5 N, 1.65 km at 58 N) as it must on a regular lat/lon grid
- Extent ≈ **1220 km N–S × 1120 km E–W**, i.e. roughly **70–76 % of the operational domain's linear extent**

The crop is far less severe than the one applied to [ALARO](./alaro-poland.md) (~38 % of its operational domain), which makes sense: ALARO's E040 domain is continental in scope, while P020 is already close to a Poland-sized box.

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** AROME (spectral limited-area model, ALADIN-NH dynamical core)
- **Code version:** CY43T2 (operational)
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (2 km grid; deep convection explicitly resolved). **Corroborated by the file contents (verified):** the public GRIB carries no convective cloud cover field and no convective/large-scale precipitation split, unlike ALARO — there is no deep convection scheme to diagnose them from. See *What it provides*.
- **Horizontal resolution:** 2 km (operational and public product alike; see the grid note above for how this maps onto the public lat/lon mesh)
- **Grid dimensions:** 799 × 799 operational; **600 × 600 in the public product (verified)**
- **Vertical levels:** 70 (model levels; the public GRIB carries pressure levels and single-level fields only — no model-level output)
- **Forecast length:** **42 h (verified)** — `+000gl` through `+042gl` all resolve, `+043gl` returns HTTP 404. Matches the IMGW product description, the 47th EWGLAM 2025 poster, and the 6th ASW April 2026 poster. *(The operational range was 30 h in every poster from 2020 through the 46th EWGLAM Sept 2024, extended to 42 h by the 47th EWGLAM Sept 2025.)* Unlike ALARO, the public feed is **not** truncated relative to the operational range.
- **Update frequency / cycles:** **4× daily (00, 06, 12, 18 UTC) — verified.** Four consecutive 6-hourly cycles (2026-08-05 18, 2026-08-06 00, 06 and 12 UTC) were simultaneously resident on the server on 2026-08-07.
- **Temporal output resolution:** **Hourly (verified**: 43 files per cycle, `+000`, `+001`, `+002` … `+042`, with no gaps**)**. Public product and operational output both hourly — unlike ALARO, there is no temporal downsampling.
- **Time step:** 50 s
- **Coupling:** LBC from **[ALARO (Poland)](./alaro-poland.md)** (ALARO-1, 4 km), **3 h coupling**. *(History: the IMGW poster series lists 1 h coupling in 2020 and April 2021, switching to 3 h from the Sept 2021 poster and staying 3 h through April 2026. The single 1 h value in the April 2024 ASW poster is contradicted by the Sept 2023 and Sept 2024 posters on either side of it and is treated as a carried-over transcription error — this resolves the earlier 3 h / 1 h flag.)*

---

## Data assimilation
- **Data assimilation:** None in operations as of the source material — AROME (Poland) is effectively a high-resolution dynamical downscaling of ALARO. (IMGW notes AROME data-assimilation studies as a future need; no operational AROME DA is documented.) **Flag for verification** — the GRIB headers carry no assimilation indicator, so file inspection cannot settle this either way.

---

## Initial and boundary conditions
- **Initial conditions:** TBD (no independent AROME analysis documented; likely derived from the driving ALARO-1 — **flag**)
- **Boundary conditions:** LBC from **[ALARO (Poland)](./alaro-poland.md)** (ALARO-1, 4 km)

---

## What it provides
**(Verified)** Every file — at every step from `+000` to `+042` — carries exactly **55 GRIB messages**. The full inventory:

### Pressure levels — 100, 300, 500, 700, 850, **925** hPa (36 fields)
Six parameters on each of six levels — the same set ALARO publishes:

| Parameter | GRIB1 code | Units |
|---|---|---|
| U wind | 33 | m s⁻¹ |
| V wind | 34 | m s⁻¹ |
| Temperature | 11 | K |
| **Specific humidity** | 51 | kg kg⁻¹ |
| **Geopotential** | 6 | m² s⁻² |
| Relative humidity | 52 | % |

Field 6 is **geopotential**, not geopotential height — divide by 9.80665 for gpm (verified: 500 hPa spans 55196–57290 m² s⁻², i.e. 5628–5842 gpm).

### Surface and mean sea level (11 fields)
- **Mean sea level pressure** (2, Pa, `meanSea`)
- **Surface (skin) temperature** (11)
- **Model orography** (8, m) — a static field, bit-identical between `+000` and `+042`; range −10.87 m to 3097.51 m. (Compare ALARO's 2980.28 m maximum over a larger domain — the 2 km mesh resolves Alpine peaks less smoothly, as expected.)
- **Cloud cover, four fields:** total (71), high (75), medium (74), low (73). **There is no convective cloud cover field** (code 72), which ALARO does publish.
- **CAPE** (154, `SURFCAPE.MOD.XFU`) — **present in the file structure but identically zero at every step and every valid time inspected**, including an 18 UTC valid time where ALARO reported a domain maximum of 540 J kg⁻¹ over overlapping territory. Treat this field as unpopulated rather than as a forecast of zero CAPE. See *Decoding notes*.
- **Precipitation, three accumulations partitioned by hydrometeor phase:** accumulated rain (150, `SURFACCPLUIE`), accumulated snow (99, `SURFACCNEIGE`), accumulated graupel (29, `SURFACCGRAUPEL`). **There is no single total-precipitation field** — total precipitation is `150 + 99 + 29`.

### 2 m above ground (4 fields)
- 2 m temperature (11), 2 m relative humidity (52)
- **Minimum and maximum 2 m temperature** (16 and 15) over a rolling **3 h** window — see *Decoding notes*

### 10 m above ground (4 fields)
- 10 m U wind (33), 10 m V wind (34)
- **10 m wind gust, U and V components** (163 `CLSU.RAF.MOD.XFU`, 164 `CLSV.RAF.MOD.XFU`) — verified as gusts by construction: gust magnitude ≥ wind magnitude at 97.8–100 % of grid points depending on step (the sub-1 % shortfalls are consistent with independent interpolation of the two vector pairs onto the lat/lon mesh)

### How this differs from the ALARO subset
The two products are built from the same template but are **not** field-identical — 55 messages versus ALARO's 58. Two structural differences, both traceable to the convection treatment:

1. **No convective cloud cover** (72) in AROME.
2. **Precipitation is split by phase, not by mechanism.** ALARO, with parameterized deep convection, publishes four fields crossing mechanism with phase — large-scale water (62, `SURFPREC.EAU.GEC`), convective water (63, `SURFPREC.EAU.CON`), large-scale snow (79, `SURFPREC.NEI.GEC`), convective snow (78, `SURFPREC.NEI.CON`) — plus graupel. AROME, resolving convection explicitly, has no mechanism split to make and instead publishes the phase-partitioned microphysical surface fluxes directly: rain, snow, graupel.

### Decoding notes **(verified)**
- **Codes 154, 163 and 164 decode as "unknown" in stock ecCodes; code 29 decodes misleadingly as "Wave spectra (2)"; code 99 decodes as `snom` ("snow melt"), which is wrong — it is accumulated snowfall.** All are Météo-France local parameters. The files declare `table2Version = 1` while ecCodes files the corresponding concepts under other table versions in `grib1/localConcepts/lfpw/faFieldName.def`, so lookups fall through to the WMO table. Matching on centre + parameter number + level type resolves them to the native FA field names given above.
- **The CAPE field is a structural placeholder.** Code 154 is emitted in every file with the correct level and time coding but contains no data. Anyone consuming this feed for convective diagnostics needs to source CAPE elsewhere — which is an awkward gap given that a 2 km convection-permitting model is precisely where such a field would be most useful.
- **Precipitation accumulations run from the start of the forecast**, not per output interval — `P1 = 0` in every case, giving step ranges `0-2`, `0-6`, `0-21`, `0-42`. Consumers wanting hourly totals must difference consecutive files.
- **Snow (99) and graupel (29) were identically zero at every step inspected.** All samples are from early August, so this is not distinguishable from a correct seasonal zero; ALARO's equivalent fields were small but non-zero over the same period. Re-check in winter before concluding either way.
- **Min/max 2 m temperature is a rolling 3-hour statistic on hourly output**, so consecutive files overlap. ecCodes names codes 15/16 `mx2t6`/`mn2t6`, but `P1`/`P2` give `0-2` at `+002`, `3-6` at `+006`, `18-21` at `+021` and `39-42` at `+042` — a 3 h window, clipped at the run start, advancing one hour per file. Neither the "6 hours" in the shortName nor an assumption of non-overlapping periods is safe here.
- **Message order is not stable between steps.** The `+000` file orders fields differently from later steps; index by parameter and level, never by message number.

This is a limited subset of the full operational field set. Internally, AROME also produces hourly GRIB for the LEADS system and **10-minute output for the INCA nowcasting system**; neither the 10-minute output nor the additional fields are part of the public datastore subset.

---

## Data availability
- **Is the data free?** Yes
- **License:** No named license. IMGW-PIB does not apply CC BY, CC0 or any other standard instrument; the governing document is the portal's **Regulamin udostępniania danych**, which grounds itself in the Polish Act of 11 August 2021 on open data and the re-use of public sector information (consolidated text Dz. U. 2023 poz. 1524) and in **Commission Implementing Regulation (EU) 2023/138** establishing the list of high-value datasets. **(Verified 2026-08-07 against the live terms page** — this resolves the earlier flag asking whether the portal names a specific license: it does not.**)**

  §5 of the regulamin grants use of free data for private purposes and, **for high-value data, for any purpose** — matching IMGW-PIB's statement in correspondence WWS.381.295.2026/AKS (June 2026) that the model data are HVD and may be used "in any way."

  **Attribution is mandatory and the wording is prescribed.** Works or products built on IMGW-PIB data must carry: *"Źródłem pochodzenia danych jest Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy"* (the source of the data is IMGW-PIB). Where the data have been processed, a second statement is required alongside it: *"Dane Instytutu Meteorologii i Gospodarki Wodnej – Państwowego Instytutu Badawczego zostały przetworzone"* (IMGW-PIB data have been processed). The regulamin states that failure to attribute may carry liability, including criminal liability.

  (An IMGW-prepared product / format conversion / extract is a paid value-added service and does not affect the free raw GRIB.)
- **Is the data downloadable?** Yes
- **Data formats:** **GRIB edition 1 (verified)** — not GRIB2. `table2Version = 1`, `packingType = grid_simple`. File sizes are 55,084,620 B at `+000` and 57,244,620 B from `+002` onward (constant-valued fields pack at `bitsPerValue = 0`, so the analysis file is smaller).

  **Flag — originating centre is Météo-France, not IMGW.** Section 1 carries `centre = 84` (`lfpw`, Toulouse), `subCentre = 255`, `generatingProcessIdentifier = 133` — identical to ALARO. This is an artifact of the ALADIN/`gl` toolchain writing the consortium's centre code rather than the operator's. Anyone filtering an archive by originating centre will not find these files under a Polish code, and **AROME and ALARO are indistinguishable by centre and process ID** — they must be told apart by grid dimensions (600 × 600 vs 300 × 300) or by the product path.

  **Flag — no file extension.** Files are named `fc<YYYYMMDD>_<HH>+<step>gl` (e.g. `fc20260806_06+000gl` … `fc20260806_06+042gl`). The trailing `gl` denotes the **HARMONIE `gl` postprocessing tool** (Andrae, SMHI; `util/gl` in the HARMONIE tree), which converts the model's native FA output to GRIB and assembles the IO-server's distributed files into a single GRIB file — i.e. the suffix marks these as gl's GRIB product (as opposed to the `fullpos`/e927 path). This is inferred from the tool's documented role; IMGW has not stated the naming convention explicitly. The FA field names recovered from the local parameter codes (`SURFACCPLUIE`, `SURFACCNEIGE`, `CLSU.RAF.MOD.XFU`) corroborate an FA → GRIB1 conversion path.
- **Official download location:**  
  https://danepubliczne.imgw.pl/pl/datastore?product=AROME_pub
- **Direct file URL pattern (verified):**  
  `https://danepubliczne.imgw.pl/{pl|en}/datastore/getfiledown/Oper/ALADIN/AROME_pub/fc<YYYYMMDD>_<HH>+<NNN>gl`  
  Both language prefixes serve the same bytes. Missing steps and cycles return a plain Apache 404.

---

## Notes
- **Public vs. operational differences.** Only two reductions, not three: the public product is **regridded and cropped in space** (600 × 600 regular lat/lon over ~1220 × 1120 km, versus 799 × 799 Lambert over ~1600 km) and **reduced in field content**. It is **not** downsampled in time and **not** truncated in range — both hourly output and the full 42 h operational range are published. This is a materially more complete public feed than [ALARO](./alaro-poland.md)'s, which loses two-thirds of its domain, two-thirds of its time steps, and its final step.
- **Retention and publication behaviour — actively rolling, with substantial lag.** Observed over a 20-minute window on 2026-08-07 (04:00–04:20 UTC): the feed holds roughly **four to five cycles (~24–30 h)** and purges older ones during the observation window (the 2026-08-05 12 UTC cycle disappeared between two probes minutes apart). Newly published cycles arrive in bursts rather than steadily — the 2026-08-06 12 UTC cycle appeared for both products between 03:59 and 04:13 UTC, i.e. roughly **16 h after its nominal cycle time**. Anyone building a harvester should poll frequently and not assume a fixed publication offset. **Repeat sampling over several days is needed** before treating these figures as settled.
- **Individual cycles can be missing.** The ALARO 2026-08-06 06 UTC cycle was absent while both its neighbours (06 UTC ± 6 h) *and* the corresponding AROME cycle were present — so the run itself happened and only ALARO's public files failed to appear. Gaps are per-product and should be expected.
- **Parent model:** AROME (Poland) is nested in [ALARO (Poland)](./alaro-poland.md), which provides its lateral boundary conditions — the analogue of the AROME ← ALARO chains elsewhere in the ACCORD consortium.
- **Public-data transition:** IMGW-PIB stated (June 2026) that it is changing its public model data **from COSMO to ICON-LAM**; ALARO and AROME GRIB are the products currently available. COSMO (being phased out) and ICON-LAM (not yet confirmed as openly downloadable) are candidate future entries, out of scope until a free feed is confirmed.
- **Consortium / sibling systems:** developed within the **ACCORD** consortium. Closely related AROME / HARMONIE-AROME deployments include [AROME France](../france/arome-france.md), [AROME Austria](../austria/arome-austria.md), and [AROME Hungary](../hungary/arome-hungary.md).
- **Nowcasting link:** AROME feeds IMGW's INCA nowcasting system (10-minute output); if INCA is later documented as a nowcasting entry, cross-link from there.

---

## Official documentation
- IMGW-PIB public data portal (datastore): https://danepubliczne.imgw.pl/pl/datastore
- IMGW-PIB meteorological models portal: https://modele.imgw.pl
- IMGW-PIB: https://www.imgw.pl

### Key references
- Sekuła, P., Bochenek, B., Kolonko, M., Szczęch-Gajewska, M., Stachura, G. (2022). *ALARO experience in Poland.* RC LACE / ACCORD, Prague, 13–15 June 2022.
- Sekuła, P., Stachura, G., Szczęch-Gajewska, M., Bochenek, B., Kolonko, M., Szopa, N. (2023). *NWP in Poland.* 3rd ACCORD All-Staff Workshop, Tallinn, 27–31 March 2023.
- Stachura, G., Kolonko, M., Róg, J., Sekuła, P., Szczęch-Gajewska, M., Szopa, N., Bochenek, B. (2024). *NWP in Poland.* 4th ACCORD All-Staff Workshop, Norrköping, 15–19 April 2024.
- Kolonko, M., Róg, J., Sekuła, P., Stachura, G., Szopa, N., Szczęch-Gajewska, M., Bochenek, B. (2025). *NWP in Poland.* 47th EWGLAM & 32nd SRNWP Meeting, Norrköping, 22–26 September 2025.
- Kolonko, M., Szczęch-Gajewska, M., Bochenek, B., Stachura, G., Sekuła, P. (2023). *Using ALARO and AROME numerical weather prediction models for the derecho case on 11 August 2017.* Meteorol. Hydrol. Water Manage., 10, 88–105. https://doi.org/10.26491/mhwm/156260
