# Landsat Seasonal Change Analysis

A reproducible remote sensing workflow for analyzing **seasonal vegetation dynamics, surface water, and land surface temperature using Landsat 8/9 products**.

This repository demonstrates how optical and thermal Landsat observations can be integrated to examine:

- seasonal vegetation change using NDVI,
- open-water mapping using NDWI,
- land surface temperature from the Landsat Level-2 `ST_B10` product,
- vegetation–temperature relationships,
- and seasonal spatial patterns across an agricultural–urban landscape.

The analysis was developed as part of a remote sensing course exercise, with emphasis on physically meaningful preprocessing, masking, spectral-index computation, thermal interpretation, and reproducible geospatial analysis.

---

## Overview

The workflow compares two Landsat 8/9 scenes representing **early June** and **mid-September** conditions over an Iowa study area.

The supplied GeoTIFF stacks include:

- Blue
- Green
- Red
- Near-Infrared (NIR)
- SWIR1
- `ST_B10`
- `QA_PIXEL`

The analysis focuses on detecting seasonal changes in vegetation condition and examining how vegetation relates to surface temperature.

---

## Main Objectives

The repository addresses the following remote sensing tasks:

1. Visualize Landsat true-color and false-color composites.
2. Compute NDVI for June and September.
3. Quantify seasonal vegetation change using ΔNDVI.
4. Compute NDWI and identify open-water pixels.
5. Convert Landsat `ST_B10` digital numbers to land surface temperature.
6. Compare LST between low- and high-NDVI surfaces.
7. Examine the NDVI–LST relationship.
8. Generate publication-style summary figures for seasonal interpretation.

---

## Data Processing

### Landsat Surface Reflectance

Surface reflectance was calculated using the Landsat Collection 2 Level-2 scaling relationship:

```text
Surface Reflectance = DN × 0.0000275 − 0.2
```

### Land Surface Temperature

The supplied `ST_B10` layer represents the Landsat Level-2 surface temperature product.

Temperature was scaled using:

```text
LST [K] = DN × 0.00341802 + 149.0
```

Because `ST_B10` already contains a Level-2 land surface temperature retrieval, no additional emissivity correction was applied.

---

## Quality Control

The `QA_PIXEL` layer was used to identify invalid observations.

The analysis masks:

- fill pixels,
- clouds,
- cloud shadows,
- and other invalid observations where required.

Only pixels remaining valid after the quality-control procedure are included in statistical calculations.

---

## Vegetation Analysis

### Normalized Difference Vegetation Index

NDVI was calculated as:

```text
NDVI = (NIR − Red) / (NIR + Red)
```

The seasonal difference was calculated as:

```text
ΔNDVI = NDVISeptember − NDVIJune
```

Only pixels valid in both dates were used for the seasonal comparison.

### Scene-average NDVI

| Date | Mean NDVI |
|---|---:|
| June | 0.6757 |
| September | 0.4854 |
| Mean ΔNDVI | -0.1904 |

The negative mean ΔNDVI indicates an overall reduction in vegetation greenness between early June and mid-September.

This seasonal decline is consistent with crop development and senescence across the Iowa agricultural landscape, although individual fields show substantial spatial variability.

---

## Seasonal Vegetation Change

The ΔNDVI map uses a diverging color scale centered at zero:

- negative values indicate lower NDVI in September,
- positive values indicate higher NDVI in September.

This representation highlights both vegetation decline and localized areas of increase.

![Seasonal vegetation and LST summary](Fig8.png)

**Figure 1.** Landsat-derived seasonal vegetation and surface temperature summary, including June NDVI, September NDVI, ΔNDVI calculated as September minus June, and June land surface temperature.

---

## True-Color and False-Color Comparison

True-color composites use the visible RGB bands, whereas false-color imagery uses NIR–Red–Green.

In false-color imagery, healthy vegetation appears bright red because vegetation strongly reflects near-infrared radiation.

![True and false color comparison](Fig3.png)

**Figure 2.** Landsat true-color and false-color composites for June and September. False-color imagery enhances vegetation and makes seasonal differences in canopy condition more visually apparent.

---

## Open-Water Mapping

The Normalized Difference Water Index was calculated as:

```text
NDWI = (Green − NIR) / (Green + NIR)
```

Several thresholds were explored. A threshold of:

```text
NDWI > 0.00
```

was retained because it preserved the main river and smaller open-water features while avoiding excessive omission of narrow channels.

Each Landsat pixel represents:

```text
30 m × 30 m = 900 m² = 0.0009 km²
```

Using the selected threshold:

- water pixels: **5,656**
- estimated open-water area: **5.090 km²**

The resulting area should be interpreted as a threshold-based estimate because shoreline and narrow-channel pixels may contain mixtures of land and water.

---

## Vegetation–Temperature Relationship

To compare contrasting vegetation conditions, two NDVI groups were defined:

```text
Low NDVI  : NDVI ≤ 0.40
High NDVI : NDVI ≥ 0.80
```

These thresholds were selected to create clearly separated vegetation conditions within the June scene and are not intended as universal land-cover thresholds.

Open-water pixels identified using NDWI were excluded from the comparison.

### Mean LST by vegetation condition

| NDVI group | Valid pixels | Mean LST |
|---|---:|---:|
| Low NDVI | 62,650 | 316.69 K |
| High NDVI | 311,554 | 308.37 K |

The resulting thermal contrast was:

```text
Mean LST(low NDVI) − Mean LST(high NDVI) = 8.33 K
```

Low-NDVI surfaces were therefore approximately **8.33 K warmer on average** than high-NDVI surfaces in the June scene.

This pattern is consistent with cooling associated with vegetation through:

- evapotranspiration,
- shading,
- and reduced sensible heat storage.

However, the observed difference should not be interpreted as a purely causal vegetation-cooling effect because NDVI groups can also differ in soil moisture, impervious cover, surface material, albedo, land use, and topography.

---

## NDVI–LST Relationship

A supporting pixel-level analysis was conducted for valid non-water pixels.

The Pearson correlation between June NDVI and LST was:

```text
r = -0.687
```

This moderately strong negative relationship indicates that greener surfaces were generally cooler.

The relationship is particularly relevant to urban heat mitigation, where tree canopy and vegetated surfaces can contribute to lower surface temperatures.

---

## Interpretation

The dominant seasonal signal is a reduction in vegetation greenness from June to September.

The scene-average NDVI declined from **0.6757** to **0.4854**, while individual agricultural fields exhibited different magnitudes and directions of change.

The spatial pattern is consistent with seasonal crop development, maturity, and senescence, although the specific cause of change in any individual field cannot be determined from two satellite observations alone.

The inverse NDVI–LST relationship also indicates that greener surfaces tend to be cooler, supporting the broader role of vegetation in moderating land surface temperature.

---

## Limitations

Optical and thermal Landsat imagery can identify spatial patterns but cannot independently determine all physical or management processes responsible for them.

The analysis cannot directly determine:

- crop species for individual fields,
- irrigation status,
- rooting-zone soil moisture,
- recent precipitation,
- air temperature or humidity,
- wind conditions,
- stomatal conductance,
- actual evapotranspiration,
- management history,
- or the exact amount of cooling caused by vegetation alone.

Landsat LST also represents **surface temperature**, not near-surface air temperature or direct human heat exposure.

Additional datasets would be required for causal interpretation, including meteorological observations, crop maps, soil-moisture data, evapotranspiration estimates, and land-management information.

---

## Repository Structure

```text
landsat-seasonal-change-analysis/
│
├── RemoteSensing_veg_Mirza.ipynb
├── Fig3.png
├── Fig8.png
└── README.md
```

### Files

- `RemoteSensing_veg_Mirza.ipynb` — complete analysis notebook
- `Fig3.png` — June and September true-color / false-color comparison
- `Fig8.png` — seasonal NDVI and LST summary figure
- `README.md` — project documentation

---

## Methods Summary

The workflow follows the sequence:

```text
Landsat GeoTIFFs
        ↓
Band loading
        ↓
Scale-factor application
        ↓
QA and fill masking
        ↓
Spectral indices
        ↓
NDVI / NDWI
        ↓
Seasonal change analysis
        ↓
LST scaling
        ↓
Vegetation–temperature comparison
        ↓
Visualization and interpretation
```

---

## Tools and Libraries

The analysis was implemented in Python using standard scientific and geospatial libraries, including:

- NumPy
- Matplotlib
- rasterio
- pandas
- Jupyter / Google Colab

---

## Reproducibility

The notebook is structured so that the workflow can be repeated with comparable Landsat 8/9 Level-2 scenes, provided that the input bands follow the same structure and scaling conventions.

Users should verify:

- band order,
- QA definitions,
- scale factors,
- acquisition dates,
- and spatial alignment

before applying the workflow to another study area.

---

## Scientific Note

The analysis emphasizes the distinction between **association and causation**.

For example, the observed NDVI–LST relationship indicates that greener surfaces tend to be cooler, but the measured temperature contrast cannot be attributed solely to vegetation because multiple surface properties covary with NDVI.

This distinction is important when interpreting remote sensing results for agricultural, environmental, and urban-climate applications.

---

## Author

**Mirza Md Tasnim Mukarram**

Remote Sensing | Geospatial Analysis | GeoAI | Environmental Applications

---

## License

This repository is intended for academic and educational use.

If reused or adapted, please provide appropriate attribution.
