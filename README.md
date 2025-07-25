# Weather & Energy Forecasting Dashboard

A **production-grade**, Python-based dashboard that retrieves and visualizes high-frequency weather and energy-relevant data every few hours. Designed for scientific insight, business use, and AI training.

---

## Purpose & Target Audiences

- **Retail Planners & Logistics**  
  Pre-position inventory (suncream, snow shovels, etc.) using heating/cooling indicators and precipitation forecasts.

- **Energy Analysts & Traders**  
  Model regional energy demand using detailed **Heating Degree Days (HDD)** and **Cooling Degree Days (CDD)** with CPC forecast overlays.

- **AI/Quant Developers**  
  Access timestamped, annotated forecast imagery to train **multi-modal models** on weather signals.

- **Geospatial Engineers**  
  A reference architecture for geospatial Python pipelines, model ingestion, and automated deployments.

---

## Scientific Foundations

### Numerical Weather Prediction (NWP)

- **HRRR & GFS models via Herbie**  
  Herbie is a Python interface that downloads high-resolution weather model data (HRRR, GFS, including GRIB2 → xarray/cfgrib).

- NWP data is gridded at ~3 km resolution (HRRR) and updated hourly—ideal for high-frequency forecasting.

### Degree Days (HDD/CDD)

#### Calculations

- **Heating Degree Days (HDD)**  
  \[
  \text{HDD} = \max(0, T_{\text{base}} - T_{\text{mean}})
  \]

- **Cooling Degree Days (CDD)**  
  \[
  \text{CDD} = \max(0, T_{\text{mean}} - T_{\text{base}})
  \]

- **Standard Base Temperature**:  
  **18 °C (65 °F)**

> The Climate Prediction Center (CPC) calculates state-level HDD and CDD using weighted averages across climate divisions, adjusted by census-based population distribution.

### Ancillary Variables

- **Thermofeel** computes “feels-like” temperature using humidity and wind.
- **500 mb geopotential height** and **jet stream wind speeds** help identify large-scale atmospheric patterns.
- **Solar radiation**, **precipitation**, **cloud cover**, **surface pressure**, **dew point**, **CAPE** — all model energy generation and usage patterns at fine scales.

---

## Image Contents & Scientific Detail

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
| **CPC Degree-Day Table** | State-level table for HDD/CDD — included as OCR/AI-friendly monospaced table. |

---

## System Architecture & Tech Stack

- **Core Language**: Python  
- **Data Ingestion**: Herbie + xarray/cfgrib (for GRIB2 parsing)  
- **Numeric Processing**: pandas, numpy (stacking, anomalies, computations)  
- **Visualization**: cartopy (mapping), matplotlib, seaborn, Thermofeel  
- **Deployment**: Linode VPS with Traefik edge router (Let's Encrypt TLS, service discovery)  
- **CI/CD**: GitLab pipelines triggered every 3 hours to download data, build images, and deploy  
- **Security**: SSL across all endpoints, timeouts, error checks, shapefile caching  

---

## Features

### 1. Scheduled Execution
Triggered automatically via GitLab CI/CD every few hours using a cron schedule.

### 2. Data Acquisition
Retrieve numerical weather prediction (NWP) variables using `Herbie.download()`. This includes:
- Temperature (TMP)
- Zonal and meridional wind components (U/V)
- Precipitation (APCP)
- Geopotential height (HGT)
- Downward shortwave radiation flux (DSWRF)
- Additional relevant meteorological fields

### 3. CPC Data Ingestion
Ingest and parse Climate Prediction Center (CPC) Heating Degree Days (HDD) and Cooling Degree Days (CDD) from CSV files.

### 4. Derived Variable Computation
Compute key derived metrics including:
- Temperature anomalies
- HDD and CDD
- Thermofeel index
- Wind speed and direction
- Other custom indicators

### 5. Visualization
Generate multi-panel plots with:
- Consistent colormaps and legends
- Clear geographic labels and titles
- Timestamp watermarks for traceability

### 6. Geospatial Overlays
Overlay CPC-derived values at corresponding geographic centroids for comparative analysis.

### 7. Output Handling
Save visual outputs as PNG images with unique UUID-based timestamps for archival and version control.

### 8. Web Deployment
Deploy images and dashboards via Traefik reverse proxy using secure, cache-enabled routes.

For the **live version** of this dashboard, check out the latest deployment [here](https://barometer.jamessawyer.co.uk/).

![HRRR Dashboard Image](hrrr_dashboard_2025071412_f04_39c63a61.png)


---
