# gridemissions: Tools for power sector emissions tracking
<img src="https://user-images.githubusercontent.com/20404131/129465144-5b086d9b-6c46-462f-a036-3f1e4cd958eb.png" width="50%" align="right">

The tools in this repository power the visualization [here](https://energy.stanford.edu/gridemissions), updated hourly. Data is made publicly available and hosted on an AWS S3 bucket.  

## References
* "Tracking emissions in the US electricity system", by Jacques A. de Chalendar, John Taggart and Sally M. Benson. Proceedings of the National Academy of Sciences Dec 2019, 116 (51) 25497-25502; DOI: 10.1073/pnas.1912950116
* "Physics-informed data reconciliation framework for real-time electricity and emissions tracking", by Jacques A. de Chalendar and Sally M. Benson. Applied Energy Dec 2021; DOI: 10.1016/j.apenergy.2021.117761 [ArXiv preprint](https://arxiv.org/abs/2103.05663).

## Demo notebooks
For a quick introduction to the module, see the notebooks in the `notebooks/demo` folder. The following notebooks can also be loaded on Colab:
* [API Demo.ipynb](https://colab.research.google.com/drive/1HYHqiA2iA-vVMuqFHrKtUUkdPLN5UJYS) shows how data can be retrieved from our API
* Consumption emissions.ipynb showcases our method to compute consumption-based emissions factors [TO COME]
* Data reconciliation.ipynb showcases our automated data reconciliation framework [TO COME]

## Data naming conventions
In the dataset that is generated from this work, we use the following conventions for naming columns (see `eia_api.py`). Replace `%s` in the following dictionaries by the balancing acronyms listed [here](https://www.eia.gov/electricity/gridmonitor/about).
* For electricity:
```python
"E": {
      "D": "EBA.%s-ALL.D.H",  # Demand
      "NG": "EBA.%s-ALL.NG.H",  # Generation
      "TI": "EBA.%s-ALL.TI.H",  # Total Interchange
      "ID": "EBA.%s-%s.ID.H",  # Interchange
  }
```
* For carbon dioxide:
```python
"CO2": {
    "D": "CO2_%s_D",  # Demand
    "NG": "CO2_%s_NG",  # Generation
    "TI": "CO2_%s_TI",  # Total Interchange
    "ID": "CO2_%s-%s_ID",  # Interchange
}
```

## Installation
Clone this repository on your machine using HTTPS:  
```
git clone https://github.com/jdechalendar/gridemissions.git
```  
or using SSH (your GitHub account needs to have been configured in this case):  
```
git clone git@github.com:jdechalendar/gridemissions.git
```  
From the `gridemissions` directory (the one that contains this README file), install this repository:
```
pip install .
```
Installing the project in this way means that you can now use statements like `import gridemissions` to make the code in this repository accessible anywhere on your system.  
To install the optional dependencies as well (needed if you would like to run the automated data cleaning workflows)
```
pip install .[all]
```
If you intend to modify the code, you may want to install with the editable flag:
```
pip install -e .[all]
```
As explained [here](https://pip.pypa.io/en/stable/cli/pip_install/#editable-installs), this installs the package in setuptools' ["Development mode"](https://setuptools.readthedocs.io/en/latest/userguide/development_mode.html) so that you don't need to re-build the project every time you edit the code.     
Open a Python interpreter and import the package to create the default configuration files for the project. When you import the package, it will check if configuration files exist. If not, a message will be printed to the screen to tell you where the configuration files are created on your system.  
Optionally, you can customize the configuration files. See the configuration section for details.

## Configuration
Two configuration files are needed for this project: one to hold environment variables like data paths and passwords (`config.json`), and one to control logging (`logging.conf`). Defaults are created for you the first time you import the package, in the folder `~/.config/gridemissions`. `~` stands for your home directory. On Linux, this is what `$HOME` evaluates to, on Windows/OSX, this is the directory that contains your Documents, Desktop and Downloads folders.

### `config.json`
Whenever you import the `gridemissions` module, the key-value pairs stored in `config.json` are loaded into a dictionary that then becomes available to you as `gridemissions.config`. Your `config.json` should contain:
* `DATA_PATH`: path to local data store, by default `~/data/gridemissions`
* `TMP_PATH`: for scratch data (e.g. when downloading data), by default `~/tmp/gridemissions`

### `logging.conf`
This file is also read when you import the `gridemissions` module and configures different loggers. We use the built-in `logging` module from the standard Python library. You can edit this file to control logging, and/or re-configure loggers programmatically after loading the gridemissions module.

### Default Values

Default values (such as configuration paths) can be set with environment variables.

```bash
export GRIDEMISSIONS_CONFIG_DIR_PATH="$HOME/.config/gridemissions_test"
```

#### Supported Environment Variables

```text
GRIDEMISSIONS_CONFIG_DIR_PATH:      the configuration directory (default: "$HOME/.config/gridemissions")
GRIDEMISSIONS_LOG_CONFIG_FILE_PATH: the file used to store logging (default: "$HOME/.config/gridemissions/logging.conf")
GRIDEMISSIONS_CONFIG_FILE_PATH:     the configuration file (default: "$HOME/.config/gridemissions/config.json")
GRIDEMISSIONS_DEFAULT_LOGGING_CONF: the default logging configuration (default can be read within ./src/gridemissions/configure.py")
GRIDEMISSIONS_DATA_DIR_PATH:        the data directory (default: "$HOME/data/gridemissions")
GRIDEMISSIONS_TMP_DIR_PATH:         the temporary data directory (default: "$HOME/tmp/gridemissions")
```

## Documentation
Documentation uses [Sphinx](https://www.sphinx-doc.org/en/master/) and was generated in the `docs` folder. Open the index.html file in a browser to view the documentation. To re-generate the documentation you shoud be able to simply do
```
cd doc
make html
```
This requires that you have `sphinx` installed.

## API Usage
A download script is provided to quickly download data:
```python
# Download data for CAISO for a year
download_emissions --variable=co2 --ba=CISO --start=20190101 --end=20200101

# Print help
download_emissions -h
```

You can also use the client library to retrieve data programmatically:
```python
from gridemissions import api

# Download data for CAISO for a year
data = api.retrieve(variable="co2", ba="CISO", start="20190101", end="20200101", field="D")

# Download electricity generation data for ERCOT and BPAT for a year:
data = api.retrieve(variable="elec", ba=["ERCOT", "BPAT"], start="20190101", end="20200101", field="NG")

import pandas as pd
from io import StringIO
print(pd.read_csv(StringIO(data)).head())
```
See the docs for more advanced usage.

## Physics-informed data cleaning
Instructions to come.
