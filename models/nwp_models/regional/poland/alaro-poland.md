# ALARO (IMGW-PIB, Poland)

## What this model is
ALARO is the operational mesoscale deterministic limited-area model run by IMGW-PIB over a European domain centred on Poland. It runs the ALADIN system with **ALARO-1vB** physics in its non-hydrostatic configuration ("ALARO-v1B NH") at 4 km, providing synoptic-to-mesoscale guidance and the lateral boundary conditions for the higher-resolution [AROME (Poland)](./arome-poland.md) nest.

A subset of the model output is published as GRIB through IMGW-PIB's public data portal. This entry documents that public product (`ALARO_pub`); the full operational output is larger than what is distributed publicly, and the public files are **regridded and spatially cropped** relative to the operational domain (see *What area it covers* and Notes).

> **Live-verified 2026-08-07** against the `ALARO_pub` datastore using ecCodes 2.48.0 on the `fc20260805_18+000gl`, `fc20260806_00+000gl`, `fc20260806_00+003gl`, `fc20260806_00+048gl` and `fc20260806_00+099gl` files, plus HTTP probing of the step and cycle space. Fields marked **(verified)** below were read from the GRIB headers rather than taken from documentation.

---

## Who runs it
- **Organization:** Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy (IMGW-PIB)
- **Country / region:** Poland

---

## What area it covers
- **Coverage:** Poland and a large part of the European continent (operational); Poland and immediate neighbours (public product)

### Operational domain — **E040**
- Lambert conformal projection
- 789 × 789 grid points at 4 km (≈ 3150 km across)
- Coupling zone of 16 points

### Public product grid **(verified)**
The distributed files are **not** on the operational Lambert grid. They are interpolated to a plain geographic grid and cropped:

- `gridType = regular_ll` (GRIB1 data representation type 0), spherical earth
- **300 × 300 = 90,000 points**, identical on every message of every file
- Corners: **47.127 N / 8.690 E** (first point) to **57.873 N / 26.310 E** (last point)
- Increments: **0.059° longitude × 0.036° latitude**
- `jScansPositively = 1` (south → north), `iScansNegatively = 0`
- Spacing ≈ **4.0 km** at the domain's mid-latitude, matching the native resolution, but longitudinal spacing varies with latitude across the box (≈ 4.5 km at 47 N to ≈ 3.5 km at 57.9 N) as it must on a regular lat/lon grid
- Extent ≈ **1196 km N–S × 1194 km E–W**, i.e. roughly **38 % of the operational domain's linear extent**

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN — **ALARO-1vB** canonical model configuration
- **Code version:** CY43T2 (operational)
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** No (4 km grid; deep convection is parameterized by the ALARO gray-zone physics)
- **Horizontal resolution:** 4 km (operational and public product alike; see the grid note above for how this maps onto the public lat/lon mesh)
- **Grid dimensions:** 789 × 789 operational; **300 × 300 in the public product (verified)**
- **Vertical levels:** 70 (model levels; the public GRIB carries pressure levels and single-level fields only — no model-level output)
- **Forecast length:** **99 h in the public product (verified)** — `fc<date>_<cycle>+102gl` returns HTTP 404 on the datastore, so the feed genuinely stops one 3-hourly step short of the 102 h operational range. The **operational range is 102 h**, confirmed in both the 47th EWGLAM (2025) and 6th ASW (April 2026) posters. *(It was 72 h in every poster from 2020 through the 46th EWGLAM Sept 2024, extended to 102 h by Sept 2025.)*
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC). Consistent with the live feed, though individual cycles do go missing — see *Retention and publication behaviour* under Notes.
- **Temporal output resolution:** **3-hourly in the public product (verified**: `+000`, `+003`, … `+099`, 34 files per cycle**)**; full operational output is hourly (the public GRIB is downsampled — see Notes)
- **Time step:** 150 s

---

## Data assimilation
- **Data assimilation:** Pre-operational (surface only) — still described as *pre-operational* in the most recent (6th ASW, April 2026) poster
- **Method / cadence:** **CANARI** surface assimilation on a 6-hour cycle. Earlier (2024) this was described as a test-mode CANARI surface analysis within CY43T2 using SYNOP and partially automatic-station data via the OPLACE database. The "pre-operational" wording persisted unchanged from 2024 through April 2026, which suggests the surface DA is **not yet in the operational (hence public) product** — **flag for verification**. The GRIB headers carry no assimilation indicator, so file inspection cannot settle this.

---

## Initial and boundary conditions
- **Initial conditions:** TBD (not stated explicitly in the source posters)
- **Boundary conditions:** LBC from **[ARPEGE](../../global/france/arpege-global.md)** at 9.4 km, 3-hour coupling frequency

---

## What it provides
**(Verified)** Every file — at every step from `+000` to `+099` — carries exactly **58 GRIB messages**. The full inventory:

### Pressure levels — 100, 300, 500, 700, 850, **925** hPa (36 fields)
Six parameters on each of six levels:

| Parameter | GRIB1 code | Units |
|---|---|---|
| U wind | 33 | m s⁻¹ |
| V wind | 34 | m s⁻¹ |
| Temperature | 11 | K |
| **Specific humidity** | 51 | kg kg⁻¹ |
| **Geopotential** | 6 | m² s⁻² |
| Relative humidity | 52 | % |

Note that field 6 is **geopotential**, not geopotential height — divide by 9.80665 for gpm (verified: 500 hPa spans 54707–57519 m² s⁻², i.e. 5579–5865 gpm).

### Surface and mean sea level (14 fields)
- **Mean sea level pressure** (2, Pa, `meanSea`)
- **Surface (skin) temperature** (11)
- **Model orography** (8, m) — a static field, bit-identical between `+000` and `+099` and between separate cycles; range −8.99 m to 2980.28 m
- **Cloud cover, five fields:** total (71), convective (72), high (75), medium (74), low (73)
- **CAPE** (154, `SURFCAPE.MOD.XFU`) — a Météo-France local code; see the decoding note below
- **Precipitation, four separate accumulations crossing mechanism with phase:** large-scale rain (62, `SURFPREC.EAU.GEC`), convective rain (63, `SURFPREC.EAU.CON`), large-scale snow (79, `SURFPREC.NEI.GEC`), convective snow (78, `SURFPREC.NEI.CON`). **There is no single total-precipitation field** — total precipitation is `62 + 63 + 79 + 78`, and `62 + 63` alone gives rain only. Note that ecCodes' WMO names for 62 and 63 ("Large-scale precipitation", "Convective precipitation (water)") invite exactly this mistake; the FA names disambiguate them.
- **Accumulated graupel** (29, `SURFACCGRAUPEL`) — zero in the analysis, reaching 0.24 kg m⁻² by `+099` over the highest Alpine terrain in the domain corner

### 2 m above ground (4 fields)
- 2 m temperature (11), 2 m relative humidity (52)
- **Minimum and maximum 2 m temperature** (16 and 15) over the preceding **3 h** — see the decoding note below

### 10 m above ground (4 fields)
- 10 m U wind (33), 10 m V wind (34)
- **10 m wind gust, U and V components** (163 `CLSU.RAF.MOD.XFU`, 164 `CLSV.RAF.MOD.XFU`) — verified as gusts by construction: gust magnitude ≥ wind magnitude at 100 % of grid points at `+000` and 99.9 % at `+099`

### Decoding notes **(verified)**
- **Codes 154, 163 and 164 decode as "unknown" in stock ecCodes**, and **code 29 decodes misleadingly as "Wave spectra (2)"**. All four are Météo-France local parameters. The files declare `table2Version = 1` (the WMO table) while ecCodes files the corresponding concepts under `table2Version = 159` in `grib1/localConcepts/lfpw/faFieldName.def`, so the lookup falls through to the WMO table. Matching on centre + parameter number + level type + level resolves them to the native FA field names given above; the CAPE identification is independently corroborated by its diurnal cycle (domain maximum 540 J kg⁻¹ at 18 UTC, 136 at 00 UTC, 69 at 03 UTC).
- **Precipitation and snow accumulations run from the start of the forecast**, not per output interval — `P1 = 0` in every case, giving step ranges `0-3`, `0-48`, `0-99`. Consumers wanting 3-hourly totals must difference consecutive files. **Accumulated graupel (29) is the exception:** the lfpw concept defines it with `timeRangeIndicator = 4`, but the files encode it with `timeRangeIndicator = 0` and a point step, so it carries no explicit accumulation range despite being an accumulated quantity.
- **Min/max 2 m temperature is a 3-hour statistic, not 6-hour.** ecCodes names codes 15/16 `mx2t6`/`mn2t6`, but `P1`/`P2` give windows of `45-48` and `96-99` — one output interval. The shortName is misleading.
- **Message order is not stable between steps.** The `+000` file orders fields differently from `+003` onward; index by parameter and level, never by message number.

This is a limited subset of the full operational field set (the full output feeds internal IMGW systems such as the hourly LEADS chain), though it is richer than a pressure-level-plus-basics subset — gusts, CAPE, layered cloud, hydrometeor-partitioned precipitation and min/max temperature are all present.

---

## Data availability
- **Is the data free?** Yes
- **License:** No named license. IMGW-PIB does not apply CC BY, CC0 or any other standard instrument; the governing document is the portal's **Regulamin udostępniania danych**, which grounds itself in the Polish Act of 11 August 2021 on open data and the re-use of public sector information (consolidated text Dz. U. 2023 poz. 1524) and in **Commission Implementing Regulation (EU) 2023/138** establishing the list of high-value datasets. **(Verified 2026-08-07 against the live terms page** — this resolves the earlier flag asking whether the portal names a specific license: it does not.**)**

  §5 of the regulamin grants use of free data for private purposes and, **for high-value data, for any purpose** — matching IMGW-PIB's statement in correspondence WWS.381.295.2026/AKS (June 2026) that the model data are HVD and may be used "in any way."

  **Attribution is mandatory and the wording is prescribed.** Works or products built on IMGW-PIB data must carry: *"Źródłem pochodzenia danych jest Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy"* (the source of the data is IMGW-PIB). Where the data have been processed, a second statement is required alongside it: *"Dane Instytutu Meteorologii i Gospodarki Wodnej – Państwowego Instytutu Badawczego zostały przetworzone"* (IMGW-PIB data have been processed). The regulamin states that failure to attribute may carry liability, including criminal liability.

  (An IMGW-prepared *opracowanie* / format conversion / extract is a paid value-added service; this does **not** affect the free raw GRIB.)
- **Is the data downloadable?** Yes
- **Data formats:** **GRIB edition 1 (verified)** — not GRIB2. `table2Version = 1`, `packingType = grid_simple`, `bitsPerValue = 24` for ordinary fields and 0 for constant-valued ones (which is why early steps are smaller: 14,044,872 B at `+000`, 14,854,872 B at `+003`, 15,664,872 B at `+048` and `+099`).

  **Flag — originating centre is Météo-France, not IMGW.** Section 1 carries `centre = 84` (`lfpw`, Toulouse), `subCentre = 255`, `generatingProcessIdentifier = 133`. This is an artifact of the ALADIN/`gl` toolchain writing the consortium's centre code rather than the operator's. Anyone filtering an archive by originating centre will not find these files under a Polish code.

  **Flag — no file extension.** Files are named `fc<YYYYMMDD>_<HH>+<step>gl` (e.g. `fc20260806_00+000gl` … `fc20260806_00+099gl`). The trailing `gl` denotes the **HARMONIE `gl` postprocessing tool** (Andrae, SMHI; `util/gl` in the HARMONIE tree), which converts the model's native FA output to GRIB and assembles the IO-server's distributed files into a single GRIB file — i.e. the suffix marks these as gl's GRIB product (as opposed to the `fullpos`/e927 path). This is inferred from the tool's documented role; IMGW has not stated the naming convention explicitly. The FA field names recovered from the local parameter codes (`SURFCAPE.MOD.XFU`, `CLSU.RAF.MOD.XFU`, `SURFACCGRAUPEL`) corroborate an FA → GRIB1 conversion path.
- **Official download location:**  
  https://danepubliczne.imgw.pl/pl/datastore?product=ALARO_pub
- **Direct file URL pattern (verified):**  
  `https://danepubliczne.imgw.pl/{pl|en}/datastore/getfiledown/Oper/ALADIN/ALARO_pub/fc<YYYYMMDD>_<HH>+<NNN>gl`  
  Both language prefixes serve the same bytes. Missing steps and cycles return a plain Apache 404.

---

## Notes
- **Public vs. operational differences.** Three separate reductions, not one: the public product is **downsampled in time** (3-hourly vs hourly operational), **truncated in range** (+099 vs +102 operational), and **regridded and cropped in space** (300 × 300 regular lat/lon over ~1196 km vs 789 × 789 Lambert over ~3150 km). The spatial reduction is the largest of the three and was not previously documented here.
- **Retention and publication behaviour — actively rolling, with substantial lag.** Observed over a 20-minute window on 2026-08-07 (04:00–04:20 UTC): the feed holds roughly **three to five cycles (~24–30 h)** and purges older ones during the observation window (the 2026-08-05 12 UTC cycle disappeared between two probes minutes apart). Newly published cycles arrive in bursts rather than steadily — the 2026-08-06 12 UTC cycle appeared between 03:59 and 04:13 UTC, i.e. roughly **16 h after its nominal cycle time**. Anyone building a harvester should poll frequently and not assume a fixed publication offset. **Repeat sampling over several days is needed** before treating these figures as settled.
- **Individual cycles can be missing.** The 2026-08-06 06 UTC ALARO cycle never appeared, while both neighbouring ALARO cycles *and* the corresponding [AROME](./arome-poland.md) 06 UTC cycle were present — so the run itself happened and only ALARO's public files failed to publish. Gaps are per-product and should be expected. This also settles the question the earlier snapshot left open: the 4× daily cadence in the posters is not contradicted by the feed.
- **`Dane archiwalne` tab.** The datastore front page exposes an archive tab alongside `Dane operacyjne`. Not yet examined for ALARO; if it carries older cycles it would materially change the retention picture above.
- **AROME nest:** ALARO provides the lateral boundary conditions for [AROME (Poland)](./arome-poland.md) (LBC from ALARO-1 4 km). The AROME public product has now been verified independently: same GRIB1 encoding, same Météo-France originating centre, same pressure-level set, but **55 messages rather than 58** on a **600 × 600** grid. It lacks convective cloud cover and splits precipitation by hydrometeor phase (rain / snow / graupel) rather than by mechanism × phase, both consequences of explicit convection at 2 km. Its public feed is also less reduced than ALARO's — full hourly output and the full operational range, with only the spatial crop applied.
- **Public-data transition:** IMGW-PIB stated (June 2026) that it is changing its public model data **from COSMO to ICON-LAM**. At present ALARO and AROME GRIB are the available products. COSMO is being phased out; ICON-LAM is not yet confirmed as openly downloadable — both are candidate future entries and out of scope until a free feed is confirmed.
- **Pre-operational code (cycle upgrade):** an **ALARO CY46T1** export version (four LACE/Prague code packages) has run daily since Feb 2024 at **2.45 km**, 90 s time step, 70 levels (later possibly more). As of the 6th ASW (April 2026) poster its verification has been running **in operational mode since the beginning of 2026** (one-month comparison 23 Feb–24 Mar 2026 against the operational 4 km CY43T2). It is **not yet the publicly distributed product** — the public feed remains CY43T2 4 km, consistent with the ~4 km public grid spacing verified above — but it is the clearest candidate to watch for a future resolution/cycle change. **Flag:** the 2026 poster gives this resolution as both **2.45 km** and **2.4 km** in different panels.
- **Consortium context:** developed within the **ACCORD** consortium (and **RC LACE**). The ACCORD/LACE common ensemble **A-LAEF** is built on ALARO-1vB physics but is run at ECMWF under SHMÚ and is a separate system. See related ALARO/ALADIN entries: [ALARO Belgium](../belgium/alaro-belgium.md), [ALADIN Slovakia](../slovakia/aladin-slovakia.md).

---

## Recent configuration history (from IMGW ACCORD/EWGLAM posters)
- **2020–April 2021:** 4 km, coupling zone **8 points**, **72 h** range.
- **Sept 2021:** coupling zone widened to **16 points**.
- **Feb 2024–:** daily pre-operational **CY46T1** export tests at 2.45 km / 90 s.
- **By Sept 2025:** operational range extended **72 h → 102 h**.
- **Since early 2026:** CY46T1 (2.45 km) verification run in operational mode; CY43T2 4 km remains the distributed product.

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
