# SWAT+ Drought Characterization and Propagation Analysis using the SWAT+ Drought Tool

A comprehensive Python-based framework for analyzing drought characteristics, propagation mechanisms, and evapotranspiration performance using SWAT+ model outputs. This project implements automated drought event identification, characterization, propagation analysis, and spatial ET performance evaluation for any watershed modeled with SWAT+.

## Overview

This repository contains tools for analyzing drought propagation from precipitation deficits to streamflow droughts using SWAT+ hydrological model outputs. The analysis focuses on understanding how meteorological droughts translate into hydrological droughts across different landscape units (LSUs) and their corresponding stream channels in any SWAT+ modeled watershed. Additionally, it provides a comprehensive spatial performance framework for evaluating SWAT+ evapotranspiration estimates against reference data.

### Key Features

- **Automated Drought Detection**: Identifies drought events using Flow Duration Curve (FDC) percentile thresholds
- **Drought Propagation Analysis**: Analyzes relationships between precipitation and streamflow droughts
- **Multi-scale Analysis**: Processes multiple LSU-channel pairs across your watershed
- **Comprehensive Metrics**: Calculates lag times, attenuation ratios, and drought characteristics
- **Spatial Analysis**: Integrates catchment characteristics (land use, soil type, topography)
- **ET Performance Evaluation**: Systematic spatial assessment of SWAT+ evapotranspiration performance
- **Machine Learning Ready**: Prepares features for drought prediction modeling

## 📁 Repository Structure

```
├── scripts/                                         # Analysis notebooks and scripts
│   ├── droughtPropagationAnalysis.ipynb                 # Main drought analysis workflow
│   ├── droughtPropagationAnalysisFormAntecedent.ipynb   # Data aggregation and ML preparation
│   ├── triggerLSUautoRun.ipynb                          # Automated batch processing
│   └── eTCalibrationMethod.ipynb                        # ET spatial performance evaluation (bonus)
├── exampleData/                                     # Example SWAT+ data and spatial files
│   ├── channel_sdmorph_day.txt                          # Example channel flow data
│   ├── lsunit_wb_day.txt                                # Example LSU water balance data
│   ├── landuse.tif                                      # Example land use raster
│   ├── soil.tif                                         # Example soil type raster
│   ├── lsu121/                                          # Example LSU shapefile
│   │   ├── lsu121.shp
│   │   ├── lsu121.dbf
│   │   └── [other shapefile components]
│   └── rivsLSU121/                                      # Example river network shapefile
│       ├── rivs1.shp
│       ├── rivs1.dbf
│       └── [other shapefile components]
├── requirements.txt                                 # Python dependencies
├── LICENSE                                          # License file
└── README.md                                        # This file
```

## Analysis Components

### 1. Drought Propagation Analysis (`droughtPropagationAnalysis.ipynb`)

The main analysis notebook that processes individual LSU-channel pairs to:
- Identify drought events in precipitation and streamflow time series
- Classify drought relationships (initiating, sustaining, standalone)
- Calculate propagation metrics (lag time, attenuation, lengthening)
- Generate comprehensive drought databases

### 2. Automated Batch Processing (`triggerLSUautoRun.ipynb`)

Automates the main drought analysis across multiple LSU-channel combinations:
- Processes all LSU-channel pairs in the watershed
- Implements error handling and progress tracking
- Generates individual results for each pair
- Facilitates basin-wide drought analysis

### 3. Data Aggregation and ML Preparation (`droughtPropagationAnalysisFormAntecedent.ipynb`)

Combines individual results and prepares machine learning features:
- Aggregates drought databases from all LSU-channel pairs
- Extracts catchment characteristics (land use, soil type, topography)
- Calculates antecedent conditions and time of concentration
- Prepares feature matrices for drought prediction modeling

### 4. ET Spatial Performance Evaluation (`eTCalibrationMethod.ipynb`)

**Bonus Analysis Framework**: A comprehensive spatial performance evaluation tool that systematically identifies regions where SWAT+ poorly represents evapotranspiration and targets specific soil-land use combinations for model refinement.

#### Methodology

The ET performance evaluation framework implements a multi-step approach:

1. **Data Harmonization**: Spatially harmonizes all input raster data (SWAT+ HRU ET, reference ET, soil and land use) and excludes water bodies and herbaceous wetlands

2. **Spatial Error Quantification**: Calculates pixel-wise weighted Mean Absolute Relative Error (wMARE):
   ```
   wMARE = |ET_observed - ET_predicted| / ET_observed × 100
   ```

3. **Spatial Smoothing**: Applies moving window spatial averaging filter to wMARE values to remove localized noise and enhance regional patterns

4. **Performance Classification**: Classifies areas into performance categories based on established thresholds:
   - **Good**: 0-10% wMARE
   - **Moderate**: 10-25% wMARE  
   - **Poor**: 25-50% wMARE
   - **Very Poor**: >50% wMARE

5. **Region Identification**: Uses morphological image analysis to identify connected regions within poor and very poor classes, retaining only contiguous areas ≥20km² to focus on substantial zones of systematic model deficiency

6. **Diagnostic Analysis**: Extracts dominant soil type and land use class for each poor performance region, enabling identification of specific soil-land use combinations consistently producing poor ET estimates

#### Key Features

- **Automated Spatial Analysis**: Processes large raster datasets efficiently
- **Performance Thresholds**: Configurable thresholds for performance classification
- **Region Characterization**: Identifies and analyzes poor performance regions
- **Soil-Land Use Diagnostics**: Links poor performance to specific combinations
- **Visualization Tools**: Generates maps and summary statistics
- **Export Capabilities**: Saves results as georeferenced rasters and CSV files

## 🔧 Requirements

### Python Dependencies

```py
pandas>=1.3.0
numpy>=1.21.0
matplotlib>=3.4.0
seaborn>=0.11.0
scipy>=1.7.0
python-dateutil>=2.8.0
jupyter>=1.0.0
notebook>=6.4.0
ipykernel>=6.0.0
geopandas>=0.10.0
rasterio>=1.2.0
rasterstats>=0.15.0
scikit-learn>=1.0.0
papermill>=2.3.0
```

### Data Requirements

#### Drought Analysis
1. **SWAT+ Model Outputs**:
   - `lsunit_wb_day.txt` - Daily water balance for landscape units
   - `channel_sdmorph_day.txt` - Daily channel flow data

2. **Spatial Data**:
   - LSU shapefile (`lsus2.shp`)
   - Stream network shapefile (`rivs1.shp`)
   - Land use raster (500m resolution)
   - Soil type raster (500m resolution)

#### ET Performance Evaluation
1. **ET Raster Data**:
   - Reference ET data (e.g., WaPOR ET average)
   - SWAT+ modeled ET raster
   - Watershed boundary shapefile
   - Water bodies mask (optional)

2. **Auxiliary Data**:
   - Soil type raster
   - Land use raster

### File Path Configuration

Update the following paths in the notebooks:

#### Drought Analysis
```python
lsu_file_path = r'path/to/lsunit_wb_day.txt'
channel_data_path = r'path/to/channel_sdmorph_day.txt'
lsu_shapefile = r'path/to/lsus2.shp'
stream_shapefile = r'path/to/rivs1.shp'
landuse_raster = r'path/to/landuse_raster.tif'
soil_raster = r'path/to/soil_raster.tif'
```

#### ET Performance Evaluation
```python
Observed_ET_PATH = r"path/to/your/Observed_ET_average.tif"
SWAT_ET_PATH = r"path/to/your/SWAT_modeled_ET.tif"
WATERSHED_BOUNDARY = r"path/to/your/watershed_boundary.shp"
SOIL_RASTER = r"path/to/your/soil_raster.tif"
LANDUSE_RASTER = r"path/to/your/landuse_raster.tif"
OUTPUT_DIR = r"path/to/output/directory"
```

## Quick Start

### 1. Single LSU Drought Analysis

Run the main analysis for a single LSU-channel pair:

```python
# Set LSU and channel IDs
lsu_id = "rtu132"
channel_id = "cha13"

# Run the analysis notebook
jupyter notebook droughtPropagationAnalysis.ipynb
```

### 2. Batch Drought Processing

Process all LSU-channel pairs from your watershed automatically:

```python
# Run the automated batch processor
jupyter notebook triggerLSUautoRun.ipynb
```

### 3. Data Aggregation and ML Preparation

Combine results and prepare for machine learning:

```python
# Aggregate all results and add catchment characteristics
jupyter notebook droughtPropagationAnalysisFormAntecedent.ipynb
```

### 4. ET Performance Evaluation

Evaluate SWAT+ ET performance spatially:

```python
# Configure paths and parameters in the notebook
# Run the ET performance evaluation
jupyter notebook eTCalibrationMethod.ipynb
```

##  Analysis Workflow

### Drought Propagation Workflow

#### Step 1: Drought Event Identification
The system identifies drought events using:
- **Threshold**: 30th percentile (70% Flow Duration Curve)
- **Pooling**: Events separated by ≤3 days are combined
- **Minimum Duration**: 5 days
- **Minimum Volume**: 5% of maximum deficit volume

#### Step 2: Drought Relationship Classification
Drought relationships are classified as:
- **Initiating**: Precipitation droughts that trigger streamflow droughts
- **Sustaining**: Precipitation droughts that overlap with ongoing streamflow droughts
- **Standalone**: Droughts that don't propagate or aren't sustained

#### Step 3: Propagation Metrics Calculation
Key metrics include:
- **Lag Time**: Days between precipitation and streamflow drought onset
- **Attenuation**: Reduction in drought intensity (1 - Q_deficit/P_deficit)
- **Lengthening**: Extension of streamflow drought duration
- **P-to-Q Likelihood**: Probability of precipitation drought causing streamflow drought

#### Step 4: Catchment Characterization
Spatial analysis extracts:
- Dominant land use and soil types
- Stream network characteristics
- Time of concentration (Kirpich method)
- Antecedent conditions (7, 10, 20-day averages)

### ET Performance Evaluation Workflow

#### Step 1: Data Preprocessing
- Load and harmonize ET raster data
- Apply water body masks
- Resample to common resolution and extent

#### Step 2: Error Calculation
- Calculate pixel-wise wMARE
- Apply spatial smoothing filter
- Generate error distribution maps

#### Step 3: Performance Classification
- Apply performance thresholds
- Generate classified performance maps
- Calculate area statistics

#### Step 4: Region Analysis
- Identify connected poor performance regions
- Extract soil-land use characteristics
- Generate diagnostic reports

##  Output Files

### Drought Analysis Outputs

#### Individual LSU Results
- `{lsu_id}_LSU_metrics.csv` - Basic propagation metrics
- `{lsu_id}_LSU_standalone_vs_aligned_metrics.csv` - Comparative analysis
- `{lsu_id}_fullDatabase.csv` - Complete drought relationship database

#### Combined Results
- `combined_watershed.csv` - Basin-wide drought relationship database
- `processed_initiating_events.csv` - Initiating events with ML features
- `lsu_characteristics.csv` - Catchment characteristics database

#### Visualizations
- Time series plots with drought periods highlighted
- Drought cascade visualizations
- Land use and stream order maps

### ET Performance Evaluation Outputs

#### Raster Outputs
- `spatial_mare.tif` - Pixel-wise wMARE values
- `smoothed_mare.tif` - Spatially smoothed wMARE
- `performance_classes.tif` - Performance classification map

#### Analysis Results
- `poor_regions_analysis.csv` - Detailed region characteristics
- `poor_regions_map.png` - Visualization of poor performance regions
- Performance summary statistics

##  Key Analysis Parameters

### Drought Identification
```python
DROUGHT_THRESHOLD_PERCENTILE = 0.3  # 30th percentile
TMIN_DAYS = 3  # Pooling threshold
DMIN_DAYS = 5  # Minimum duration
MIN_DEFICIT_VOLUME_FRACTION = 0.05  # Minimum volume
N_DAYS_TO_ANALYZE = 13149  # ~36 years
```

### Relationship Analysis
```python
max_initial_lag = 60  # Maximum lag for initiating events
max_gap_after_p = 10  # Maximum gap between P end and Q start
overlap_buffer = 10   # Overlap tolerance
leniency_period = 3   # Days of leniency for event classification
```

### ET Performance Evaluation
```python
PERFORMANCE_THRESHOLDS = [10, 25, 50]  # Performance class thresholds (%)
MIN_REGION_SIZE = 20  # Minimum region size (pixels)
SMOOTHING_WINDOW = 7  # Spatial smoothing window size
```

## Usage Examples

### Basic Drought Analysis
```python
# Load and process data
lsu_data = process_large_file(lsu_file_path, lsu_id)
channel_data = pd.read_csv(channel_data_path, sep=r'\s+', skiprows=1)

# Calculate thresholds and identify droughts
precip_percentile_dfs, _ = calculate_percentile_thresholds(
    lsu_data, 'precip_roll_5d', [0.01, 0.05, 0.1, 0.2, 0.3]
)

# Analyze drought relationships
relationships_df = identify_drought_relationships(precip_df, flow_df)
```

### Catchment Characterization
```python
# Extract catchment characteristics
lsu_characteristics = analyze_lsu_characteristics(
    lsu_shapefile, landuse_raster, soil_raster, stream_shapefile
)

# Calculate time of concentration
result_df = calculate_time_of_concentration_kirpich(merged_df)
```

### ET Performance Evaluation
```python
# Load ET data
ref_et, ref_bounds, ref_crs, ref_transform = clip_raster(WAPOR_ET_PATH, WATERBODIES_MASK)
swat_et_resampled = resample_raster(SWAT_ET_PATH, ref_et, ref_transform, ref_crs)

# Calculate spatial MARE
spatial_mare = calculate_spatial_mare(ref_et, swat_et_resampled)

# Classify performance and find poor regions
performance_classes = classify_performance(spatial_mare, PERFORMANCE_THRESHOLDS)
poor_regions = find_poor_regions(performance_classes, MIN_REGION_SIZE)
```
## Research Applications

This framework supports research in:

### Drought Studies
- **Drought Propagation Mechanisms**: Understanding how droughts move through the hydrological cycle in any watershed
- **Early Warning Systems**: Developing precipitation-based streamflow drought predictions for your study area
- **Climate Change Impact Assessment**: Analyzing changing drought patterns across different watersheds
- **Water Resource Management**: Informing drought preparedness strategies for any SWAT+ modeled basin

### Model Evaluation and Improvement
- **ET Performance Assessment**: Systematic evaluation of SWAT+ evapotranspiration estimates
- **Parameter Regionalization**: Identifying soil-land use combinations requiring calibration
- **Model Diagnostics**: Spatial identification of systematic model deficiencies
- **Targeted Calibration**: Focusing calibration efforts on underperforming regions

### Comparative Studies
- **Multi-watershed Analysis**: Comparing drought behavior across different watersheds and climatic regions
- **Model Intercomparison**: Evaluating different model configurations and parameterizations

## Adapting to Your Watershed

To use this framework with your own SWAT+ model:

### 1. Update File Paths
Modify the file paths in all notebooks to point to your SWAT+ output files and spatial data.

### 2. Configure LSU-Channel Pairs
Update the `lsu_numbers` and `channel_numbers` arrays in the notebooks to match your watershed:
```python
# Example: Update these arrays with your LSU and channel IDs
lsu_numbers = ['001', '002', '003', ...]  # Your LSU numbers
channel_numbers = ['01', '02', '03', ...]  # Corresponding channel numbers
```

### 3. Adjust Analysis Parameters
Modify analysis parameters based on your watershed characteristics:
- `N_DAYS_TO_ANALYZE` - Length of your simulation period
- `DROUGHT_THRESHOLD_PERCENTILE` - Adjust based on your climate
- Time windows for antecedent conditions (7, 10, 20 days)
- ET performance thresholds based on your accuracy requirements

### 4. Update Category Mappings
Update the category mappings to match your raster values:

#### Land Use and Soil Categories (Drought Analysis)
```python
landuse_categories = {
    1: 'FOREST',
    2: 'AGRICULTURE',
    # ... your categories
}
```

#### Soil and Land Use Names (ET Analysis)
```python
SOIL_NAMES = {
    0: 'Clay',
    1: 'Sandy Clay',
    # ... your soil types
}

LANDUSE_NAMES = {
    20: 'Rangeland - Brush',
    30: 'Rangeland - Grasses',
    # ... your land use types
}
```

## License

[Add your license information here]

## Citation

If you use this code in your research, please cite:

```bibtex
[Add your citation information here]
```

## Contributing

[Add contribution guidelines if applicable]

## Contact

[Add contact information]

---

**Note**: Update all file paths in the notebooks to match your local data directory structure before running the analysis. The ET calibration method is a bonus analysis framework independent of the main drought propagation analysis and can be used separately for SWAT+ model evaluation purposes.
