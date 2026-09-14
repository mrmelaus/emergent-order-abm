# Emergent Order and Institutional Collapse: A Historical Agent-Based Model of Behaviour, Structural Pressure, External Shock, and Intervention

An agent-based model (ABM) of colonial Australian convict society,
combining historical convict records with drought data to study how
endogenous structural despair, external environmental shocks,
micro-level behavioural thresholds, and institutional intervention
design interact to produce societal collapse.

This repository contains the full simulation, sensitivity analysis,
and surrogate-model explanation pipeline for the paper *"Emergent
Order and Institutional Collapse: A Historical Agent-Based Model of
Behaviour, Structural Pressure, External Shock, and Intervention"*
(submitted to JASSS — the Journal of Artificial Societies and Social
Simulation).

## Requirements

```bash
pip install -r requirements.txt
```

For running the test suite, also install:

```bash
pip install -r requirements-dev.txt
```

## Project structure

```
main.py                Unified command-line entry point for every analysis mode
src/
  worker.py             Core per-universe simulation logic
  agent_brain.py         Individual agent behavioural model (ABMConvictAgent)
  config.py              Centralized model parameters and historical calibration
  data_loader.py          Loads pre-cleaned input data (see Data section below)
  plot_style.py           Shared matplotlib house style for all figures
  runners/               One module per analysis mode (see Usage below)
scripts/
  prepare_jasss_figures.py   Validates/resizes figures for JASSS submission
data/                    Input data (not included — see Data section below)
analysis/                Simulation outputs, figures, and results (not
                         committed to this repository)
```

## Usage

Every analysis mode is run through `main.py`:

```bash
# Large-scale robustness run (main 200,000-universe sweep)
python3 main.py robustness --n 200000 --batch 200

# K-value structural sensitivity comparison (K=10/30/50)
python3 main.py sensitivity --n 5000 --k-values 10,30,50

# Q13 collapse-timing diagnostic check (post-hoc, no new simulation)
python3 main.py diagnostic --input analysis/robustness_results/run.csv

# Sobol global sensitivity analysis
python3 main.py sobol --samples 2048

# Two-proportion z-tests on an existing results file
python3 main.py ztest --input analysis/robustness_results/run.csv

# SHAP surrogate-model explanation
python3 main.py shap --input analysis/robustness_results/run.csv

# Single-variable ablation study (hold one or more parameters constant)
python3 main.py ablation --fix w3=0.0 --n 50000
```

Each subcommand supports `--help` for its full argument list. Most
subcommands also support `--figures-only`, which regenerates figures
from an existing results CSV without rerunning any simulation.

## Data

This repository does not include the historical input data or the
data-cleaning pipeline used to prepare it. To run the model, you will
need your own cleaned CSVs matching the schema expected by
`src/data_loader.py`:

1. **Convict records CSV** — must contain at minimum: `ship`,
   `transported_year`, `sentence_n`, `dp_id`, and (optionally)
   `arrival_place_clean` for destination-specific historical stress
   calibration. See `src/worker.py` (`load_and_group_by_voyage`) and
   `src/config.py` (`build_year_stress_map`) for exact usage.
2. **Drought history CSV** — must contain a `Drought_Index` column
   with annual drought index values, ordered chronologically.

Both underlying source datasets are publicly accessible historical
records; this repository only assumes they have already been cleaned
into the schema above.
