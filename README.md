# Green AI Skills Analysis

A research pipeline for extracting skills from EURES job advertisements, mapping them to ESCO labels, and analysing the relationship between technical skills, soft skills, and advertised salaries across European country samples.

The repository includes skill extraction, dataset preparation, statistical analysis, and visualisation scripts.

## Features

- Extract skills from job descriptions using an ESCO skill extractor.
- Map extracted ESCO identifiers to readable skill labels.
- Classify technical and soft skills using custom taxonomies.
- Prepare country-level analytical datasets.
- Compare advertised salaries across skill combinations.
- Run regression models and salary prediction benchmarks.
- Generate country-level reports and skill word clouds.

Technical categories include AI and machine learning, data analysis, data engineering, software development, cloud and DevOps, cybersecurity, and networks and systems.

## Repository Structure

```text
.
├── skills-extraction.py          # Extract skills from raw advertisements
├── skillsmapping.py              # Map and clean skills for selected samples
├── utils.py                      # Shared cleaning and classification helpers
├── build_analysis.py             # Build analytical datasets by country
├── analyze.py                    # Generate statistical reports and figures
├── wordcloud.py                  # Generate skills word clouds
├── full_analysis.ipynb           # Analysis notebook
├── requirements-analysis.txt     # Analysis dependencies
├── ESCO_Mapping_csv 2.csv         # ESCO identifier-to-label mapping
├── macro-economic/               # Macroeconomic preparation scripts and outputs
├── reports/                      # Country-level analysis outputs
├── results/wordclouds/            # Generated word cloud images
└── files/                        # Local input and intermediate datasets
    ├── raw/
    ├── extracted/
    ├── cleaned/
    └── analysis/
```

The `files/` directory is excluded from version control. Input datasets must be supplied locally.

## Installation

Clone the repository and create a virtual environment:

```bash
git clone https://github.com/mpousmpi/green-ai-skills-analysis.git
cd green-ai-skills-analysis

python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
python -m pip install -r requirements-analysis.txt
```

On Windows, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

The requirements file covers the analysis and visualisation scripts. Raw skill extraction additionally requires PyTorch, the `esco_skill_extractor` module, and a pandas-compatible Parquet engine. These dependencies are not included in `requirements-analysis.txt`.

Run all commands from the repository root.

## Data Preparation

Create the local data directories:

```bash
mkdir -p files/raw files/extracted files/cleaned files/analysis
```

The main analysis pipeline reads semicolon-separated files named:

```text
files/extracted/extracted_skills_<country_code>.csv
```

These files must contain the fields expected by `build_analysis.py`, including extracted skills, job information, and salary data.

The ESCO mapping file must contain `conceptUri` and `preferredLabel` columns.

Country codes used by the project include:

| Code | Country |
|------|---------|
| `be` | Belgium |
| `cs` | Czechia |
| `de` | Germany |
| `el` | Greece |
| `es` | Spain |
| `fr` | France |
| `nl` | Netherlands |
| `sv` | Sweden |

## Running the Analysis

### 1. Extract skills from raw data

Place the source Parquet files in `files/raw/`, then run:

```bash
python skills-extraction.py
```

Before running, review the country settings and salary limits in `salary_limits_dict`.

The current configuration enables Czechia (`cs`). The script selects Apple MPS, CUDA, or CPU according to availability. If GPU memory is insufficient, reduce `BATCH_SIZE`.

### 2. Build analytical datasets

If extracted CSV files are already available, start here:

```bash
python build_analysis.py
```

The script processes all matching country files in `files/extracted/` and writes:

```text
files/analysis/<country_code>_analysis.csv
```

It maps ESCO skills, constructs technical and soft skill indicators, and assigns advertisements to four skill combinations:

- Technical skills only
- Soft skills only
- Both technical and soft skills
- Neither detected

### 3. Generate reports

```bash
python analyze.py
```

Reports are written to:

```text
reports/<country_code>/
```

Outputs include:

- Salary summaries by skill combination
- Skill category summaries
- Regression coefficients and model fit statistics
- Contrasts between skill combinations
- Prediction metrics
- Data quality summaries
- Salary and skill prevalence figures

Country datasets with fewer than 30 rows are skipped. Other modelling issues are reported in the terminal and may also cause a country to be skipped.

### 4. Generate skill word clouds

Word clouds use cleaned datasets from `files/cleaned/`.

To generate a cleaned dataset with the existing mapping script:

```bash
python skillsmapping.py
```

The current script selects extracted files ending in `cs.csv`. Update its file-selection rule to process other countries.

Then run:

```bash
python wordcloud.py
```

The word cloud script reads the `matched_mapped_skills` column and saves PNG images to:

```text
results/wordclouds/<Country>.png
```

Word size reflects the frequency of skill mentions in the input data.

## Notebook

For interactive exploration:

```bash
jupyter lab full_analysis.ipynb
```

## Macroeconomic Data

The `macro-economic/` directory contains preparation scripts and existing macroeconomic outputs for European country samples covering 2019–2024.

The preparation script expects external source JSON files at configured local paths. Review those paths before attempting to regenerate the outputs.

## Interpretation and Limitations

- Results describe the available job advertisement samples.
- Advertised salaries may differ from actual earnings.
- Skill detection depends on extraction quality, ESCO mappings, and classification rules.
- A missing skill label does not establish that a job does not require that skill.
- Regression results describe associations and do not establish causal effects.
- Cross-country salary comparisons require attention to currency, purchasing power, and sample composition.
- Small or sparse samples may not support every model.

## Reproducibility

To reproduce the results, retain the same input datasets, ESCO mapping, package versions, and country settings.

The prediction analysis uses a fixed random seed of `42`.
