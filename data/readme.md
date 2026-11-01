# Spatial Datasets & Sources

This directory contains the metadata and source descriptions for all spatial vector and raster datasets utilized in the Abuja Solar Farm Site Suitability Analysis.

## Coordinate Reference System (CRS)
* **Projected CRS:** UTM Zone 32N
* **EPSG Code:** `EPSG:32632`
* **Geographic Datum:** WGS 84

---

## Dataset Inventory

| Layer Name | Data Type | Source Provider | Description & Resolution |
| :--- | :--- | :--- | :--- |
| **Power Grid Infrastructure** | Vector (Lines/Points) | Nigerian Energy Sector (`nigeriase4all.gov.ng`) | Existing 33kV and 132kV transmission lines and substations across the FCT. Used for Euclidean distance proximity calculations. |
| **Road Network** | Vector (Lines) | OpenStreetMap (OSM) / Infrastructure Vectors | Primary, secondary, and tertiary highways and connecting roads. Used to compute logistics and transport accessibility. |
| **Digital Elevation Model (DEM)** | Raster | USGS Earth Explorer (SRTM)[ | Shuttle Radar Topography Mission 30m resolution DEM. Used to derive slope angles in degrees. |
| **Sentinel / Landsat Imagery** | Raster | USGS Earth Explorer / Copernicus | Satellite imagery processed to calculate the Normalized Difference Vegetation Index (NDVI) at 30m resolution. |
| **ESA WorldCover LULC** | Raster | ESA WorldCover Dataset | Global land use land cover dataset at 10m/30m resolution. Used to create the binary constraint mask. |

---
## NDVI Calculation & Mathematical Formula

The Normalized Difference Vegetation Index (NDVI) was computed from satellite imagery to evaluate vegetation density and sparse land cover:

$$\text{NDVI} = \frac{\text{NIR} - \text{Red}}{\text{NIR} + \text{Red}}$$

* **NIR (Near-Infrared Band):** Sentinel-2 Band 8 / Landsat-8 Band 5 (strongly reflected by plant cellular structure).
* **Red Band:** Sentinel-2 Band 4 / Landsat-8 Band 4 (absorbed by chlorophyll pigments).
* **Threshold Context:** Values ranging from **0.0 to 0.2** represented bare soil, rock, and urban built-up surfaces (such as concrete and rooftops), which required cross-referencing with the ESA WorldCover mask to prevent false classification.

---

## Pre-Processing & Standardization Workflow
1. **Projection Alignment:** All raw vector and raster layers were reprojected to `EPSG:32632 (UTM Zone 32N)` to ensure uniform metric distance calculations.
2. **Rasterization & Buffering:** Proximity layers were converted using Euclidean distance tools, and all criteria rasters were resampled to a consistent **30m spatial resolution**.
3. **Reclassification:** Continuous variables were reclassified into a standardized 1-to-5 suitability scale(see readme).
4. **Binary Masking:** The `solar_mask.tif` derived from ESA WorldCover was applied as a hard exclusion filter to eliminate water bodies, dense forests, and built-up urban zones.
