# 🌍 MODIS Land Cover Zonal Statistics – Khyber Pakhtunkhwa (KPK)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Platform](https://img.shields.io/badge/Platform-Google%20Colab-orange)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

A complete geospatial analysis pipeline to perform **zonal statistics** on MODIS
Land Cover data clipped to the **tehsils of Khyber Pakhtunkhwa (KPK), Pakistan**.
Built and tested on **Google Colab**.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Datasets](#datasets)
- [Requirements](#requirements)
- [Pipeline Steps](#pipeline-steps)
- [Reclassification Scheme](#reclassification-scheme)
- [Outputs](#outputs)
- [Visualizations](#visualizations)
- [How to Run](#how-to-run)
- [Project Structure](#project-structure)
- [Results Preview](#results-preview)

---

## 📖 Overview

This project performs a full **remote sensing + GIS analysis** workflow:

1. Load a MODIS Land Cover raster and a KPK tehsil boundary shapefile
2. Check and align their coordinate reference systems (CRS)
3. Clip the raster to the KPK tehsil boundaries
4. Reclassify MODIS land cover classes into 4 categories
5. Compute **zonal statistics** (min, max, mean, pixel count, coverage %)
   per tehsil per land cover class

---

## 🗂️ Datasets

| Dataset | Format | Description |
|---|---|---|
| `mosaic_wgs84_2021_LC_Type1.tif` | GeoTIFF | MODIS Land Cover Type 1 (MCD12Q1) – 2021 |
| `KPK_tehsil.zip` | Shapefile (zipped) | Administrative tehsil boundaries of KPK, Pakistan |

> **MODIS Product:** MCD12Q1 – Land Cover Type 1 (IGBP Classification)
> **Projection:** WGS84 (EPSG:4326)

---

## 🛠️ Requirements

### Python Libraries

```bash
pip install geopandas rasterio rasterstats matplotlib seaborn fiona pyproj shapely
```

### Platform
- ✅ Google Colab (recommended)
- ✅ Local Jupyter Notebook

---

## 🔄 Pipeline Steps

### Step 0 — Install Dependencies
Install all required geospatial Python libraries.

### Step 1 — Upload & Load Data
Upload the MODIS `.tif` raster and `KPK_tehsil.zip` shapefile to Colab,
then extract and load them into memory.

### Step 2 — Spatial Reference Check
Check the CRS of both datasets. If they do not match, reproject the
shapefile to match the raster CRS.

```python
# Reproject shapefile if CRS mismatch
if gdf.crs != raster_crs:
    gdf = gdf.to_crs(raster_crs)
```

### Step 3 — Clip Raster
Clip the MODIS raster to the KPK tehsil boundaries using `rasterio.mask`.

```python
clipped_data, clipped_transform = mask(src, geometries, crop=True, nodata=255)
```

### Step 4 — Visualize Clipped Raster
Two side-by-side plots:
- Clipped raster with colorbar
- Clipped raster overlaid with tehsil boundaries

### Step 5 — Reclassify
Reclassify the clipped raster into 4 land cover categories.

### Step 6 — Visualize Reclassified Raster
Two side-by-side plots with a shared legend showing all 4 classes.

### Step 7 — Zonal Statistics (Overall + Class-wise)
Compute min, max, mean, pixel count, and coverage % per tehsil per class.

### Step 8 — Save Results
Export results to CSV files and download them.

---

## 🎨 Reclassification Scheme

| Reclassified Value | Class | Original MODIS Classes | Color |
|---|---|---|---|
| 0 | Other | All remaining classes | ![#D3D3D3](https://placehold.co/15x15/D3D3D3/D3D3D3.png) Light Gray |
| 1 | Forest | 1, 2, 3, 4, 5 | ![#FFD700](https://placehold.co/15x15/FFD700/FFD700.png) Yellow |
| 2 | Grasslands | 10 | ![#006400](https://placehold.co/15x15/006400/006400.png) Dark Green |
| 3 | Croplands | 14 | ![#0000FF](https://placehold.co/15x15/0000FF/0000FF.png) Blue |

---

## 📤 Outputs

### Raster Files
| File | Description |
|---|---|
| `clipped_modis.tif` | MODIS raster clipped to KPK tehsil extent |
| `reclassified_modis.tif` | Reclassified land cover raster |
| `class_X_binary.tif` | Binary mask per class (for pixel count) |
| `class_X_origvals.tif` | Original value mask per class (for min/max/mean) |

### CSV Files
| File | Description |
|---|---|
| `classwise_zonal_stats_full.csv` | Wide format — all stats per tehsil |
| `classwise_pixel_summary.csv` | Pixel count per class per tehsil |
| `classwise_long_format.csv` | Long format — one row per tehsil per class |

### Sample Output (Long Format)

| Tehsil | Class | Pixel_Count | Pct (%) | Min | Max | Mean |
|---|---|---|---|---|---|---|
| BARANG | Forest | 1 | 100.0 | 4.0 | 4.0 | 4.0 |
| BARANG | Grasslands | 563 | 100.0 | 10.0 | 10.0 | 10.0 |
| MAMUND | Croplands | 2 | 100.0 | 14.0 | 14.0 | 14.0 |

---

## 📊 Visualizations

| Plot | File | Description |
|---|---|---|
| Clipped Raster | `clipped_raster_viz.png` | Original MODIS classes with/without boundaries |
| Reclassified Map | `reclassified_viz.png` | 4-class map with/without tehsil boundaries |
| Stacked Bar | `classwise_stacked_bar.png` | Pixel count per class stacked per tehsil |
| Heatmap | `classwise_heatmap.png` | % coverage of each class across tehsils |
| Pie Chart | `classwise_pie.png` | Overall land cover distribution across KPK |
| Mean Bar | `classwise_mean_bar.png` | Mean MODIS value per class per tehsil |
| Min/Max/Mean Range | `classwise_minmaxmean.png` | Shaded range plots per class across tehsils |

---

## ▶️ How to Run

### On Google Colab

1. Open [Google Colab](https://colab.research.google.com)
2. Create a new notebook
3. Run the cells in order (Steps 0 → 8)
4. When prompted, upload:
   - `mosaic_wgs84_2021_LC_Type1.tif`
   - `KPK_tehsil.zip`
5. CSV outputs will auto-download at the end

### On Local Jupyter Notebook

```bash
# Clone the repo
git clone https://github.com/yourusername/kpk-modis-zonal-stats.git
cd kpk-modis-zonal-stats

# Install dependencies
pip install -r requirements.txt

# Launch notebook
jupyter notebook
```

> Replace `files.upload()` and `files.download()` with local file paths
> when running outside of Google Colab.

---

## 📁 Project Structure

---

## 📍 Study Area

**Khyber Pakhtunkhwa (KPK)** is a province in northwestern Pakistan, bordering
Afghanistan to the west and north. It contains diverse land cover including
forests, grasslands, croplands, and urban areas across its many tehsils
(sub-district administrative units).

---

## 👤 Author

**Your Name**
- GitHub: [@yourusername](https://github.com/yourusername)
- Email: your.email@example.com

---

## 📄 License

This project is licensed under the MIT License.
See the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- [MODIS Land Cover Product (MCD12Q1)](https://lpdaac.usgs.gov/products/mcd12q1v006/)
  provided by NASA LP DAAC
- Shapefile data for KPK tehsil boundaries
- [rasterstats](https://pythonhosted.org/rasterstats/) library for zonal statistics
- [rasterio](https://rasterio.readthedocs.io/) and
  [geopandas](https://geopandas.org/) for geospatial processing

  
