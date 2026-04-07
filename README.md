# Quantum IP-Change Simulation

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)

This repository contains the experimental data and data analysis code for the manuscript:  
**Experimental simulation of a Hilbert-space inner-product change**

## Overview
This codebase supports the experimental demonstration of simulating non-unitary quantum channels—specifically, the inner-product (IP) change of a qubit system. The simulation is executed by embedding the non-unitary action into a larger unitary operation. We compare the performance of this simulation using two different hardware approaches on a Rigetti superconducting processor:
1. A two-qubit hardware framework using entangling gates.
2. A hardware-native, single-qutrit framework leveraging the higher-dimensional subspace.

## Repository Structure

* `data/`
  * `experimental_fidelities_master.csv`: A unified dataset containing the $(r, \theta, \phi)$ metric parameters, the theoretical adversarial threshold, and the error-mitigated process fidelities for both the two-qubit and single-qutrit simulations.
* `src/`
  * `[insert_script_name_1.py]`: Script used to generate the $(r, \theta, \phi)$ metric operator parameter grid.
  * `[insert_script_name_2.py]`: Script used for evaluating the process tomography data and performing readout error mitigation (REM).
  * `[insert_script_name_3.py]`: Plotting scripts for generating the 3D spherical fidelity plots shown in the manuscript.

## Data Availability
The data provided in the `data/` directory perfectly reproduces the results shown in Table III (Appendix B) of the manuscript. The theoretical adversarial threshold ($\Delta_\text{th}^\eta$) used to certify a genuinely higher-dimensional process is included alongside the empirical fidelities.

## Requirements
To run the analysis and plotting scripts locally, ensure you have Python 3.x installed along with the following packages:
```bash
pip install numpy pandas scipy matplotlib
