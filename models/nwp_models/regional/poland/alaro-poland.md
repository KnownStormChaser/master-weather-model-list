# ALARO (IMGW-PIB, Poland)

## What this model is
ALARO is the operational mesoscale deterministic limited-area model run by IMGW-PIB over a European domain centred on Poland. It runs the ALADIN system with **ALARO-1vB** physics in its non-hydrostatic configuration ("ALARO-v1B NH") at 4 km, providing synoptic-to-mesoscale guidance and the lateral boundary conditions for the higher-resolution [AROME (Poland)](./arome-poland.md) nest.

A subset of the model output is published as GRIB through IMGW-PIB's public data portal. This entry documents that public product (`ALARO_pub`); the full operational output is larger than what is distributed publicly (see Notes).

---

## Who runs it
- **Organization:** Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy (IMGW-PIB)
- **Country / region:** Poland

---

## What area it covers
- **Coverage:** Poland and a large part of the European continent
- **Operational domain:** **E040**
  - Lambert conformal projection
  - 789 × 789 grid points at 4 km (≈ 3150 km across)
  - Coupling zone of 16 points

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN — **ALARO-1vB** canonical model configuration
- **Code version:** CY43T2 (operational)
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** No (4 km grid; deep convection is parameterized by the ALARO gray-zone physics)
- **Horizontal resolution:** 4 km
- **Grid dimensions:** 789 × 789
- **Vertical levels:** 70
- **Forecast length:** **99 h in the public product** (IMGW product description and the `+000gl … +099gl` file range); the operational forecast range is documented as **102 h** in the 2025 EWGLAM poster — i.e. the public distribution appears to stop one or two steps short of the full run
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** **3-hourly in the public product**; full operational output is hourly (the public GRIB is downsampled — see Notes)
- **Time step:** 150 s

---

## Data assimilation
- **Data assimilation:** Pre-operational (surface only) as of the 2025 EWGLAM poster
- **Method / cadence:** **CANARI** surface assimilation on a 6-hour cycle. Earlier (2024) this was described as a test-mode CANARI surface analysis within CY43T2 using SYNOP and partially automatic-station data via the OPLACE database. Whether it is in the publicly distributed product yet is unconfirmed — **flag for verification**.

---

## Initial and boundary conditions
- **Initial conditions:** TBD (not stated explicitly in the source posters)
- **Boundary conditions:** LBC from **[ARPEGE](../../global/france/arpege-global.md)** at 9.4 km, 3-hour coupling frequency

---

## What it provides
The **publicly distributed** GRIB subset contains:

- **Pressure levels (100, 300, 500, 700, 850, 950 hPa):** temperature, geopotential height, U wind, V wind, relative humidity
- **Surface:** mean sea-level pressure, total precipitation, total cloud cover
- **2 m above ground:** 2 m temperature, 2 m relative humidity
- **10 m above ground:** 10 m U wind, 10 m V wind

This is a limited subset of the full operational field set (the full output feeds internal IMGW systems such as the hourly LEADS chain).

---

## Data availability
- **Is the data free?** Yes
- **License:** IMGW-PIB confirmed (correspondence WWS.381.295.2026/AKS, June 2026) that the model data on the public datastore are **HVD (High Value Datasets)** and may be used "in any way," with a **mandatory obligation to cite the source — including for processed/derived data**. IMGW-PIB does **not** cite a specific named license (e.g. CC BY 4.0) in this correspondence; the terms are open-reuse-with-attribution. **Flag:** confirm whether the portal's terms page names a specific license. (Note: an IMGW-prepared *opracowanie* / format conversion / extract is a paid value-added service; this does **not** affect the free raw GRIB.)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB. **Flag:** files carry **no standard file extension** — they are named `fc<YYYYMMDD>_<HH>+<step>gl` (e.g. `fc20260609_18+000gl` … `fc20260609_18+099gl`). The trailing `gl` denotes the **HARMONIE `gl` postprocessing tool** (Andrae, SMHI; `util/gl` in the HARMONIE tree), which converts the model's native FA output to GRIB and assembles the IO-server's distributed files into a single GRIB file — i.e. the suffix marks these as gl's GRIB product (as opposed to the `fullpos`/e927 path). This is inferred from the tool's documented role; IMGW has not stated the naming convention explicitly. IMGW confirmed the format is GRIB.
- **Official download location:**  
  https://danepubliczne.imgw.pl/pl/datastore?product=ALARO_pub

---

## Notes
- **Public vs. operational differences:** the public product is downsampled to 3-hourly output (operational output is hourly) and stops at +99 h (operational range 102 h). Confirm whether a +102 h step is also published.
- **AROME nest:** ALARO provides the lateral boundary conditions for [AROME (Poland)](./arome-poland.md) (LBC from ALARO-1 4 km).
- **Public-data transition:** IMGW-PIB stated (June 2026) that it is changing its public model data **from COSMO to ICON-LAM**. At present ALARO and AROME GRIB are the available products. COSMO is being phased out; ICON-LAM is not yet confirmed as openly downloadable — both are candidate future entries and out of scope until a free feed is confirmed.
- **Pre-operational code:** preoperational tests of **ALARO CY46T1** (export version) run daily at 2.45 km horizontal resolution, 90 s time step, 70 levels — not the publicly distributed product.
- **Consortium context:** developed within the **ACCORD** consortium (and **RC LACE**). The ACCORD/LACE common ensemble **A-LAEF** is built on ALARO-1vB physics but is run at ECMWF under SHMÚ and is a separate system. See related ALARO/ALADIN entries: [ALARO Belgium](../belgium/alaro-belgium.md), [ALADIN Slovakia](../slovakia/aladin-slovakia.md).

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
