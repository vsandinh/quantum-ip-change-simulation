# Benchmarking Non-Unitary Quantum Processes

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23372890.svg)](https://doi.org/10.5281/zenodo.23372890)

This repository contains the experimental data and data analysis code for the manuscript:  
**Experimental simulation of a Hilbert-space inner-product change**

## Overview
This codebase supports the experimental demonstration of an adversarial certification framework for trace-non-increasing quantum operations, applied to the simulation of a Hilbert-space inner-product (IP) change on a superconducting quantum processor. The framework constructs a theoretical adversary restricted to unitary operations within the computational subspace augmented by classical post-selection, and certifies whether an implementation achieves process fidelity beyond this adversarial threshold — witnessing the use of genuinely higher-dimensional resources. We compare two hardware implementations on a Rigetti Aspen-11 processor:
1. A two-qubit framework embedding the non-unitary operation via Stinespring dilation using entangling gates.
2. A hardware-native single-qutrit framework leveraging the higher-dimensional subspace of a transmon qubit.

## Repository Structure

```
data/
├── processed/
│   ├── experimental_fidelities_master.csv
│   ├── experimental_fidelities_qubit.csv
│   └── experimental_fidelities_qutrit.csv
└── raw_tomography/
    ├── qubit_tomography/
    │   └── qubit_tomography_metric_01.csv  ...  qubit_tomography_metric_40.csv
    ├── qubit_choi_matrices/
    │   └── qubit_choi_metric_01.npy  ...  qubit_choi_metric_40.npy
    └── qutrit_raw_counts/
        └── qutrit_tomography_metric_01.csv  ...  qutrit_tomography_metric_40.csv

src/
├── SimulationTwoQubit.ipynb
├── SimulationQutrit.ipynb
├── PostProcessingTwoQubit.ipynb
├── PostProcessingQutrit.ipynb
├── PlotQutritBarChart.ipynb
└── PlotSphere.ipynb
```

## Data Description

### `data/processed/experimental_fidelities_master.csv`
A unified summary dataset containing the metric parameters, the theoretical adversarial threshold, and the process fidelities of the two-qubit simulation (no readout error mitigation) and of the single-qutrit simulation (with and without readout error mitigation). This file reproduces Table II (Appendix G) of the manuscript. It is written by the last cell of `PostProcessingQutrit.ipynb`, which combines `experimental_fidelities_qubit.csv` and `experimental_fidelities_qutrit.csv`, the per-experiment outputs of the two post-processing notebooks.

| Column | Description |
|---|---|
| `index` | Row index (1–40), corresponding to metric number |
| `r` | Radial metric parameter |
| `theta_pi` | Polar angle parameter (units of π) |
| `phi_pi` | Azimuthal angle parameter (units of π) |
| `threshold_fidelity` | Theoretical adversarial threshold (Δ_th^η) for certifying a genuinely higher-dimensional process |
| `qubit_fidelity` | Process fidelity for the two-qubit simulation (no readout error mitigation) |
| `qutrit_fidelity` | Process fidelity for the single-qutrit simulation after readout error mitigation (REM) |
| `qubit_passed` | Whether the two-qubit fidelity exceeds the adversarial threshold |
| `qutrit_passed` | Whether the REM-corrected qutrit fidelity exceeds the adversarial threshold |
| `qutrit_raw_fidelity` | Process fidelity for the single-qutrit simulation without REM |
| `qutrit_raw_passed` | Whether the raw qutrit fidelity exceeds the adversarial threshold |

Fidelities and thresholds are rounded to three decimals. The pass/fail columns are evaluated before rounding.

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
One NumPy binary file (`.npy`) per metric (40 total). Each file contains the Choi matrix for the two-qubit simulation as a 4×4 complex128 array, reconstructed by linear inversion of the post-selected process tomography data. No readout error mitigation was applied. The reconstruction took the survival probability of each input state from its X-basis setting only; `PostProcessingTwoQubit.ipynb` replaces it with the average over the X-, Y- and Z-basis settings before evaluating fidelities.

---

### `data/raw_tomography/qutrit_raw_counts/`
One CSV file per metric (40 total). Each file contains the raw measurement counts and readout-error-mitigated counts for the single-qutrit simulation across 18 tomography settings (six input states X±, Y±, Z±, each measured in the X, Y and Z bases, 10,000 shots per setting). The REM-corrected counts are rounded to integers.

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

* `SimulationTwoQubit.ipynb`: Implements quantum process tomography (QPT) for the two-qubit simulation. Defines the metric operator parameter grid, constructs the two-qubit embedding circuit, runs QPT on the Rigetti Aspen-11 processor with post-selection on the ancilla qubit, and reconstructs Choi matrices via linear inversion. Saves raw tomography data to `data/raw_tomography/qubit_tomography/` and Choi matrices to `data/raw_tomography/qubit_choi_matrices/`.

* `SimulationQutrit.ipynb`: Implements QPT for the single-qutrit simulation. Defines the native qutrit gate decomposition using 0-1 and 1-2 transition pulses, runs QPT on the Rigetti Aspen-11 processor using a custom IQ readout procedure, and applies readout error mitigation (REM) via linear SVM classification and confusion matrix inversion. Saves raw and REM-corrected counts to `data/raw_tomography/qutrit_raw_counts/`.

* `PostProcessingTwoQubit.ipynb`: Post-processes the reconstructed two-qubit Choi matrices to produce process fidelities. Replaces the X-basis survival probabilities with their average over the three measurement bases, projects each Choi matrix onto the completely positive, trace-non-increasing maps (`proj_choi_to_physical(choi, False)`), and evaluates process fidelity against the ideal Choi matrix and the adversarial threshold. Saves results to `data/processed/experimental_fidelities_qubit.csv`.

* `PostProcessingQutrit.ipynb`: Post-processes the qutrit tomography data to produce process fidelities, for both the raw and the REM-corrected counts. Reconstructs Choi matrices via linear inversion, with the survival probability of each input state averaged over its three measurement bases, projects onto the completely positive, trace-non-increasing maps, and evaluates process fidelity against the ideal Choi matrix and the adversarial threshold. Saves results to `data/processed/experimental_fidelities_qutrit.csv`, then builds `experimental_fidelities_master.csv` from the two-qubit and qutrit results. Run `PostProcessingTwoQubit.ipynb` first.

* `PlotQutritBarChart.ipynb`: Generates the four-panel bar chart figure comparing raw, error-mitigated, and ideal qutrit process tomography outcome probabilities for a selected metric operator, along with their difference. Reads from `data/raw_tomography/qutrit_raw_counts/` and saves the figure to `figures/` (PDF and SVG).

* `PlotSphere.ipynb`: Generates the polar sphere plots showing which metric operators pass or fail the adversarial certification threshold, visualised as points on a sphere parameterised by $(r, \theta, \phi)$. Produces both a vertical plane (meridional slice, varying $\theta$) and a horizontal plane (equatorial slice, varying $\phi$) for either the qutrit or two-qubit experiment. Reads from `data/processed/experimental_fidelities_master.csv` and saves figures to `figures/` (PDF and PNG).

All notebooks use paths relative to the repository root, so run them from there.

## Version History

### v1.1
* **Survival probability averaged over the measurement bases.** Each input state is measured in the X, Y and Z bases, and every setting gives its own estimate of the survival probability. v1.0 used the X-basis estimate only; v1.1 averages the three. All pass/fail outcomes are unchanged (24/40 REM-corrected qutrit, 16/40 two-qubit, 14/40 raw qutrit). Individual fidelities change by up to 0.025 (qutrit) and 0.004 (two-qubit).
* `experimental_fidelities_master.csv` regenerated accordingly, with new columns for the raw qutrit results. The per-experiment tables it is built from are now included.
* `PostProcessingQutrit.ipynb`: added the missing imports of `state2matrix`, `pauli2matrix`, `vec` and `unvec`; now also evaluates the raw counts and builds the master table.
* `PlotSphere.ipynb`: the vertical-plane plot now shows both halves (φ = −π/2 and φ = +π/2); pass/fail colours are read per metric from the master table; panel titles and legend match the manuscript figures.
* `PlotQutritBarChart.ipynb`: corrected the operator order of the ideal target unitary, which affected the ideal and difference panels for the 20 horizontal-plane metrics; the raw-data panel now shows probabilities.
* The raw tomography data are unchanged.

### v1.0
* Initial release.

## Requirements

Python 3.7 or later is required. The QPT notebooks additionally require access to a Rigetti Quantum Cloud Services (QCS) account and the Aspen-11 QPU.

To run the analysis and plotting scripts locally, install the following packages:
```bash
pip install numpy pandas scipy matplotlib scikit-learn pyquil forest-benchmarking
```
