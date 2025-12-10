# California Post-fire Debris Flow Hazard Mapping
---
## 1. Project Overview
This project models post-fire debris flow and flood susceptibility in California by integrating:
- Wildfire burn severity (MTBS / Sentinel-2 NBR)
- Land-cover exposure after fire
- Terrain controls (slope, aspect, curvature)
- Atmospheric river–driven extreme rainfall (ERA5 / NOAA AR Catalog)

The project aims to understand **cascade hazards** following large wildfires:
>> Wildfire → Burn Scar Severity → Loss of Vegetation (Land exposure) → Atmospheric River Extreme Rainfall → Debris Flow & Flash Flood Risk
---

##  2. Data Sources
| Dataset                    | Source  | Usage                      |
| -------------------------- | ------- | -------------------------- |
| MTBS Burn Severity         | USGS    | Burn scar intensity (dNBR) |
| Sentinel-2                 | ESA/GEE | NDVI/NBR pre & post fire   |
| NLCD Land Cover            | USGS    | Fuel type & land exposure  |
| ERA5 Precipitation         | ECMWF   | AR rainfall intensity      |
| NOAA AR Catalog            | NOAA    | AR event identification    |
| USGS DEM                   | USGS    | Terrain slope / aspect     |
| USGS Debris Flow Inventory | USGS    | Model validation           |

---

## 3. Methodology
### 3.1 Burn Severity Mapping
A. MTBS (preferred)
Use dNBR-classified burn severity:
``` 
BurnSeverity = MTBS_dNBR
```
B. Sentinel-2 NBR (if computing manually)
```
NBR = (B8 - B12) / (B8 + B12)
BurnSeverity = NBR_before - NBR_after
```

### 3.2 Atmospheric River Rainfall Extraction
Extreme rainfall linked to AR events extracted from ERA5:
- 24–72 hour accumulated precipitation
- Z-score transformation to quantify extremeness
```
AR_intensity = Sum(precip_72h)
AR_zscore = (AR_intensity - μ) / σ
```

### 3.3 Terrain Factor Extraction
Using DEM, the strongest predictor for debris flow initiation:
```
Slope = Slope(DEM)
Aspect = Aspect(DEM)
Curvature = Curvature(DEM)
```
### 3.4 Land Exposure After Fire
```
Exposure = BurnSeverity * (LandCover == vegetation classes)
```
### 3.5 Debris Flow Susceptibility Model
A weighted linear combination (based on USGS), all layers normalized to 0–1.:
```
Risk = 0.4 * BurnSeverity  
     + 0.4 * Slope  
     + 0.2 * AR_intensity
```

### 3.6 Validation
Overlay risk raster with USGS debris-flow inventory:
- Hit rate / recall
- ROC Curve (optional)
- Confusion Matrix (threshold-based)
---

## 4. Final Outputs
Burn Severity Map
- Atmospheric River Rainfall Intensity Map
- Terrain Slope Map
- Final Debris Flow Susceptibility Map
- Validation Stats
---

## 5.GeoDatabase Structure
```
root/
│── data/
│── scripts/
│   ├── burn_severity_gee.js
│   ├── ar_rainfall_gee.js
│   ├── preprocess.py
│   └── debrisflow_risk.py
│── outputs/
│── README.md
```

## 6.Data
https://apps.nationalmap.gov/downloader/




