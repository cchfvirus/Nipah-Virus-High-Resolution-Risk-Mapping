# Nipah Virus High-Resolution Risk Mapping
### Bangladesh & South India

[![Live Map](https://img.shields.io/badge/🗺️-View_Interactive_Map-blue?style=for-the-badge)](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)
[![GitHub Pages](https://img.shields.io/badge/Status-Live-success)](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## 🌐 Interactive Map

### **[→ Launch Interactive Risk Map ←](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)**

---

## Overview

This repository provides high-resolution spatial risk assessments for Nipah virus (NiV) transmission in Bangladesh and South India. The interactive web map allows researchers, public health officials, and decision-makers to explore risk patterns and identify high-priority areas for surveillance and intervention.

## Map Features

### Available Layers

#### 1. **Ecological Niche Model (ENM) - Median Suitability**
- **Resolution:** 1 kilometer
- **Description:** Shows environmental suitability for Nipah virus transmission based on ecological factors
- **Color Scale:** Gray (low suitability) → Dark Red (high suitability)
- **Use Cases:** Identifying potential spillover regions, long-term risk assessment

#### 2. **Weighted Risk Surface**
- **Resolution:** 100 meters
- **Description:** Integrated risk assessment combining multiple epidemiological and environmental factors
- **Color Scale:** Light Yellow (low risk) → Dark Red (high risk)
- **Use Cases:** Fine-scale intervention planning, local surveillance prioritization

### Interactive Tools

- 📍 **Click** any location to view risk values
- 📋 **Right-click** to copy coordinates
- 📏 **Drawing tools** to measure distances and areas
- 🗺️ **Multiple basemaps** (OpenStreetMap, Street view, Satellite imagery)
- 🎛️ **Opacity controls** for layer visualization
- 🔍 **Zoom and pan** to explore regions of interest

## Quick Start

### View the Map Online
Simply visit: **[https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)**

### Download Data
High-resolution raster files are available in [Releases](https://github.com/cchfvirus/Nipah-Virus-High-Resolution-Risk-Mapping/releases)

### Use in Your Own Project
```html
<!-- Embed the map in your website -->
<iframe src="https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/" 
        width="100%" height="600px" frameborder="0">
</iframe>
```

## Data Description

### Study Area
- **Primary Focus:** Bangladesh
- **Extended Coverage:** South India, including West Bengal
- **Coordinate System:** WGS84 (EPSG:4326)

### Risk Factors Included
- Environmental suitability
- Population density
- Land use/land cover
- Proximity to bat habitats
- Historical outbreak locations
- [Add your specific factors]

## Technical Details

### Data Format
- **File Type:** Cloud-Optimized GeoTIFF (COG)
- **Compression:** ZSTD (maximum compression)
- **NoData Value:** -9999

### Map Technology
- **Framework:** Leaflet.js
- **Raster Display:** GeoRaster Layer for Leaflet
- **Basemaps:** OpenStreetMap, Esri World Street Map & Satellite Imagery

### File Sizes
- ENM Layer (1km): ~[X] MB
- Risk Surface (100m): ~[X] MB

## How to Use the Data

### In QGIS
1. Download the .tif files from [Releases](https://github.com/cchfvirus/Nipah-Virus-High-Resolution-Risk-Mapping/releases)
2. Drag and drop into QGIS
3. Style using the same color ramps as the web map

### In R
```r
library(terra)

# Load ENM layer
enm <- rast("enm_median_suitability_1km.tif")

# Load risk surface
risk <- rast("weighted_risk_100m.tif")

# Extract values at points
coordinates <- data.frame(lon = c(90.4, 91.2), lat = c(23.8, 24.1))
values <- extract(risk, coordinates)
```

### In Python
```python
import rasterio
import numpy as np

# Load risk surface
with rasterio.open('weighted_risk_100m.tif') as src:
    risk_data = src.read(1)
    
    # Get value at coordinate
    lon, lat = 90.4, 23.8
    row, col = src.index(lon, lat)
    value = risk_data[row, col]
```

## Repository Structure
```
.
├── index.html                          # Interactive map (GitHub Pages)
├── enm_median_suitability_1km.tif     # ENM layer (1km)
├── weighted_risk_100m.tif             # Risk surface (100m)
├── README.md                          # This file
└── docs/
    └── methodology.md                 # Detailed methods (optional)
```

## Methodology

[Add a brief description of your methodology here, or link to a separate document]

### Data Sources
- [List your data sources]
- [e.g., LandScan population data]
- [e.g., MODIS land cover]
- [e.g., Outbreak databases]

### Analysis Workflow
1. [Step 1: Data collection]
2. [Step 2: Processing]
3. [Step 3: Modeling]
4. [Step 4: Validation]

## Citation

If you use this data or map in your research, please cite:
```
[Your Name(s)]. (2026). Nipah Virus High-Resolution Risk Mapping: Bangladesh & South India.
GitHub repository. https://github.com/cchfvirus/Nipah-Virus-High-Resolution-Risk-Mapping
```

## Publications

- [Link to your papers using this data]
- [Link to preprints]

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## Issues & Feedback

Found a bug or have suggestions? Please [open an issue](https://github.com/cchfvirus/Nipah-Virus-High-Resolution-Risk-Mapping/issues)

## License

This project is licensed under the [MIT License](LICENSE) - see the LICENSE file for details.

[Or choose another license like CC-BY-4.0 for data]

## Acknowledgments

- [Funding sources]
- [Collaborating institutions]
- [Data providers]
- [Other contributors]

## Contact

- **Author:** [Your Name]
- **Email:** [your.email@institution.edu]
- **Institution:** [Your Institution]
- **ORCID:** [Your ORCID if applicable]

## Related Resources

- [Link to your other projects]
- [Link to related datasets]
- [Link to publications]

---

### 🗺️ **[View Interactive Map](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)**

---

**Keywords:** Nipah virus, risk mapping, Bangladesh, South India, GIS, spatial epidemiology, disease surveillance, interactive map, ecological niche modeling

**Last Updated:** February 2026
