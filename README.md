# Quantum IP-Change Simulation

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)

This repository contains the experimental data and data analysis code for the manuscript:  
**Experimental simulation of a Hilbert-space inner-product change**

## Overview
This codebase supports the experimental demonstration of simulating non-unitary quantum channels—specifically, the inner-product (IP) change of a qubit system. The simulation is executed by embedding the non-unitary action into a larger unitary operation. We compare the performance of this simulation using two different hardware approaches on a Rigetti superconducting processor:
1. A two-qubit hardware framework using entangling gates.
2. A hardware-native, single-qutrit framework leveraging the higher-dimensional subspace.

## Repository Structure

```
data/
├── processed/
│   └── experimental_fidelities_master.csv
└── raw_tomography/
    ├── qubit_tomography/
    │   └── qubit_tomography_metric_01.csv  ...  qubit_tomography_metric_40.csv
    ├── qubit_choi_matrices/
    │   └── qubit_choi_metric_01.npy  ...  qubit_choi_metric_40.npy
    └── qutrit_raw_counts/
        └── qutrit_tomography_metric_01.csv  ...  qutrit_tomography_metric_40.csv

src/
├── [insert_script_name_1.py]
├── [insert_script_name_2.py]
└── [insert_script_name_3.py]
```

## Data Description

### `data/processed/experimental_fidelities_master.csv`
A unified summary dataset containing the metric parameters, the theoretical adversarial threshold, and the error-mitigated process fidelities for both the two-qubit and single-qutrit simulations. This file directly reproduces the results shown in Table III (Appendix B) of the manuscript.

| Column | Description |
|---|---|
| `index` | Row index (1–40), corresponding to metric number |
| `r` | Radial metric parameter |
| `theta_pi` | Polar angle parameter (units of π) |
| `phi_pi` | Azimuthal angle parameter (units of π) |
| `threshold_fidelity` | Theoretical adversarial threshold (Δ_th^η) for certifying a genuinely higher-dimensional process |
| `qubit_fidelity` | Error-mitigated process fidelity for the two-qubit simulation |
| `qutrit_fidelity` | Error-mitigated process fidelity for the single-qutrit simulation |
| `qubit_passed` | Whether the two-qubit fidelity exceeds the adversarial threshold |
| `qutrit_passed` | Whether the qutrit fidelity exceeds the adversarial threshold |

---

### `data/raw_tomography/qubit_tomography/`
One CSV file per metric (40 total). Each file contains the raw process tomography data for the two-qubit simulation, including the ancilla qubit (qubit 27) used to embed the non-unitary operation.

| Column | Description |
|---|---|
| `in_state` | Input state prepared on the system qubit (e.g. `X+_26`, `Z-_26`) |
| `observable` | Joint two-qubit Pauli observable measured (e.g. `(1+0j)*X26*Z27`) |
| `expectation` | Measured expectation value |
| `std_err` | Standard error of the expectation value |
| `total_counts` | Total number of shots for this measurement setting |

---

### `data/raw_tomography/qubit_choi_matrices/`
One NumPy binary file (`.npy`) per metric (40 total). Each file contains the reconstructed Choi matrix for the two-qubit simulation as a 4×4 complex128 array, obtained from the process tomography data after readout error mitigation (REM).

---

### `data/raw_tomography/qutrit_raw_counts/`
One CSV file per metric (40 total). Each file contains the raw measurement counts and readout-error-mitigated counts for the single-qutrit simulation across 18 tomography settings.

| Column | Description |
|---|---|
| `raw_0` | Raw counts in qutrit state \|0⟩ |
| `raw_1` | Raw counts in qutrit state \|1⟩ |
| `raw_2` | Raw counts in qutrit state \|2⟩ |
| `mitigated_0` | REM-corrected counts in qutrit state \|0⟩ |
| `mitigated_1` | REM-corrected counts in qutrit state \|1⟩ |
| `mitigated_2` | REM-corrected counts in qutrit state \|2⟩ |

---

## Source Code

* `[insert_script_name_1.py]`: Script used to generate the (r, θ, φ) metric operator parameter grid.
* `[insert_script_name_2.py]`: Script used for evaluating the process tomography data and performing readout error mitigation (REM).
* `[insert_script_name_3.py]`: Plotting scripts for generating the 3D spherical fidelity plots shown in the manuscript.

## Requirements
To run the analysis and plotting scripts locally, ensure you have Python 3.x installed along with the following packages:
```bash
pip install numpy pandas scipy matplotlib
```