# Vector Data Profiler

A Python-based geospatial data profiling tool for assessing the structure, attribute quality, and spatial characteristics of vector datasets.

The profiler is designed to provide a repeatable first-pass quality assessment of spatial data and generate Excel reports that can be used for data cleaning, quality assurance, analysis preparation, and documentation.

## Overview

The Vector Data Profiler automatically discovers supported spatial datasets within a dataset directory, profiles each dataset, and exports the profiling results as Excel workbooks.

The profiling workflow examines five main areas:

1. **Dataset information**
2. **Field/attribute quality**
3. **Numeric attributes**
4. **Categorical attributes**
5. **Spatial/geometry quality**

The tool is intended to help identify potential data-quality issues before a dataset is used for GIS analysis, mapping or modelling workflows.


## Features

### Dataset profiling

The profiler captures general information about each spatial dataset, including:

- Dataset name
- Number of records
- Number of fields
- Coordinate Reference System (CRS)
- Geometry information
- Dataset-level characteristics

### Field profiling

Attribute fields are assessed for:

- Data type
- Missing values
- Missing-value percentage
- Number of unique values
- Duplicate values
- Field-level completeness

This helps identify fields that may require cleaning or further investigation.

### Numeric profiling

Numeric fields are summarized using descriptive statistics such as:

- Minimum
- Maximum
- Mean
- Median
- Standard deviation
- Zero values
- Negative values

These statistics provide an initial understanding of the distribution and potential anomalies within numeric attributes.

### Categorical profiling

Categorical fields are examined through value-frequency summaries, including:

- Distinct values
- Value counts
- Percentage distributions

This is useful for identifying inconsistent categories, unexpected values, and highly dominant or sparse categories.

### Spatial profiling

The profiler examines spatial characteristics including:

- Geometry types
- Null geometries
- Empty geometries
- Invalid geometries
- Duplicate geometries
- Spatial extent/bounding coordinates
- Coordinate Reference System

This provides an initial spatial quality assessment before performing further GIS analysis.


## Supported Formats

The current profiler is intended for common vector data formats:

| Format | Extension |
|---|---|
| ESRI Shapefile | `.shp` |
| GeoPackage | `.gpkg` |
| GeoJSON | `.geojson` |
| KML | `.kml` |

The profiler should **not automatically treat every `.json` file as a spatial dataset**. Dataset directories may contain files such as `metadata.json` that are metadata rather than spatial data.

---

## Requirements

The profiler requires Python and the following core libraries:

- `pandas`
- `geopandas`
- `openpyxl`
- `numpy`
- `pathlib`

A typical environment can be prepared with:

```bash
pip install pandas geopandas openpyxl numpy
```

For a more controlled environment, it is recommended to use a Python virtual environment or Conda environment.


## Project Structure

A typical project structure is:

```text
vector-data-profiler/
│
├── vectordataprofiler.ipynb
│
├── dataset/
│   ├── roads/
│   │   ├── roads_lines.shp
│   │   ├── roads_lines.shx
│   │   ├── roads_lines.dbf
│   │   └── roads_lines.prj
│   │
│   ├── administrative_boundaries/
│   │   ├── ken_admin0.shp
│   │   ├── ken_admin1.shp
│   │   └── ...
│   │
│   └── schools/
│       ├── Schools.shp
│       └── ...
│
└── reports/
    ├── roads_lines_dataprofile.xlsx
    ├── ken_admin0_dataprofile.xlsx
    └── Schools_dataprofile.xlsx
```

The notebook recursively searches the `dataset` directory for supported spatial datasets.


## How It Works

The profiling workflow follows this general process:

```text
Dataset directory
       │
       ▼
Discover spatial datasets
       │
       ▼
Identify supported formats
       │
       ▼
Read vector dataset
       │
       ▼
Profile dataset
       │
       ├── Dataset information
       ├── Field profile
       ├── Numeric profile
       ├── Categorical profile
       └── Spatial profile
       │
       ▼
Generate Excel report
       │
       ▼
Save report to reports/
```

Each dataset is processed independently. If one dataset fails, the error should be reported without preventing the profiler from processing the remaining datasets.


## Excel Output

Each successfully profiled dataset produces an Excel workbook.

The report contains the following main sections:

### Dataset Summary

Provides an overview of the dataset and its general spatial characteristics.

### Field Profile

Provides field-level information covering data types, completeness, uniqueness, and duplicates.

### Numeric Summary

Provides descriptive statistics for numeric attributes.

### Categorical Summary

Provides frequency and percentage distributions for categorical attributes.

### Spatial Summary

Provides information about geometry quality, CRS, spatial extent, and potential geometry problems.


## Data Quality Interpretation

The profiler is intended as a **diagnostic tool**, not as an automatic replacement for expert data validation.

## Error Handling

The profiler processes datasets independently.

Errors should be separated into two categories:

### Profiling errors

These occur when the spatial dataset cannot be read or analyzed.

Examples include:

- Unsupported or corrupted spatial files
- Missing Shapefile components
- Invalid data structures
- Driver/read errors

### Export errors

These occur after profiling has succeeded but the Excel report cannot be created.

Examples include:

- File permission problems
- Existing file conflicts
- Workbook generation errors


## Important Notes

### Shapefiles are multi-file datasets

A Shapefile normally consists of several associated files, including:

```text
.shp
.shx
.dbf
.prj
```

These files should remain together.

### Metadata files

Dataset directories frequently contain additional files such as:

```text
metadata.json
README.txt
license.txt
```

These should not automatically be interpreted as spatial datasets.

### Excel reports

The profiler generates Excel workbooks using `openpyxl`. Each workbook should contain at least one visible worksheet before it is saved.

If an export fails with:

```text
At least one sheet must be visible
```

the issue is associated with workbook creation/finalization rather than the spatial dataset itself.

---

## Recommended Workflow

A practical workflow is:

1. Place spatial datasets in the `dataset` directory.
2. Keep associated spatial files together.
3. Run the notebook.
4. Review the discovered datasets.
5. Run the profiling process.
6. Check the console for profiling or export errors.
7. Open the generated Excel reports.
8. Review missingness and uniqueness.
9. Review categorical distributions.
10. Review numeric statistics.
11. Investigate spatial-quality issues.
12. Clean or validate the original dataset.
13. Re-run the profiler to confirm improvements.


## Intended Applications

The Vector Data Profiler can support:

- GIS data cleaning
- Spatial data QA/QC
- Data integration
- ETL workflows
- Geospatial database preparation
- Cartographic, Remote sensing and research  data preparation

## Limitations

The current profiler provides a statistical and structural overview of vector datasets. It does not automatically determine whether a dataset is **correct for a particular research, GIS, or M&E requirement**.


## Future Development

Potential extensions include:

- Automated data-quality scoring
- Data-quality issue classification
- Automated cleaning recommendations
- Geometry repair workflows
- Outlier detection
- Domain/value validation
- Field naming validation
- Schema comparison
- Cross-layer consistency checks
- CRS consistency checks
- Spatial overlap analysis
- Duplicate feature detection
- Interactive HTML reports
- Profiling dashboards
- Automated QA/QC summaries
- Batch processing
- Configurable profiling rules
- GIS-ready issue layers

A future version could therefore evolve from a profiling notebook into a reusable **geospatial data QA/QC framework**.

---

## Author

**Immaculate Khaoma**

Geospatial Data Analyst | M&E Officer

The profiler reflects an approach to geospatial data quality that combines spatial analysis, attribute profiling, and reproducible data workflows.

