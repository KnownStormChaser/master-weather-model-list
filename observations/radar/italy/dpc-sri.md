# DPC SRI (Italian National Radar Composite — Surface Rainfall Intensity)

## What this is
The Surface Rainfall Intensity (SRI) product is the ground-level precipitation
estimate from Italy's national weather-radar mosaic, produced by the Dipartimento
della Protezione Civile (DPC). It is a gridded, observational precipitation
product (mm/h) covering the Italian peninsula, major islands, and surrounding
seas at 1 km resolution, updated every 5 minutes in the current configuration.

This entry exists because **Italy is not a member of EUMETNET OPERA**, so the
pan-European OPERA composite has a gap over Italian territory. The DPC SRI mosaic
fills that gap and is technically compatible with OPERA products, so the two can
be combined for complete coverage of continental Europe. The product is available
both as an operational live feed (from the DPC's own platform and via MeteoHub)
and as a harmonized 16-year Analysis-Ready Cloud-Optimized (ARCO) Zarr archive
covering 2010 to present.

---

## Who operates it
- **Operator / coordinating programme:** The national radar network is coordinated by the **Dipartimento della Protezione Civile (DPC)**, under the Presidenza del Consiglio dei Ministri; product generation is centralized at DPC's Centro Funzionale Centrale (CFC) via the WIDE (Weather Ingestion Data Engine) processing chain.
- **Country / region:** Italy (peninsula, Sicily, Sardinia, and surrounding seas).
- **Data distributor:** DPC (Radar-DPC platform); **Agenzia ItaliaMeteo** (MeteoHub, operated with CINECA). The harmonized 2010–present ARCO archive was produced by **Fondazione Bruno Kessler (FBK)** with CINECA, ARPAE Emilia-Romagna, and Agenzia ItaliaMeteo, under the IT4LIA AI-Factory project.

---

## Network composition
26 radar systems — 20 C-band and 6 X-band — operated by 13 administrations:
the DPC directly operates 7 C-band and 4 X-band systems (all dual-polarization),
with the remainder run by regional authorities, ENAV (air navigation), and CNMCA
(military meteorology). C-band provides the standard operational surveillance;
the X-band systems add higher-resolution coverage in specific areas. The network
has expanded substantially since 2010. The national mosaic is delivered on a
1200 × 1400 grid at 1 km resolution in a Transverse Mercator projection (origin
42°N, central meridian 12.5°E, WGS 84), spanning roughly 35–48°N and 5–20°E.

---

## Products
This entry focuses on **SRI**, but the national mosaic generates several projected
gridded products:

- **Surface Rainfall Intensity (SRI)** — ground-level precipitation intensity (mm/h) derived from radar reflectivity, with corrections for beam overshooting, bright-band effects, and attenuation. **Radar-only** (no gauge fusion). 5-minute cadence (current); 1 km. This is the variable in the ARCO archive (stored as `RR`, precipitation rate, mm/h = kg m⁻² h⁻¹, float32, CF `standard_name: rainfall_flux`).
- **SRIadj** — the gauge-adjusted version of SRI (radar + national rain-gauge network). Not included in the ARCO archive.
- **SRT (Surface Rainfall Total)** — hourly cumulative precipitation, integrating radar with rain gauges; 5-minute update.
- **VMI** (vertical maximum indicator) and **CAPPI** (constant-altitude PPI, 2–8 km spaced by 1 km) — reflectivity-based mosaic products.

---

## Data availability
- **Is the data free?** Yes. Fully anonymous via ECMWF EWC and Zenodo; the DPC operational API is open; MeteoHub and ArcoDataHub require a free self-service account.
- **Is the data downloadable?** Yes.
- **Access tier:** Open — anonymous (no account) via ECMWF European Weather Cloud and Zenodo; the DPC Radar-DPC API is open access; MeteoHub (GRIB + ARCO) and ArcoDataHub (live) require simple self-service registration.
- **Data formats:** GeoTIFF (DPC operational API), GRIB (MeteoHub `radar_sri_dpc`), and Zarr v2 ARCO (the 2010–present archive). Historical raw source was OPERA BUFR (to mid-2020) then HDF5/ODIM.
- **Update cadence:** 5 minutes (current SRI). Historical temporal resolution: 15 min (2010–Jun 2014), 10 min (Jun 2014–Jun 2020), 5 min (Jul 2020 onward).
- **Primary access:**
  - *Operational / live:*
    - **DPC Radar-DPC platform** — open REST API (GeoTIFF), FTP distribution, and WebSocket notifications. Docs: https://dpc-radar.readthedocs.io
    - **MeteoHub `radar_sri_dpc`** (GRIB) — packaged daily files (rolling ~7-day window) plus on-demand extraction; open-data API `GET https://meteohub.agenziaitaliameteo.it/api/datasets/radar_sri_dpc/opendata`. Requires a free MeteoHub account.
  - *Harmonized ARCO archive (2010–present, variable `RR`):*
    - **ECMWF European Weather Cloud** (S3-compatible, **anonymous, no authentication**) — simplest via the `mlcast-datasets` Python package (an intake catalog from the EUMETNET E-AI programme), or directly with xarray + fsspec.
    - **Zenodo** — static, DOI-versioned Zarr snapshot for reproducibility: `doi:10.5281/zenodo.18637608` (open download).
    - **ArcoDataHub** (https://arcodatahub.com) — continuously updated live version (new timesteps appended every 5 min; time axis prefilled to 2050; check the `last_valid` attribute). Requires an account and API key: `https://<user>:<key>@api.arcodatahub.com/S3/<dataset>.zarr`.
    - **MeteoHub `radar.zarr`** (the MeteoARCO "Italian Radar DPC SRI Archive") — the **live** version (time axis pre-allocated to 2050; query the `last_valid` attribute for the true extent), at `https://meteohub.agenziaitaliameteo.it/api/arco/radar.zarr`. Open access, but requires a free MeteoHub account and a self-generated ARCO Access Key. Open with inline credentials — `xr.open_dataset(f"https://{email}:{access_key}@meteohub.agenziaitaliameteo.it/api/arco/radar.zarr", engine="zarr", chunks={}, consolidated=True)` — or a Basic-auth `Authorization` header. The exact URL is available via the "Copy ARCO Data URL" button on MeteoHub's ARCO Datasets page.
- **New-data notifications:** DPC WebSocket (Radar-DPC platform); MeteoHub scheduled extractions / API.
- **Archive depth:** Harmonized archive 2010-01-01 to present (~1.04 M timesteps, 98.5% complete). The DPC API serves primarily recent data; the MeteoHub GRIB feed is a rolling ~7-day window.
- **Licence:** **CC BY-SA 4.0** (ShareAlike). Attribution owed to the Dipartimento della Protezione Civile (data source) and, for the harmonized archive, Fondazione Bruno Kessler (processing and harmonization).

---

## Scope note
- **Observation, not forecast.** SRI is an observed surface-rainfall-intensity mosaic — clean fit.
- **Access gate is convenience, not a barrier.** The product has fully anonymous channels (ECMWF EWC, Zenodo) and the DPC's own open API, so it is unambiguously in scope. The two options surfaced on MeteoHub (`radar_sri_dpc`, `radar.zarr`) and the ArcoDataHub live feed are self-service-account tiers over the same data — flagged here because they are the *gated* doors, whereas the EWC/Zenodo route is the cleaner catalog target.
- **Fills the OPERA gap.** Italy is not an OPERA member, so OPERA composites have a gap over Italian territory; this product fills it and is OPERA-compatible.
- **ShareAlike obligation.** CC BY-SA 4.0 carries a ShareAlike requirement (stronger than OPERA's CC BY 4.0) — relevant for anyone redistributing derived products.

---

## Notes
- **Same archive, several doors.** The FBK IT-DPC-SRI archive is served as two *live* copies behind a free self-service account and Access Key — MeteoHub `radar.zarr` and ArcoDataHub, both pre-allocated to 2050 with a `last_valid` marker — and two *static, anonymous* copies: the ECMWF European Weather Cloud store and the DOI-versioned Zenodo snapshot. Prefer EWC or Zenodo for open, reproducible access; use a live copy for near-real-time.
- **SRI vs SRIadj vs SRT.** This entry covers radar-only SRI. SRIadj (gauge-adjusted) and SRT (hourly gauge-integrated total) are separate mosaic products; VMI and CAPPI are also produced.
- **Archive caveats.** The ARCO archive contains only the `RR` precipitation rate — no reflectivity or other radar moments (for those, work directly with DPC archives). Early years (2010–2015) have lower quality and coverage; source quality flags were not preserved; mountain shadowing/beam blockage degrades coverage in Alpine/Apennine valleys; maritime coverage is limited to ~150 km range.
- **Data feed vs viewer.** MeteoHub's radar map and the DPC Radar-DPC web viewer are rendered maps; the data feeds are the API/GRIB/Zarr channels above.

---

## Recent version history
- **2026 — IT-DPC-SRI harmonized ARCO archive published** (2010–2025 static snapshot on Zenodo + anonymous ECMWF EWC access; live version on ArcoDataHub appended every 5 min).
- **Jul 2020 — SRI cadence to 5 min**; raw archival format moved from OPERA BUFR to HDF5/ODIM.
- **~2020 — DPC Radar-DPC open REST API** launched (open-access radar products).
- **Jun 2014 — cadence 15 → 10 min.**
- **2010 — archive start.**

---

## Official documentation
- DPC Radar-DPC platform (API/FTP/WebSocket): https://dpc-radar.readthedocs.io
- MeteoHub (Agenzia ItaliaMeteo): https://meteohub.agenziaitaliameteo.it/ — ARCO guide https://meteohub.agenziaitaliameteo.it/ui/arco-guide?lang=en ; CKAN catalog https://dati.agenziaitaliameteo.it/ ; licenses https://meteohub.agenziaitaliameteo.it/app/license
- Product descriptions (SRI/SRT): https://www.agenziaitaliameteo.it/en/meteorology/observed-data/radar/italy-radar-map/
- IT-DPC-SRI dataset paper (Franch et al. 2026): https://arxiv.org/abs/2602.15088
- Zenodo dataset (DOI): https://doi.org/10.5281/zenodo.18637608
- ArcoDataHub: https://arcodatahub.com
