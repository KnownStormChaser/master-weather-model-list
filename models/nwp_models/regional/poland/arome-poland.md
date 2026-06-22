# AROME (IMGW-PIB, Poland)

## What this model is
AROME is the operational convection-permitting regional deterministic numerical weather prediction model run by IMGW-PIB over Poland and surrounding Central Europe. It runs the AROME (ALADIN-NH) system at 2 km and is nested inside the coarser [ALARO (Poland)](./alaro-poland.md) model, which supplies its lateral boundary conditions.

A subset of the model output is published as GRIB through IMGW-PIB's public data portal. This entry documents that public product (`AROME_pub`); the full operational output is larger than what is distributed publicly (see Notes).

---

## Who runs it
- **Organization:** Instytut Meteorologii i Gospodarki Wodnej – Państwowy Instytut Badawczy (IMGW-PIB)
- **Country / region:** Poland

---

## What area it covers
- **Coverage:** Poland and surrounding parts of Central Europe
- **Operational domain:** **P020**
  - Lambert conformal projection
  - 799 × 799 grid points at 2 km (≈ 1600 km across)

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** AROME (spectral limited-area model, ALADIN-NH dynamical core)
- **Code version:** CY43T2 (operational)
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes (2 km grid; deep convection explicitly resolved)
- **Horizontal resolution:** 2 km
- **Grid dimensions:** 799 × 799
- **Vertical levels:** 70
- **Forecast length:** 42 h (consistent across the IMGW product description, the `+000gl … +042gl` file range, and the 2025 EWGLAM poster)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly (public product and operational output both hourly)
- **Time step:** 50 s
- **Coupling:** LBC from ALARO-1 (4 km). **Flag:** the coupling frequency is given as **3 h** in the 2022, 2023 and 2025 posters but as **1 h** in the 2024 ACCORD poster — treat as 3 h pending confirmation.

---

## Data assimilation
- **Data assimilation:** None in operations as of the source material — AROME (Poland) is effectively a high-resolution dynamical downscaling of ALARO. (IMGW notes AROME data-assimilation studies as a future need; no operational AROME DA is documented.) **Flag for verification.**

---

## Initial and boundary conditions
- **Initial conditions:** TBD (no independent AROME analysis documented; likely derived from the driving ALARO-1 — **flag**)
- **Boundary conditions:** LBC from **[ALARO (Poland)](./alaro-poland.md)** (ALARO-1, 4 km)

---

## What it provides
The **publicly distributed** GRIB subset contains:

- **Pressure levels (100, 300, 500, 700, 850, 950 hPa):** temperature, geopotential height, U wind, V wind, relative humidity
- **Surface:** mean sea-level pressure, total precipitation, total cloud cover
- **2 m above ground:** 2 m temperature, 2 m relative humidity
- **10 m above ground:** 10 m U wind, 10 m V wind

This is a limited subset of the full operational field set. Internally, AROME also produces hourly GRIB for the LEADS system and **10-minute output for the INCA nowcasting system**; neither the 10-minute output nor the additional fields are part of the public datastore subset.

---

## Data availability
- **Is the data free?** Yes
- **License:** IMGW-PIB confirmed (correspondence WWS.381.295.2026/AKS, June 2026) that the model data on the public datastore are **HVD (High Value Datasets)** and may be used "in any way," with a **mandatory obligation to cite the source — including for processed/derived data**. IMGW-PIB does **not** cite a specific named license (e.g. CC BY 4.0) in this correspondence; the terms are open-reuse-with-attribution. **Flag:** confirm whether the portal's terms page names a specific license. (An IMGW-prepared product/format conversion/extract is a paid value-added service and does not affect the free raw GRIB.)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB. **Flag:** files carry **no standard file extension** — they are named `fc<YYYYMMDD>_<HH>+<step>gl` (e.g. `fc20260609_18+000gl` … `fc20260609_18+042gl`). The trailing `gl` denotes the **HARMONIE `gl` postprocessing tool** (Andrae, SMHI; `util/gl` in the HARMONIE tree), which converts the model's native FA output to GRIB and assembles the IO-server's distributed files into a single GRIB file — i.e. the suffix marks these as gl's GRIB product (as opposed to the `fullpos`/e927 path). This is inferred from the tool's documented role; IMGW has not stated the naming convention explicitly. IMGW confirmed the format is GRIB.
- **Official download location:**  
  https://danepubliczne.imgw.pl/pl/datastore?product=AROME_pub

---

## Notes
- **Parent model:** AROME (Poland) is nested in [ALARO (Poland)](./alaro-poland.md), which provides its lateral boundary conditions — the analogue of the AROME ← ALARO chains elsewhere in the ACCORD consortium.
- **Public vs. operational:** the public GRIB is a subset of fields/levels; the 10-minute INCA output and LEADS-chain products are not publicly distributed here.
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
