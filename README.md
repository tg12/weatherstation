# Weather & Energy Forecasting Dashboard

A **production-grade**, Python-based dashboard that retrieves and visualises high-frequency weather and energy-relevant data every 3 hours. Designed for scientific insight, business use, and AI training.

---

##  Purpose & Target Audiences

- **Retail Planners & Logistics**: Pre-position inventory (suncream, snow shovels, etc.) using heating/cooling indicators and precipitation forecasts.  
- **Energy Analysts & Traders**: Model regional energy demand using detailed **Heating Degree Days (HDD)** and **Cooling Degree Days (CDD)** with CPC forecast overlays.  
- **AI/Quant Developers**: Access timestamped, annotated forecast imagery to train **multi-modal models** on weather signals.  
- **Geospatial Engineers**: A reference architecture for geospatial Python pipelines, model ingestion, and automated deployments.

---

##  Scientific Foundations

### Numerical Weather Prediction (NWP)

- **HRRR & GFS models via Herbie**: Herbie is a Python interface that downloads high-resolution weather model data (HRRR, GFS, including GRIB2 → xarray/cfgrib) :contentReference[oaicite:1]{index=1}  
- NWP data is gridded at ~3 km resolution (HRRR) and updated hourly—ideal for high-frequency forecasting.

### Degree Days (HDD/CDD)

- **Calculations**:
  - **HDD** = max(0, Tₚₑₙₑᵣₐₜᵤᵣₑ _base_ − Tₘₑₐₙ)
  - **CDD** = max(0, Tₘₑₐₙ − Tₚₑₙₑᵣₐₜᵤᵣₑ _base_)
  - Standard base: **18 °C (65 °F)** :contentReference[oaicite:2]{index=2}  
- CPC computes state-level HDD/CDD via weighted climate divisions using census-based population distribution :contentReference[oaicite:3]{index=3}.

### Ancillary Variables

- **Thermofeel** computes “feels-like” temperature using humidity and wind.
- **500 mb geopotential height** and **jet stream wind speeds** help identify large‑scale atmospheric patterns.
- **Solar radiation**, **precipitation**, **cloud cover**, **surface pressure**, **dew point**, **CAPE** — all model energy generation and usage patterns at fine scales.

---

##  Image Contents & Scientific Detail

Each dashboard generates a multi-panel figure containing:

| Panel | Description |
|---|---|
| **2 m Air Temp** | Actual grid-sample temperature in °C. |
| **10 m Wind Barbs** | Barbs colored by m/s speed; wind-speed legend included. |
| **4 h Precipitation** | Total precipitation (mm) across 4-hour HRRR forecast period. |
| **500 mb Geopotential** | Contours & shading reveal mid-tropospheric ridges and troughs. |
| **Surface Solar Radiation** | Potential PV generation distribution (W/m²). |
| **Temp Anomaly** | Deviation from 15 °C climatology (°C)—highlights unusual thermal events. |
| **HDD & CPC Overlay** | HRRR-based HDD shading + CPC state-level numeric HDD values. |
| **CDD & CPC Overlay** | Mirror of HDD for cooling demand quantification. |
| **Jet Stream (250 mb)** | Wind speed map and height contours—storms steering. |
| **Cloud Cover** | Percentage-based shading—controls solar potential and rainfall inference. |
| **Feels-Like Temp** | Thermofeel output expressing human thermal stress. |
| **Optional Panels** | Surface pressure, dew point, CAPE, relative humidity when available. |
| **CPC Degree-Day Table** | State-level table for HDD/CDD — included as OCR/AI-friendly monospaced table.

---

## System Architecture & Tech Stack

- **Core Language**: Python  
- **Data Ingestion**: Herbie + xarray/cfgrib (for GRIB2 parsing) :contentReference[oaicite:4]{index=4}  
- **Numeric Processing**: pandas, numpy (stacking, anomalies, computations)  
- **Visualization**: cartopy (mapping), matplotlib, seaborn, Thermofeel  
- **Deployment**: Linode VPS with Traefik edge router (Let's Encrypt TLS, service discovery)  
- **CI/CD**: GitLab pipelines triggered every 3 hours to download data, build images, and deploy 
- **Security**: SSL across all endpoints, timeouts, error checks, shapefile caching  

---

## How It Works

1. **Cron-triggered** via GitLab every 3 hours  
2. **Retrieve NWP variables** (`Herbie.download()` for TMP, U/V wind, APCP, HGT, DSWRF, etc.)  
3. **CPC HDD/CDD ingestion/parsing** via CSV  
4. **Compute variables**: anomaly, hdd, cdd, thermofeel, wind speed, etc.  
5. **Generate multi-panel plots**: consistent color maps, labels, titles, and timestamp watermark  
6. **Overlay CPC values** at geographic centroids  
7. **Save PNGs** with unique UUID-timestamps  
8. **Deploy via Traefik**, using secure routes and caching  

---
