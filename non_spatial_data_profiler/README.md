# Non-Spatial Data Profiler

A Python-based data profiling tool for assessing the structure, completeness, uniqueness, statistical characteristics, categorical distributions, and duplication patterns of tabular datasets.

The profiler is designed for non-spatial datasets such as CSV and Excel files and produces structured Excel reports that can be used as an initial data-quality assessment before analysis, reporting, modelling, visualization, or integration into other data systems.

---

## Overview

The Non-Spatial Data Profiler automates the first stage of tabular data quality assessment.

Instead of manually inspecting each dataset, the profiler:

1. Discovers supported datasets automatically.
2. Loads each dataset into a Pandas DataFrame.
3. Generates a dataset-level summary.
4. Profiles every field.
5. Summarizes numeric variables.
6. Profiles categorical/text variables.
7. Identifies duplicate records.
8. Exports the results to an Excel workbook.

The tool is intended to provide a consistent and reproducible way of understanding a dataset before deeper analysis.

---

## Features

### Dataset Summary

For each dataset, the profiler calculates:

- File name
- Number of records
- Number of fields
- Number of duplicate rows
- Dataset memory usage

This provides a high-level overview of the size and basic structure of the dataset.

---

## Field Profile

Every field/column is profiled individually.

The profiler reports:

| Metric | Description |
|---|---|
| Field | Column name |
| Type | Pandas data type |
| Missing | Number of missing values |
| Missing % | Percentage of missing values |
| Unique | Number of unique non-null values |
| Duplicate values | Number of duplicated values |

This helps identify fields that may require cleaning, transformation, validation, or further investigation.

---

## Numeric Data Profiling

Numeric columns are automatically identified using Pandas numeric data types.

For each numeric field, the profiler calculates:

- Minimum
- Maximum
- Mean
- Median
- Standard deviation
- Number of zero values
- Number of negative values

These statistics provide an initial understanding of the distribution and potential anomalies in numerical variables.

---

## Categorical Data Profiling

Text and categorical columns are automatically identified.

For each categorical field, the profiler generates a frequency distribution containing:

- Field name
- Category/value
- Count
- Percentage

Missing values are included in the frequency distribution.

This can help identify:

- Dominant categories
- Rare categories
- Unexpected values
- Inconsistent spelling
- Different representations of the same category
- Missing categorical information


---

## Duplicate Analysis

The profiler evaluates duplicate records using Pandas' `duplicated()` function.

The report includes:

- Number of duplicate rows
- Percentage of duplicate rows

The percentage is calculated relative to the total number of records.

This provides an initial indication of whether duplicate records may affect downstream analysis.

Duplicate records are not automatically treated as errors because legitimate datasets can contain repeated records depending on their structure and intended use.

---

## Supported Formats

The current profiler supports:

| Format | Extension |
|---|---|
| Comma-Separated Values | `.csv` |
| Microsoft Excel | `.xlsx` |
| Legacy Excel | `.xls` |

The profiler identifies these formats automatically from the file extension.

---

## Project Structure

A typical project structure is:

```text
non-spatial-data-profiler/
│
├── Non_spatial_data_profiler.ipynb
│
├── dataset/
│   ├── schools.csv
│   ├── survey_data.xlsx
│   └── monitoring_data.xlsx
│
└── reports/
    ├── schools_dataprofile.xlsx
    ├── survey_data_dataprofile.xlsx
    └── monitoring_data_dataprofile.xlsx
```

The profiler recursively searches the `dataset` directory, meaning datasets can also be organized into subdirectories.

For example:

```text
dataset/
├── education/
│   └── schools.csv
│
├── health/
│   └── facilities.xlsx
│
└── monitoring/
    └── indicators.xlsx
```

---

## Workflow

The profiler follows this workflow:

```text
                 Dataset Directory
                        │
                        ▼
                Dataset Discovery
                        │
                        ▼
                 Supported Format
                     Detection
                        │
                        ▼
                  Load DataFrame
                        │
                        ▼
              ┌─────────┴─────────┐
              │                   │
              ▼                   ▼
       Dataset Summary       Field Profile
              │                   │
              ├──────────┬────────┘
              │          │
              ▼          ▼
       Numeric Data   Categorical Data
              │          │
              └─────┬────┘
                    ▼
              Duplicate Analysis
                    │
                    ▼
              Compile Report
                    │
                    ▼
              Excel Workbook
```

---

## Requirements

The profiler uses the following Python libraries:

- `pandas`
- `numpy`
- `openpyxl`
- `pathlib`

Install the required packages with:

```bash
pip install pandas numpy openpyxl
```

For `.xls` files, Pandas may require an appropriate Excel engine such as `xlrd`.

If necessary:

```bash
pip install xlrd
```

---

## Usage

### 1. Prepare the dataset directory

Create a directory named:

```text
dataset/
```

Place the CSV or Excel files you want to profile inside it.

### 2. Open the notebook

Open:

```text
Non_spatial_data_profiler.ipynb
```

in Jupyter Notebook, JupyterLab, VS Code, or another compatible environment.

### 3. Run the notebook

Run the cells sequentially.

The dataset discovery step will report the number and location of detected datasets.

Example:

```text
Found 3 datasets:

dataset/schools.csv
dataset/survey_data.xlsx
dataset/monitoring_data.xlsx
```

### 4. Review the generated reports

The profiler creates a `reports` directory automatically.

Each dataset produces a report following the naming convention:

```text
<dataset_name>_dataprofile.xlsx
```

For example:

```text
schools_dataprofile.xlsx
```

---

## Excel Report Structure

Each generated workbook contains five worksheets.

### `Dataset_Summary`

Contains high-level dataset information:

```text
Metric
Value
```

Example metrics include:

```text
file
records
fields
duplicate rows
memory usage
```

---

### `Field_Profile`

Contains field-level quality information.

Example:

```text
Field
Type
Missing
Missing %
Unique
Duplicate values
```

This sheet is useful for quickly identifying incomplete or highly repetitive fields.

---

### `Numeric_Summary`

Contains statistical summaries for numeric variables.

Example:

```text
Field
Min
Max
Mean
Median
Std
Zeros
Negative
```

---

### `Categorical_Summary`

Contains frequency distributions for text and categorical fields.

Example:

```text
Field
Value
Count
Percentage
```

---

### `Duplicate_Summary`

Contains:

```text
Duplicate rows
Duplicate rows %
```

This provides a concise overview of record duplication within the dataset.

---

## Interpreting the Results

The profiler is a **diagnostic tool**. It identifies patterns and potential issues but does not automatically determine whether a value is correct or incorrect.

### Missing Values

A high `Missing %` does not necessarily mean that a field is problematic.

Missing values may be legitimate when:

- An attribute is optional.
- A question does not apply to a respondent.
- Information was unavailable at collection.
- The source system does not capture the attribute.

Missingness should therefore be interpreted against the data dictionary and business requirements.

---

### Unique Values

The `Unique` metric indicates the number of distinct non-null values in a field.

A field with very few unique values may represent a categorical variable.

A field with a unique value for almost every record may potentially represent:

- An identifier
- A transaction ID
- A geographic identifier
- A timestamp
- A continuous measurement

---

### Duplicate Values

The `Duplicate values` metric indicates repeated values within an individual field.

Repeated values are not automatically errors.

For example:

```text
County
-------
Nakuru
Nakuru
Nakuru
Kisumu
Kisumu
```

contains duplicated county values, but this is expected because multiple records can belong to the same county.

Duplicate analysis should therefore consider the role of the field.

---

### Duplicate Rows

Duplicate rows can indicate:

- Repeated data entry
- Accidental imports
- Multiple data collection submissions
- Replicated records
- Legitimate repeated observations

Before deleting duplicates, determine whether the complete row is actually expected to be unique.

---

### Numeric Values

The numeric summary provides an initial statistical overview.

Values that deserve investigation may include:

- Unexpectedly large values
- Unexpectedly small values
- Negative values where they are not expected
- Excessive zero values
- Very large differences between minimum and maximum

These should be assessed against the expected domain of each variable.

---

### Categorical Values

Categorical frequency distributions are particularly useful for detecting inconsistent values.

For example:

```text
Kenya
kenya
KENYA
Kenia
```

may represent the same category but appear as four distinct values in the dataset.

The profiler identifies these patterns but does not automatically standardize them.

---

## Error Handling

Each dataset is processed independently.

If a dataset fails, the profiler reports:

```text
✗ Failed: filename
  Error: <error message>
```

This allows the remaining datasets to continue processing.

Potential errors may occur because of:

- Unsupported file structures
- Corrupt files
- Invalid Excel files
- Missing dependencies
- File permissions
- Incompatible file formats
- Encoding problems
- Data-loading issues

---

## Memory Usage

The profiler records the DataFrame's memory usage using:

```python
df.memory_usage(deep=True).sum()
```

This provides an indication of the amount of memory consumed by the loaded DataFrame.

It is useful when working with large tabular datasets because a dataset that appears small on disk may require substantially more memory once loaded into Pandas.

---

## Current Limitations

The current version focuses on descriptive profiling and does not automatically perform data cleaning.

It does not currently provide:

- Automated missing-value imputation
- Outlier detection
- Data type correction
- Category standardization
- Schema validation
- Business-rule validation
- Referential-integrity checks
- Cross-dataset comparisons
- Statistical hypothesis testing
- Automated data-quality scoring
- Automatic duplicate resolution
- Data transformation
- Data-cleaning output files

The profiler should therefore be considered the **assessment stage** of a broader data-quality workflow.

---

## Recommended Data Quality Workflow

A practical workflow is:

```text
Profile
   ↓
Identify Issues
   ↓
Investigate
   ↓
Define Cleaning Rules
   ↓
Clean / Transform
   ↓
Validate
   ↓
Re-profile
   ↓
Approve for Analysis
```

The profiler can be run again after cleaning to determine whether the identified issues have been reduced or resolved.

---

## Potential Future Development

The profiler can be extended into a more comprehensive tabular data-quality framework.

Potential improvements include:

### Data Quality Scoring

Introduce configurable scores for:

- Completeness
- Uniqueness
- Validity
- Consistency
- Accuracy
- Timeliness

### Automated Issue Detection

Automatically flag conditions such as:

```text
HIGH MISSINGNESS
POTENTIAL DUPLICATES
LOW CARDINALITY
HIGH CARDINALITY
UNEXPECTED NEGATIVE VALUES
UNEXPECTED ZERO VALUES
POTENTIAL CATEGORY INCONSISTENCY
```

### Data Dictionary Integration

Allow users to provide expected metadata such as:

```text
Field
Expected Type
Required?
Expected Range
Allowed Values
Unique?
Description
```

The profiler could then compare the actual dataset against the expected schema.

### Outlier Detection

Introduce statistical methods such as:

- IQR
- Z-score
- Modified Z-score
- Domain-specific thresholds

### Cross-Dataset Profiling

Compare datasets to identify:

- Schema differences
- Missing fields
- Type inconsistencies
- Category mismatches
- Duplicate records across files

### Interactive Reports

Generate HTML dashboards containing:

- Dataset summaries
- Missingness charts
- Frequency distributions
- Numeric distributions
- Duplicate analysis
- Data-quality scores
- Automated issue flags

---

## Relationship to the Geospatial Profilers

This tool forms part of a broader data-profiling workflow covering different data types:

```text
                    Data Profiling Toolkit
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
   Non-Spatial          Vector             Raster
     Profiler          Profiler           Profiler
          │                 │                 │
          ▼                 ▼                 ▼
    Tabular QA/QC      Spatial QA/QC     Pixel QA/QC
```

The Non-Spatial Data Profiler focuses on tabular datasets, while the vector and raster profilers address spatial and raster-specific characteristics.

Together, these tools provide a foundation for a broader **geospatial and analytical data quality framework**.

---

## Intended Applications

The profiler can support:

- Data cleaning
- Data quality assessment
- Monitoring and evaluation
- Research data preparation
- Survey-data inspection
- Business intelligence workflows
- ETL preparation
- Database migration
- Data integration
- Statistical analysis preparation
- Reporting workflows
- GIS attribute-table preparation

It is particularly useful as an initial assessment step before transforming data into dashboards, analytical models, GIS layers, or reporting systems.

---

## Author

**Immaculate Khaoma**

Spatial Data Analyst | GIS Developer | Cartographer

---

## License

Add the appropriate project license here if the profiler is intended for public distribution.

For internal or proprietary use, specify the applicable usage and distribution terms.