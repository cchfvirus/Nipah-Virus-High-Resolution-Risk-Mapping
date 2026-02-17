# Nipah Virus High-Resolution Risk Mapping

## 🌐 Interactive Map

### **[→ Launch Interactive Risk Map ←](https://cchfvirus.github.io/Nipah-Virus-High-Resolution-Risk-Mapping/)**

---

**Overview**

This map provides high-resolution spatial risk assessments for Nipah virus (NiV) transmission in Eastern South Asia (Bangladesh, Eastern India, Eastern Nepal, Southern Bhutan) and Southern India. The interactive web map allows researchers, public health officials, and decision-makers to explore risk patterns and identify high-priority areas for surveillance, preparedness, and intervention. The platform integrates ecological suitability modeling, fine-scale risk surfaces, historical outbreak data, and uncertainty estimates into a single, browser-based visualization tool.

# Nipah Virus Risk Mapping — Bangladesh & South India (Leaflet + GeoRaster)

A single-page, client-side web GIS viewer for interactive Nipah virus risk mapping across Bangladesh / Eastern South Asia and South India, including a regional ecological suitability surface. This interactive web map visualizes multi-scale Nipah virus suitability and risk surfaces across Bangladesh and South India. The goal is to provide a fast, intuitive way to explore where environmental suitability and modeled risk signals are highest, and how these patterns relate to known outbreak locations and geographic context.

## **1) Purpose and scope**

- **Application type:** Single-page, client-side web GIS viewer  
- **Primary function:** Interactive Nipah virus risk mapping for Bangladesh / Eastern South Asia and South India, plus a regional suitability surface  
- **Data types supported:**
  - **Raster:** GeoTIFFs (including Cloud Optimized GeoTIFFs: `*_cog.tif`)
  - **Vector:** GeoJSON/JSON polygons and points (`.geojson`, `.json`)

## **2) Runtime environment**

- **Execution model:** Runs entirely in the browser (no server-side code in this repo)
- **Network requirements:**
  - External CDNs for JS/CSS libraries
  - Local/relative `fetch()` for raster/vector files (e.g., `enm_cog.tif`, `outbreaks.json`)  
    - Files must be hosted alongside the HTML (same origin) or be CORS-accessible
- **Browser APIs used:**
  - `fetch()` for loading rasters/vectors
  - Clipboard API: `navigator.clipboard.writeText()` with fallback to `document.execCommand('copy')`
  - `setTimeout()` to delay vector loading

## **3) Core libraries and versions (dependencies)**

Loaded via `<script>` tags / CDNs:

- **Leaflet 1.9.4** — map rendering, layer system, controls, popups  
- **georaster 1.6.0** — parses GeoTIFF `ArrayBuffer`s in-browser  
- **georaster-layer-for-leaflet 3.10.0** — renders parsed georasters as Leaflet layers  
- **chroma-js 2.4.2** — color scales for continuous rasters  
- **leaflet-draw 1.0.4** — interactive measurement/drawing tools (polyline, polygon)

## **4) Map initialization and display stack**

- **Initial view:** `center=[18.0, 82.5]`, `zoom=6` (regional view)
- Stores an `initialView` to restore for specific layers (**`enm`** uses it)

### **Basemaps (tile layers)**
- OpenStreetMap: `https://{s}.tile.openstreetmap.org/...`
- Esri World Street Map
- Esri World Imagery

### **Custom Leaflet panes and z-index ordering**
This establishes a deliberate cartographic stack so rasters don’t hide boundaries and points don’t get buried:

- `tilePane`: **100** (basemap beneath everything)
- `rasterPane`: **200** (GeoTIFF rasters)
- `adminPane`: **400** (admin boundaries)
- `vectorPane`: **450** (general vectors)
- `pointsPane`: **800** (outbreak points always above boundaries)
- `popupPane` / `tooltipPane`: **10000** (always above points)

## **5) Data layers and configuration model**

The script uses two configuration objects:

### **A) Raster layers (`layerConfigs`)**
- **Total raster layers:** **9** (`totalLayers = 9`)

#### **1 continuous “regional suitability” layer**
- `enm` → `enm_cog.tif`
- **Domain:** `[0, 1]`
- **Colors:** 7-color ramp (gray → dark red)
- **Legend:** continuous gradient bar (“Low Suitability” → “High Suitability”)
- **Special behavior:** `useInitialView: true` (returns to regional overview)

#### **4 discrete “risk class” layers (100m)**
- `pop_risk_india`, `lc_risk_india`, `pop_risk_bangladesh`, `lc_risk_bangladesh`
- Discrete palette keyed by integer classes: `{0, 1, 2, 3, 4, 6, 9}`
- **Legend:** checkbox list to toggle class visibility (per layer)
- **View targeting:** custom `center` + `zoom` per region/layer

#### **4 continuous uncertainty layers labeled “SD” (500m)**
- `sd_india_pop`, `sd_india_lc`, `sd_bang_pop`, `sd_bang_lc`
- Continuous chroma scale with numeric domains (e.g., `[0, 0.099]`)
- **Legend:** continuous gradient bar (“Low” → “High”)
- **View targeting:** custom `center` + `zoom` per region

### **B) Vector overlays (`vectorConfigs`)**

#### **outbreaks (points)**
- File: `outbreaks.json`
- Style: `circleMarker` (blue fill, white stroke, radius 7)
- Popup fields: `Latitude`, `Longitude`, `Year`, `Reference`
- Uses `pointsPane` (above admin boundaries)

#### **study (polygon)**
- File: `study.geojson`
- Dashed outline, transparent fill
- Marked **non-interactive** (no hover/click)

#### **district_2026 (polygon)**
- File: `nipah_2026.json`
- White fill, dashed outline, semi-transparent
- Uses a **custom popup** containing narrative text and an external link to WHO

#### **admins_with_records (polygon)**
- File: `admins_with_records.json`
- Outline-only, initially hidden (`visible: false`)
- Interactive with popup showing `Name`
- Uses `adminPane`

## **6) Rendering logic for rasters (important implementation detail)**

### **Continuous rasters**
- Creates `chroma.scale(config.colors).domain(config.domain)`
- Per pixel:
  - Returns `scale(value).hex()` for valid numeric values
  - Returns `null` for nodata/NaN → transparent

### **Discrete rasters (classed risk)**
- Pixel values are rounded: `Math.round(value)`
- Each class can be toggled via: `visibleClasses[layerId][classValue]`
- If a class is disabled, the pixel returns `null` → transparent  
- Enables interactive “masking” of classes without reloading the raster

### **Performance/quality settings**
GeoRasterLayer uses:
- `opacity: 0.7` (default)
- `resolution: 256` (render sampling; lower = faster, higher = sharper/heavier)
- `pane: 'rasterPane'`

## **7) UI controls and interactions**

### **Layer Control (top-left)**
A custom Leaflet control containing:
- Basemap radio buttons (OSM / Esri street / Esri imagery)
- Raster layer radio buttons (explicit ordering, default `enm`)
- Vector overlay checkboxes (toggle show/hide)
- Per-raster opacity sliders (0–100%, default 70%) calling `layer.setOpacity(opacity)`

### **Legend (bottom-right)**
Updates dynamically for the active raster layer:
- **Discrete risk:** checkbox list by class + “All/None” buttons
- **Continuous:** gradient bar + low/high labels
- Optional description text per layer

### **Info box (top-right)**
Static text describing:
- Multi-scale nature (1km regional vs 100m detail)
- Right-click behavior (copy coordinates)

### **Measurement / drawing tools (leaflet-draw)**
- Enabled drawing: **polyline** and **polygon**
- Disabled: rectangle, circle, marker, circlemarker
- On creation:
  - Polyline: sums segment distances; popup shows **km**
  - Polygon: geodesic area via `L.GeometryUtil.geodesicArea`; popup shows **km²**
- Includes edit/remove support for drawn shapes

### **Right-click coordinate copy**
- `contextmenu` handler copies `lat,lng` to 6 decimals
- Clipboard API if available; fallback textarea copy
- Temporary on-screen notification

## **8) Layer switching behavior**

When switching rasters:
- Removes previous raster: `map.removeLayer(layers[currentLayer])`
- Adds selected raster
- Updates legend
- Adjusts map view:
  - If `useInitialView`: returns to `[18.0, 82.5]`, `zoom=6`
  - Else uses that layer’s `center` + `zoom`
- Adjusts boundary outline colors depending on raster type:
  - Risk layer (`pop_risk_*`, `lc_risk_*`) → **white** outlines
  - Non-risk layers (e.g., `SD`, `ENM`) → **black** outlines

## **9) Loading strategy and sequencing**

- Immediately starts loading **all rasters**: `Object.keys(layerConfigs).forEach(loadLayer)`
- After **1 second**, starts loading **all vectors**
- Tracks raster progress with `loadedCount / totalLayers`
- First displayed raster is explicitly **`enm`** (not first-loaded or alphabetical)

## **10) Error handling and diagnostics**

- `fetch()` failures throw with HTTP status and log errors to console
- `updateStatus()` currently logs to console (CSS exists for `.loading` / `.error`, but no visual widget is wired up)
- Includes a diagnostic block for discrete risk rasters:
  - Logs expected discrete values vs observed min/max and sampled unique pixel values
  - Helps detect mismatches between raster encoding and `discreteColors` keys

## **11) Technical assumptions / constraints (implicit specs)**

- Raster and vector files must be reachable via browser `fetch()`:
  - Opening via `file://` often breaks due to browser security/CORS rules  
  - Serve via HTTP(S) (local server or hosted site)
- GeoTIFFs must be compatible with in-browser parsing (projection and bounds are read from the file)
- Discrete “risk” rasters are assumed to contain integer-like class values (or values very close to integers)
