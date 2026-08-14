# Raster Data Profiler

A Python-based raster data profiling tool for inspecting the structural, statistical, and spatial characteristics of raster datasets.

The profiler is designed as a lightweight first-pass **raster data quality assessment and metadata inspection tool**. It automatically discovers raster datasets, analyzes each band, calculates descriptive statistics, examines raster properties, and exports the results to Excel workbooks.

---

## Overview

Raster datasets often contain important information that is not immediately visible from the raster itself. Before using imagery, digital elevation models, land-cover products, satellite-derived datasets, or other raster layers for analysis, it is useful to understand:

- Raster dimensions
- Number of bands
- Coordinate Reference System (CRS)
- Pixel resolution
- Data types
- NoData values
- Value ranges
- Pixel statistics
- Missing-data proportions
- Zero and negative values
- Spatial extent
- Raster tiling and block structure
- Compression
- Overviews

The Raster Data Profiler automates this initial assessment and produces a structured Excel report for each raster dataset.

---

## Key Features

### 1. Automatic Dataset Discovery

The profiler recursively searches a `dataset` directory and its child folders for supported raster formats.

Supported formats are:

| Format | Extension |
|---|---|
| GeoTIFF | `.tif` |
| TIFF | `.tiff` |
| ERDAS IMAGINE | `.img` |
| JPEG 2000 | `.jp2` |
| Virtual Raster | `.vrt` |

This allows multiple raster datasets stored in different subdirectories to be profiled in a single run.

---

## 2. Dataset Summary

For each raster, the profiler records general dataset information including:

- File name
- Raster driver
- Width
- Height
- Number of bands
- CRS
- X resolution
- Y resolution
- Data types
- NoData values
- Spatial bounds

The spatial bounds include:

```text
min_x
min_y
max_x
max_y
```

This provides a quick overview of the raster's geographic footprint and structure.

---

## 3. Band-Level Profiling

Each raster band is profiled independently.

For every band, the profiler calculates:

- Band number
- Band description
- Data type
- NoData value
- Minimum value
- Maximum value
- Mean
- Median
- Standard deviation
- Valid pixel count
- NoData count
- NoData percentage
- Number of zero-valued pixels
- Number of negative-valued pixels

---

## 4. NoData Analysis

The profiler distinguishes between valid pixels and NoData pixels.

For each band it reports:

```text
Valid Pixels
NoData Count
NoData %
```

The NoData percentage is calculated relative to the total number of pixels:

```text
NoData % = NoData pixels / Total pixels × 100
```

This provides a quick indication of the completeness of the raster.

High NoData proportions may indicate:

- Areas outside the source data footprint
- Cloud or data-quality masking
- Processing gaps
- Missing observations
- Raster clipping
- Incomplete source data

The significance of NoData depends on the intended use and source dataset.

---

## 5. Raster Value Statistics

The profiler calculates descriptive statistics for valid pixel values.

### Minimum

The smallest valid pixel value.

### Maximum

The largest valid pixel value.

### Mean

The arithmetic mean of valid pixel values.

### Median

The middle value estimated from a sample of valid pixels.

### Standard Deviation

A measure of the dispersion of valid pixel values around the mean.

These statistics can provide an initial indication of the distribution and range of raster values.

---

## 6. Large Raster Handling

The profiler processes raster data **block by block** rather than loading the entire raster into memory at once.

This is implemented using Rasterio's block windows:

```python
src.block_windows()
```

This approach is useful when working with large raster datasets because the complete raster does not need to be loaded into memory simultaneously.

---

## 7. Median Sampling

For the median calculation, the profiler limits the sample size to:

```text
100,000 pixels
```

The default function configuration is:

```python
profile_raster_data(filepath, sample_size=100_000)
```

A fixed random seed is used:

```python
np.random.default_rng(42)
```

This makes the sampling process reproducible.

The sampling approach reduces the computational and memory cost of calculating a median for very large rasters while still providing a useful estimate.

---

## 8. Spatial and Raster Summary

The profiler records additional raster properties including:

- CRS
- Width
- Height
- Number of bands
- Pixel width
- Pixel height
- Minimum X
- Minimum Y
- Maximum X
- Maximum Y
- Affine transform
- Tiled status
- Block shapes
- Compression
- Available overviews

These properties are useful for understanding how the raster is physically structured and how it may behave in downstream GIS workflows.

---

## Excel Output

Each successfully processed raster produces an Excel workbook in the `reports` directory.

The workbook contains three main sheets:

```text
Dataset_Summary
Band_Profile
Spatial_Summary
```

### Dataset_Summary

Contains general raster metadata and dimensions.

Example fields include:

```text
File
Driver
Width
Height
Bands
CRS
Resolution X
Resolution Y
Data Types
NoData Values
Bounds
```

### Band_Profile

Contains statistical and quality information for every raster band.

Example fields include:

```text
Band
Description
Data Type
NoData
Min
Max
Mean
Median
Std
Valid Pixels
NoData Count
NoData %
Zeros
Negative
```

### Spatial_Summary

Contains spatial and raster-structure information such as:

```text
CRS
Width
Height
Bands
Pixel Width
Pixel Height
Min X
Min Y
Max X
Max Y
Transform
Tiled
Block Shapes
Compression
Overviews
```

---

## Project Structure

A typical project structure is:

```text
raster-data-profiler/
│
├── rasterdataprofiler.ipynb
│
├── dataset/
│   ├── elevation/
│   │   └── dem.tif
│   │
│   ├── landcover/
│   │   └── landcover.tif
│   │
│   └── imagery/
│       ├── image.tif
│       └── image.jp2
│
└── reports/
    ├── dem_rasterprofile.xlsx
    ├── landcover_rasterprofile.xlsx
    └── image_rasterprofile.xlsx
```

The `reports` directory is created automatically if it does not already exist.

---

## Workflow

The profiler follows this workflow:

```text
Raster datasets
       │
       ▼
Dataset discovery
       │
       ▼
Identify supported formats
       │
       ▼
Open raster with Rasterio
       │
       ▼
Read raster metadata
       │
       ▼
Profile each band
       │
       ├── Value statistics
       ├── NoData analysis
       ├── Zero values
       └── Negative values
       │
       ▼
Profile raster structure
       │
       ├── CRS
       ├── Resolution
       ├── Extent
       ├── Transform
       ├── Tiling
       ├── Compression
       └── Overviews
       │
       ▼
Generate Excel report
```

---

## Requirements

The profiler uses the following Python libraries:

- `rasterio`
- `pandas`
- `numpy`
- `openpyxl`

Install the dependencies using:

```bash
pip install rasterio pandas numpy openpyxl
```

For geospatial projects, a Conda environment may also be used to manage Rasterio and its GDAL-related dependencies.

---

## Usage

### 1. Prepare the dataset directory

Place raster datasets inside:

```text
dataset/
```

Subdirectories are supported.

For example:

```text
dataset/
├── DEM/
│   └── kenya_dem.tif
├── landcover/
│   └── kenya_landcover.tif
└── imagery/
    └── sentinel_image.tif
```

### 2. Run the notebook

Open:

```text
rasterdataprofiler.ipynb
```

Run the cells sequentially.

The profiler will first identify supported raster datasets.

### 3. Review the generated reports

Reports are saved automatically under:

```text
reports/
```

with filenames following the pattern:

```text
<raster_name>_rasterprofile.xlsx
```

---

## Interpreting the Results

The profiler should be considered a **diagnostic and exploratory tool**, rather than an automated declaration that a raster is correct or incorrect.

### High NoData %

A high NoData percentage may be expected for:

- clipped imagery
- irregular study areas
- masked satellite imagery
- mosaics
- datasets covering only selected geographic areas

Therefore, NoData percentages should be interpreted against the expected spatial footprint.

### Large value ranges

Large or unexpected ranges should be investigated in relation to the raster's purpose.

For example:

- Elevation
- Temperature
- Reflectance
- Population density
- Land-cover codes
- Probability surfaces

all have very different expected value domains.

### Negative values

Negative values are reported but are not automatically classified as errors.

Negative values can be valid for datasets such as:

- Elevation
- Temperature
- Change detection
- Statistical surfaces

The meaning depends on the source data.

### Zero values

Zero-valued pixels are also reported without automatically classifying them as invalid.

For some rasters, zero is a meaningful measurement or class. For others, it may represent background or an encoded missing value.

---

## Quality Assurance Applications

The profiler can support raster QA/QC activities such as:

- Pre-analysis data inspection
- Remote sensing data preparation
- DEM quality assessment
- Land-cover data inspection
- Satellite imagery review
- Raster ETL workflows
- Data integration
- GIS database preparation
- Research data validation
- Environmental analysis
- Monitoring and evaluation workflows

It is particularly useful as an initial screening step before more detailed raster-specific validation.

---

## Design Considerations

### Block-Based Processing

Raster values are processed block by block. This avoids unnecessarily loading an entire raster into memory.

### Reproducible Sampling

Median estimation uses a fixed random seed, making repeated profiling runs reproducible for the same input raster.

### Independent Dataset Processing

Each raster is processed independently. An error affecting one raster is reported by the error handler rather than being silently ignored.

---

## Current Limitations

The current profiler focuses on **metadata, raster structure, and basic pixel-value statistics**.

It does not currently perform advanced raster validation such as:

- Automated outlier detection
- Spatial pattern analysis
- Histogram generation
- Correlation between bands
- Band-to-band consistency checks
- Cloud detection
- Raster alignment checks between multiple datasets
- Pixel-level anomaly mapping
- Automated CRS validation against a project specification
- Automated value-domain validation
- Automated quality scoring
- Raster repair or correction

Consequently, the generated report should be treated as a starting point for raster QA/QC rather than a complete validation framework.

---

## Potential Future Development

The profiler can be extended into a more comprehensive raster QA/QC framework.

Potential additions include:

### Data Quality Scoring

Generate an overall quality score based on configurable rules such as:

```text
CRS present
NoData percentage
Valid pixel percentage
Expected data type
Expected value range
Expected resolution
Expected band count
```

### Value-Domain Validation

Allow users to define expected ranges.

### Raster Comparison

Compare multiple rasters for:

- CRS
- Resolution
- Extent
- Alignment
- Dimensions
- Pixel size

### Histogram and Distribution Reports

Add visual summaries of pixel-value distributions to the Excel report or a separate HTML dashboard.

### Spatial QA Layers

Generate spatial outputs identifying:

- NoData areas
- Outliers
- Invalid values
- Anomalous pixels

### Interactive Reporting

A future version could generate an interactive HTML dashboard containing:

- Raster metadata
- Statistics
- Histograms
- Maps
- Quality flags
- Data-quality scores

---

## Relationship to Vector Data Profiling

This raster profiler complements a vector data profiling workflow.

The two tools follow a similar philosophy:

```text
                 Geospatial Data Profiler
                         │
             ┌───────────┴───────────┐
             │                       │
        Vector Profiler         Raster Profiler
             │                       │
       Attribute QA/QC          Pixel QA/QC
       Geometry QA/QC           Raster QA/QC
       Field statistics         Band statistics
       CRS / extent             CRS / extent
       Categorical data         Pixel distributions
```

Together, they provide a foundation for a broader geospatial data quality toolkit covering both vector and raster datasets.

---

## Author

**Immaculate Khaoma**

Geospatial Data Analyst | M&E Officer



