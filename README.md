# Next-Gen Battery Intelligence & Automation

This repository delves into cutting-edge advancements in battery intelligence and automation, showcasing a detailed process to highlight these innovations.

Through our work, we have achieved significant cost savings of $85K annually by automating the Python pipeline with AWS stack (Redshift, QuickSight) and data science libraries (Numpy, Pandas, Matplotlib, Scikit-learn). This automation effectively replaced a full-time Data Analyst role and reduced analysis time by an impressive 95%, bringing it down from 40 hours to just 2 hours weekly.

Additionally, we have boosted battery life prediction accuracy by 10 percentage points, increasing it from 79% to 89%. This improvement was realized through ensemble modeling techniques (XGBoost, RandomForest, SVM) and innovative feature engineering, including the use of cell-pack resistance ratios and thermodynamic calculations.

Furthermore, our work has substantially reduced testing costs, slashing $500K annually by detecting failing batteries within their first 100 test cycles. This was achieved through statistical modeling of key electrical parameters, which reduced overall testing time by 80%.

I'm happy to discuss more about this project and the fascinating techniques employed. If you have any specific questions or need further information, feel free to reach out! 😊

**Disclaimer:** This is a novel work in battery intelligence, and due to a Non-Disclosure Agreement (NDA), I cannot share all minor details. This is a redacted version of how automation generally works, and how feature engineering is done in batteries using electrical parameter measurements to enhance machine learning predictive power.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Files and Directories](#files-and-directories)
  - [Python Files](#python-files)
    - [parse_compact.py](#python-filesparse_compactpy)
    - [CycloidConnector.py](#python-filescycloidconnectorpy)
    - [adhoc_analysis.py](#python-filesadhoc_analysispy)
      - [Methods](#methods)
        - [outlier_handler](#outlier_handler)
        - [get_discharge_energy](#get_discharge_energy)
        - [ttest](#ttest)
        - [get_statistics_before_after_rpt](#get_statistics_before_after_rpt)
        - [get_energy_cycle_gain](#get_energy_cycle_gain)
        - [get_dcir](#get_dcir)
        - [plot_dcir](#plot_dcir)
        - [plot_mean_voltage](#plot_mean_voltage)
        - [plot_rover_metrics](#plot_rover_metrics)
        - [Data Filtering Methods](#data-filtering-methods)
        - [Voltage Analysis Methods](#voltage-analysis-methods)
        - [Temperature Analysis Methods](#temperature-analysis-methods)
  - [Jupyter Notebooks](#jupyter-notebooks)
    - [adhoc_worksheet.ipynb](#jupyter-notebooksadhoc_worksheetipynb)
    - [dump_data_into_psql.ipynb](#jupyter-notebooksdump_data_into_psqlipynb)
    - [error_in_current_0.5_6A.ipynb](#jupyter-notebookserror_in_current_05_6aipynb)
    - [error_measurement_estimation.ipynb](#jupyter-notebookserror_measurement_estimationipynb)
    - [pack_voltage_capacity_per_current_step.ipynb](#jupyter-notebookspack_voltage_capacity_per_current_stepipynb)

## Prerequisites
- Python 3.x
- Pandas
- NumPy
- Seaborn
- Matplotlib
- Amazon Web Service (AWS) Command Line Service (CLI)
- Redshift
- tqdm
- boto3
- bz2
- json
- pingouin

You can install all required dependencies using:
```bash
pip install -r requirements.txt
```

## Files and Directories

### Python Files

#### python files/parse_compact.py
The `parse_bz2` function reads and processes compressed JSON data from Rockpi raw log messages stored in `.bz2` files. It transforms the data into a structured DataFrame for analysis.

**Dependencies:**
- bz2
- json
- pandas

Installation:
```bash
pip install pandas
```

#### python files/CycloidConnector.py
The `VWCycloid` class interacts with AWS Redshift to retrieve and process cycloid data using boto3 and pandas.

**Dependencies:**
- boto3
- pandas
- tqdm

Installation:
```bash
pip install boto3 pandas tqdm
```

#### python files/adhoc_analysis.py
The `adhoc` class streamlines and automates daily ad-hoc analysis requests. Below are its key methods:

##### Methods

###### outlier_handler
Handles outliers in a pandas Series using rolling median replacement.
- **Parameters:**
  - `series`: Input Series
  - `window`: Window size (default: 5)
  - `sigma_multiplier`: Outlier boundary definition (default: 0.1)
- **Returns:** Series with handled outliers

###### get_discharge_energy
Retrieves discharge energy data with median and lower bound energy.
- **Parameters:**
  - `df`: Input cycloid DataFrame
  - `window`: Window size (default: 7)
  - `sigma_multiplier`: Outlier boundary (default: 0.1)
  - `disclaimer`: Print energy jump disclaimer (default: True)
  - `raw`: Return raw discharge energy (default: False)
- **Returns:** DataFrame with discharge energy data

###### ttest
Performs t-test for A/B testing.
- **Parameters:**
  - `sample1`: First sample
  - `sample2`: Second sample
  - `alternative`: Test hypothesis ("two-sided", "greater", "less")
  - `interprete`: Print interpretation (default: True)
- **Returns:** DataFrame with statistical parameters

###### get_statistics_before_after_rpt
Calculates statistics around RPT cycles.
- **Parameters:**
  - `df`: Input DataFrame
  - `rpt_cycle`: RPT cycle number (default: 201)
  - `neighbors_cycle`: Cycles to consider (default: 3)
  - `window`: Rolling window size (default: 5)
  - `sigma_multiplier`: Outlier detection (default: 0.1)
  - `avoid_close_cycle`: Cycles to avoid (default: 0)
- **Returns:** DataFrame with cycle statistics

###### get_energy_cycle_gain
Calculates energy gains around RPT cycles.
- **Parameters:**
  - `df`: Input DataFrame
  - `rpt_cycle`: Reference cycle (default: 201)
  - `neighbors_cycle`: Cycles to consider (default: 5)
  - `avoid_close_cycle`: Cycles to avoid (default: 0)
  - `window`: Window size (default: 5)
  - `sigma_multiplier`: Outlier detection (default: 0.1)
  - `show_plot`: Display plot (default: True)
- **Returns:** DataFrame with energy statistics

###### get_dcir
Retrieves DCIR data.
- **Parameters:**
  - `df`: Input cycloid DataFrame
- **Returns:** DataFrame with DCIR data

###### plot_dcir
Plots DCIR data over cycles.
- **Parameters:**
  - `df`: Input DataFrame
  - `hue`: Color grouping (default: 'instance_number')
  - `label_title`: Legend title (default: 'Instance Number')
  - `ax`: Matplotlib axes object

###### plot_mean_voltage
Plots mean voltage data.
- **Parameters:**
  - `df`: Input DataFrame
  - `hue`: Color grouping (default: 'instance_number')
  - `charging_state_name`: Substate to plot (default: 'CHARGE')
  - `label_title`: Legend title
  - `raw_data`: Plot raw data (default: True)
  - `window`: Window size (default: 5)
  - `sigma_multiplier`: Outlier detection (default: 0.2)
  - `ax`: Matplotlib axes object

###### plot_rover_metrics
Creates subplot dashboard of up to 6 metrics.
- **Parameters:**
  - `df`: Input DataFrame
  - `x`: X-axis column (default: 'elapsed_minutes')
  - `charging_state_name`: Charging state (default: 'CHARGE')
  - `hue`: Color grouping
  - `suptitle`: Overall title
  - `label_title`: Legend title
  - `legend`: Show legend (default: True)

##### Data Filtering Methods
- `read_data`: Reads and processes data files
- `filter_by_doe`: Filters by DOE values
- `filter_by_instance`: Filters by instance values
- `filter_by_cycle`: Filters by cycle numbers
- `filter_by_doe_instance`: Filters by DOE-instance combinations

##### Voltage Analysis Methods
- `get_cells_voltage_std`: Calculates voltage standard deviation
- `plot_cells_voltage_std`: Plots voltage standard deviation
- `plot_cells_voltage`: Visualizes cell voltages
- `get_polarization_voltage`: Calculates polarization voltage
- `plot_polarization_voltage`: Plots polarization voltage

##### Temperature Analysis Methods
- `plot_temperature`: Visualizes temperature data across cycling states

### Jupyter Notebooks

#### jupyter notebooks/adhoc_worksheet.ipynb
Example usage of adhoc class methods:
```python
# User input configuration
doe_number = 'MTSOWPhase2_Pack25CValidation'
instance_number = None
cycles = None
default_columns = True
override_query = None

# Fetch data
dfc = redshift.get_cycloid_data(
    doe=doe_number,
    instances=instance_number,
    cycles=cycles,
    default_columns=default_columns,
    override_query=override_query
)

# Analysis
dcir = adhoc.get_dcir(dfc)
discharge_energy = adhoc.get_discharge_energy(dfc)
```

#### jupyter notebooks/dump_data_into_psql.ipynb
Handles data dumping to Redshift database with these key components:

- **DBCredentials**: Dataclass for database credentials
- **load_credentials**: Loads credentials from JSON
- **check_database_exists**: Verifies database existence
- **create_db_and_dump_data**: Creates database and imports data

#### jupyter notebooks/error_in_current_0.5_6A.ipynb
Features the `CapacityTimeUncertainty` class for analyzing cycloid data:

- Calculates capacity and time uncertainties
- Handles data loading and parameter management
- Processes capacity bounds and uncertainties
- Implements data cleaning and outlier removal

#### jupyter notebooks/error_measurement_estimation.ipynb
Contains detailed data preprocessing for CapacityTimeUncertainty calculations.

#### jupyter notebooks/pack_voltage_capacity_per_current_step.ipynb
Explains error limit variations across different current regimes for charge time and capacity uncertainty estimation.
