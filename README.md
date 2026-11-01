# Abuja Solar Farm Site Suitability Analysis (AHP-MCDA)

[![GIS Analysis](https://img.shields.io/badge/GIS-QGIS%203.28-blue.svg)](https://qgis.org)
[![Methodology](https://img.shields.io/badge/Methodology-AHP%20%2F%20WLC-green.svg)](https://en.wikipedia.org/wiki/Analytic_hierarchy_process)
[![Web Map](https://img.shields.io/badge/Web%20Map-Leaflet%20%2F%20GitHub%20Pages-orange.svg)](https://sotayo-afolabi.github.io/gis-solar-farm-site-suitability-abuja/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

An advanced spatial Multi-Criteria Decision Analysis (MCDA) framework developed to identify and prioritize optimal candidate sites for utility-scale solar photovoltaic (PV) farm deployment across the Federal Capital Territory (FCT) / Abuja, Nigeria.

🌐 **Explore the Interactive Web Map:** [sotayo-afolabi.github.io/gis-solar-farm-site-suitability-abuja](https://sotayo-afolabi.github.io/gis-solar-farm-site-suitability-abuja/)

---

## 📌 Executive Summary
Because Nigeria sits near the equator, regional solar irradiance across Abuja is consistently high and uniform. Consequently, this project shifts the core site-selection focus away from raw irradiance toward **civil constructability, terrain topography, environmental protection, and infrastructure accessibility**. 

Using **QGIS 3.28+**, spatial layers—including transmission line proximity, road accessibility, slope topography, and NDVI vegetation indices—were standardized and evaluated using **Saaty's Analytic Hierarchy Process (AHP)**. To eliminate false positives, an **ESA WorldCover Land Use Land Cover (LULC) binary mask** was integrated to filter out urban rooftops, water bodies, and protected zones. The resulting model achieved a **Consistency Ratio (CR) of 2.5%**, producing a high-resolution suitability map to guide renewable energy capital expenditure.

---

## ⚙️ Spatial Criteria & Reclassification Logic

Each spatial criterion was transformed to a 30m resolution raster, and reclassified onto a uniform 1-to-5 scale ($1 = \text{Least Suitable}$, $5 = \text{Most Suitable}$):

| Criterion | GIS Data Source | Derived Variable | Reclassification Logic |
| :--- | :--- | :--- | :--- |
| **Power Line Proximity** | Power Grid Vectors (`nigeriase4all.gov.ng`) | Euclidean Distance (30m Raster) | Closer to existing 33kV/132kV transmission lines & substations = Higher suitability & lower grid-interconnection CapEx (Value: 5)|
| **Road Network Accessibility** | OpenStreetMap Infrastructure Vectors | Euclidean Distance (30m Raster)| Closer to major roads = Higher suitability for heavy machinery, PV module transport, and logistics (Value: 5). *(Note: Abuja's dense network ensures no areas exceed 20km from primary roads).*|
| **Slope & Topography** | Shuttle Radar Topography Mission (DEM) | Terrain Angle in Degrees (30m Raster) | Flat or gently undulating terrain ($0^\circ - 5^\circ$) favored to minimize civil grading, mounting structures, and site preparation costs (Value: 5) |
| **NDVI (Vegetation Index)** | Sentinel / Landsat Imagery | Vegetation Density Score (30m Raster) | Lower NDVI / sparse vegetation favored to minimize environmental clearance hurdles and ecological disruption (Value: 5) |
| **ESA WorldCover LULC Mask** | ESA WorldCover LULC Dataset | Binary Exclusion Raster (`solar_mask.tif`) | Water bodies, dense forests, and built-up urban zones completely masked out via hard thresholding (Zero-weight exclusion) |

---

## 🔬 AHP Pairwise Comparison & Model Validation

Pairwise comparison matrices were executed using Saaty's 1–9 preference scale. Grid transmission proximity and road access were prioritized to minimize balance-of-system (BoS) expenses.

### AHP Pairwise Matrix
| Criteria | Slope | NDVI | Road | Powerline |
| :--- | :--- | :--- | :--- | :--- |
| **Slope** | 1.00 | 3.00 | 0.05 | 0.20 |
| **NDVI** | 0.33 | 1.00 | 0.25 | 0.14 |
| **Road** | 2.00 | 4.00 | 1.00 | 0.33 |
| **Powerline** | 5.00 | 7.00 | 3.00 | 1.00 |

### Derived Weights & Consistency Metrics:
* **Power Line Proximity:** 57.8% (0.578)
* **Road Proximity:** 22.8% (0.228)
* **Slope / Topography:** 13.4% (0.134)
* **NDVI (Vegetation Index):** 6.0% (0.060)

**Validation Results:**
* Principal Eigenvalue ($\lambda_{max}$) = 4.067
* Consistency Ratio ($CR$) = **2.5%** *(Well below the standard 10% threshold, validating mathematical rigor)*

---

## 🛠️ Resolving Classification Conflicts: The LULC Mask Logic

Relying on NDVI alone introduced classification conflicts. Specifically, **NDVI values between 0.0 and 0.2** correspond interchangeably to bare soil, exposed rock, and urban built-up surfaces (such as concrete infrastructure and building rooftops). 

To prevent urban areas from being misclassified as open land, an **ESA WorldCover binary constraint mask (`solar_mask.tif`)** was applied. This hard filter successfully eliminated built environments, wetlands, and dense forestry, restricting final candidate zones strictly to vacant, unconstrained terrain.

---

## 📐 Composite Suitability Model Formula

Standardized rasters were combined via Weighted Linear Combination (WLC) in the QGIS Raster Calculator:

$$\text{Suitability Index} = \Big[(R_{\text{Power}} \times 0.578) + (R_{\text{Road}} \times 0.228) + (R_{\text{Slope}} \times 0.134) + (R_{\text{NDVI}} \times 0.060)\Big] \times \text{Mask}_{\text{solar}}$$

---

## 📊 Key Findings & Recommendations

1. **High-Priority Development Corridors:** Optimal suitability zones (scores 4.0 – 5.0) cluster strategically across flat terrain in the FCT periphery, particularly aligning with municipal councils such as **Gwagwalada, Suleja, and Abaji**, where existing 33kV/132kV transmission infrastructure intersects with major highways.
2. **CapEx & BoS Optimization:** Constraining candidate sites within a 2 km buffer of power grids and roads significantly reduces electrical interconnection expenses and heavy haulage logistics costs.
3. **Environmental Safeguards:** Rigorous masking successfully shielded ecological reserves and drainage channels from industrial encroachment.

---

## 🗂️ Repository Structure

```tree
gis-solar-farm-site-suitability-abuja/
│
├── docs/                      # QGIS qgis2web export files powering GitHub Pages live web map
│   ├── index.html             # Main entry point for the interactive web map
│   ├── css/                   # Map styling and layout sheets
│   ├── js/                    # Leaflet interactivity and layer controls
│   └── data/                  # GeoJSON spatial vector layers rendered on the web map
│
├── data/                      # Spatial datasets and input layers storage
│   └── README.md              # Comprehensive documentation of all data sources and parameters
│
├── maps/                      # Exported high-resolution map images & layout compositions
│   ├── abuja_suitability_map.png
│   ├── power_line_reclassified.png
│   ├── road_reclassified.png
│   ├── slope_reclassified.png
│   ├── ndvi_reclassified.png
│   └── lulc_mask.png
│
├── portfolio/                 # case study PDFs and executive summary documents
│   └── Abuja_Solar_Farm_Suitability_Portfolio.pdf
│
├── LICENSE                    # Open-source MIT License
└── README.md                  # Main project overview and technical documentation
```


## 💻 Tech Stack & Tools
* **GIS Engine:** QGIS 3.28+ (Raster Calculator, GDAL Processing Toolbox)
* **Web Mapping:** `qgis2web` plugin, Leaflet.js, GitHub Pages
* **Coordinate Reference System (CRS):** EPSG:32632 (UTM Zone 32N)[cite: 14]
* **Programming & Automation Roadmap:** Python (`Rasterio`, `GeoPandas`, `NumPy`) for future multi-state pipeline scaling

## 👤 Author
**Sodiq Afolabi Sotayo**  
*GIS Analyst & Spatial Data Engineer*  
* **Email:** [Sotayo32@gmail.com](mailto:Sotayo32@gmail.com)  
* **LinkedIn:** [linkedin.com/in/afolabi-sotayo-48416a1a3](https://www.linkedin.com/in/afolabi-sotayo-48416a1a3)  
* **Portfolio:** [GitHub Profile](https://github.com/Sotayo-Afolabi)

