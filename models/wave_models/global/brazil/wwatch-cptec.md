# WWATCH (CPTEC/INPE Global Wave Model)

## What this model is
WWATCH is CPTEC/INPE's operational global ocean wave forecast system, based on the third-generation spectral wave model WAVEWATCH III (WW3). It produces deterministic forecasts of integrated wave parameters and partitioned wind-sea/swell fields to support marine and coastal forecasting.

---

## Who runs it
- **Organization:** CPTEC/INPE (Center for Weather Forecasting and Climate Studies / National Institute for Space Research)
- **Country / region:** Brazil

---

## What area it covers
- **Coverage:** Global oceans
- **Domain details:** Regular latitude–longitude grid, 1440 × 629 points at 0.25°; longitude 0–359.75°, latitude 78.5°S–78.5°N (quasi-global, excluding the high polar caps).

---

## Basic details
- **Model type:** Deterministic global wave model
- **Core wave model:** WAVEWATCH III (WW3), version 5.16
- **Horizontal resolution:** 0.25° (`gl_0p25`)
- **Forecast length:** +120 h (5 days)
- **Update frequency / cycles:** 1× daily (00 UTC)
- **Temporal output resolution:** 3-hourly (41 timesteps per run, +0 h to +120 h)

---

## Forcing and nesting
- **Wind forcing:** 10 m winds embedded in the output (`uwnd`, `vwnd`); source is CPTEC's atmospheric model, plausibly [BAM](../../../nwp_models/global/brazil/bam-cptec.md) — unconfirmed for the public feed (TBD).
- **Ice forcing:** Sea-ice concentration is applied (`ice` field present); source TBD.
- **Current forcing:** None documented (TBD).

---

## Data assimilation (optional)
- **Assimilates wave observations:** TBD — no wave data assimilation is documented; the system appears to run as a wind-forced forecast.

---

## What it provides
A single GrADS binary file per run carries all timesteps with 39 variables:

### Integrated wave parameters
- Significant wave height (`hs`)
- Mean wavelength (`lm`)
- Mean periods: zero-crossing Tz (`t02`), mean Tm (`t01`), energy period Te (`t0m1`)
- Peak frequency (`fp`) and peak direction (`dp`)
- Mean direction (`dir`) and directional spread (`spr`)
- Total wind-sea fraction (`tws`)

### Partitioned fields
- Six spectral partitions (`00`–`05`; partition 0 typically wind-sea, 1–5 swells), each with significant height (`phs`), peak period (`ptp`), mean wavelength (`plp`), and direction (`pdi`)

### Forcing / static fields
- 10 m wind components (`uwnd`, `vwnd`)
- Sea-ice concentration (`ice`)
- Water depth (`dpt`) and grid-use map (`MAP`)

---

## Data availability
- **Is the data free?** Yes — free of charge, no registration.
- **License:** **Transitional / not yet an open license.** Data is freely accessible and usable personally today, but CPTEC/INPE's operational-server notice restricts commercial use and redistribution in published/dissemination outlets without express CPTEC/INPE authorization, and requires attribution to "CPTEC/INPE." INPE has committed under its Open Data Plan (PDA 2025–2027, Decreto 8.777/2016) to republish the wave data ("WAVE") as open data on dados.gov.br, scheduled ~June 2027. As of late June 2026 it is not yet live on the dados.gov.br "Tempo e Clima" category; open reuse terms apply once it appears there.
- **Is the data downloadable?** Yes
- **Data formats:** GrADS binary — one `.dat` data file plus its `.ctl` descriptor per run (sequential, big-endian). Requires GrADS-compatible tooling (GrADS/OpenGrADS, or the `.ctl` read via xgrads/pygrads) rather than a GRIB/NetCDF reader.
- **Official download location:**
  https://dataserver.cptec.inpe.br/dataserver_modelos/wwatch/ww3_v5.16/gl_0p25/brutos/
  - `<YYYY>/<MM>/<DD>/00/` — files `WW_<init>.dat` and `WW_<init>.ctl` (`<init>` = `YYYYMMDDHH`)

---

## Notes
- **One file per run:** Unlike the per-timestep GRIB layout of CPTEC's atmospheric models, each WWATCH run ships as a single `.dat` containing all 41 timesteps, described by one `.ctl`.
- **Embedded forcing:** The forcing winds and ice concentration are included as output fields, which is convenient for verification and drift applications.
- **Format caveat:** GrADS binary is structured gridded data but less universal than GRIB2 — flag for users expecting a standard wave-product format.
- **Versioned path:** The server path encodes the WW3 version and grid (`ww3_v5.16/gl_0p25`), so future version or grid changes would appear as sibling directories; only this global 0.25° v5.16 product is currently present.
- **Older data:** Only recent data is served operationally; older data requires a request to CPTEC and is subject to availability.

---

## Official documentation
- CPTEC/INPE model data server — https://dataserver.cptec.inpe.br/dataserver_modelos/wwatch/
- CPTEC/INPE — https://www.cptec.inpe.br/
- gov.br service page (PNT) — https://www.gov.br/pt-br/servicos/obter-dados-provenientes-de-modelos-numericos-de-previsao-de-tempo-inpe-pnt
