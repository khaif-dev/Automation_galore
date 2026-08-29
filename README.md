# Automation

Collection of Python notebooks to automate repetitive data tasks.

## Repository Structure

```
Automation/
├── notebooks/
│   ├── raster_data_profiler.ipynb
│   ├── tabular_data_profiler.ipynb
│   └── vector_data_converter.ipynb
├── data/
│   ├── Kisumu.*
│   ├── Mombasa.*
│   ├── Nairobi.*
│   └── Nakuru.*
├── Export/
│   ├── Kisumu.gpkg
│   ├── Mombasa.gpkg
│   ├── Nairobi.gpkg
│   └── Nakuru.gpkg
├── Shamiri/
│   ├── data/
│   │   ├── Inputs/
│   │   └── academic_QA_Exports/
│   ├── notebooks/
│   │   ├── 01_convert_pdf_to_xlsx.ipynb
│   │   ├── 02_add_shamiri_ID.ipynb
│   │   └── Survey_QA.ipynb
│   └── reports/
└── .gitignore
```

## General Data Automation

### Vector Data Converter

Converts ESRI Shapefiles (.shp) to OGC GeoPackage (.gpkg) format.

**Input:** `data/*.shp`  
**Output:** `Export/*.gpkg`

### Tabular Data Profiler

Profiles tabular datasets (CSV, XLSX, XLS) and exports a multi-sheet Excel report containing:
- Dataset summary (records, fields, duplicates)
- Field profile (type, missing, unique, duplicates)
- Numeric summary (min, max, mean, median, std, zeros, negatives)
- Categorical summary (value counts and percentages)
- Duplicate summary

**Input:** Place files in `dataset/` folder  
**Output:** `reports/<filename>_dataprofile.xlsx`

### Raster Data Profiler

Profiles raster datasets (GeoTIFF, IMG, JP2, VRT) and exports a multi-sheet Excel report containing:
- Dataset summary (driver, dimensions, CRS, resolution, data types)
- Band profile (min, max, mean, median, std, valid/no-data pixels, zeros, negatives)
- Spatial summary (bounds, transform, tiling, compression, overviews)

Uses reservoir sampling for memory-efficient median computation on large rasters.

**Input:** Place files in `dataset/` folder  
**Output:** `reports/<filename>_rasterprofile.xlsx`

## Shamiri Academic Data QA

End-to-end pipeline for cleaning and validating school academic records.

### Stage 1 — PDF to Excel Conversion

**Notebook:** `Shamiri/notebooks/01_convert_pdf_to_xlsx.ipynb`

Batch converts tabular PDF files to `.xlsx` workbooks. Extracts all tables per page, classifies them into:
- Student academic data
- Grade breakdown tables
- Class grade summary tables

**Input:** `Shamiri/data/Inputs/*.pdf`  
**Output:** `Shamiri/data/Inputs/<filename>.xlsx`

### Stage 2 — Add School Name and Shamiri ID

**Notebook:** `Shamiri/notebooks/02_add_shamiri_ID.ipynb`

Prepares datasets for analysis by:
1. Matching each file to its school using filename aliases in `schools.csv`
2. Adding a `School Name` column
3. Adding a `Shamiri ID` column by matching on `School Name` + `Admission number` against `prefilling_data.csv`
4. Normalizing admission number column names across files

**Input:** `Shamiri/data/Inputs/*.xlsx`  
**Output:** `Shamiri/data/academic_QA_Exports/*.xlsx`

### Stage 3 — Survey QA

**Notebook:** `Shamiri/notebooks/Survey_QA.ipynb`

Validates survey data and generates Excel reports with the following checks:
- Missing pages (baseline: 10 pages, endline: 7 pages)
- School name mismatches against reference school list
- Admission number mismatches between survey and prefilling data

**Input:** `Shamiri/data/*.csv`  
**Output:** `Shamiri/reports/<date>_<report_name>.xlsx`

## Requirements

Common dependencies across notebooks:

```bash
pip install pandas numpy rasterio geopandas pdfplumber openpyxl
```

## Notes

- `data/` and `Export/` directories at the root are gitignored and intended to be populated locally.
- `Shamiri/data/` is gitignored to protect sensitive student data.
