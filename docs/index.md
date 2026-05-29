# CLM-FATES on Hydra

## Introduction

TEST

This tutorial provides a practical introduction to running CLM-FATES on the Hydra HPC cluster at VUB.

The material is based on a combination of practical sessions from the Land Climate Dynamics course (VUB) and the FATES tutorial held at Lawrence Berkeley National Laboratory in 2024.

All tutorial material is available on GitHub:

- [CLM-FATES4UGent](https://github.com/sdeherto/CLM-FATES4UGent)

Additional resources:

- [FATES Tutorial](https://github.com/NGEET/fates-tutorial/)
- [UGent HPC Introduction](https://github.com/qforestlab/UGent-hpc-introduction)

## What is FATES?

FATES (Functionally Assembled Terrestrial Ecosystem Simulator) is the vegetation demographic model used within land surface models such as ELM and CLM.

FATES represents vegetation as Plant Functional Types (PFTs), which are groups of species sharing similar ecological traits.

The landscape is represented as a mosaic of patches in different successional stages. Each patch contains cohorts of trees of different sizes and PFTs. This allows FATES to simulate competition, disturbance, recruitment, mortality, and succession.

FATES can be run in many different configurations depending on the scientific question:

- Global simulations
- Regional simulations
- Site-level simulations
- Inventory-initialised simulations
- Bare-ground spinups
- Restart simulations

In this tutorial we will first perform a standard CLM-FATES simulation and then move towards site-level simulations driven by local inventory and meteorological data.

## Tutorial Structure

1. Installation and environment setup
2. Running a default CLM-FATES simulation
3. Exploring input data and source code
4. Running and analysing model output
5. Site-level simulations
6. Inventory initialisation
7. Regional simulations
8. Modifying FATES parameter files
9. Further resources and documentation

## Getting Started

Proceed to the **Installation** section using the navigation menu on the left.