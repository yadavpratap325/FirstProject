# Quark vs Gluon Jet Classification using Physics-Inspired Features

## Project Overview

This repository contains the code and analysis for the ML4SCI HEPSIM GSoC 2026 evaluation task. The project focuses on distinguishing quark jets from gluon jets using physics-motivated observables extracted from simulated jet data. The analysis includes feature engineering, Lorentz boosting to the rest frame, and classification using logistic regression.

## Key Results

- **Best Classification Performance**: AUC of **0.822** using lab-frame features
- **Most Discriminating Feature**: Jet width (largest coefficient in logistic regression)
- **Multi-Dataset Validation**: Consistent performance (AUC 0.823) across 300k jets from three files

## Methodology

### 1. Data Loading and Preprocessing
- Dataset contains 100,000 jets (100k per file for multi-file analysis)
- Each jet has up to 139 constituents with (pT, η, φ) coordinates
- Zero-padding removed by considering only constituents with pT > 0

### 2. Feature Engineering
Three physics-motivated observables were computed:

**Jet Mass**  
Invariant mass calculated from the total four-momentum of all jet constituents:
$m_J = \sqrt{E_J^2 - p_{x,J}^2 - p_{y,J}^2 - p_{z,J}^2}$

**Jet Width**  
Angular spread weighted by transverse momentum:
$w = \frac{\sum_i p_{T,i} \Delta R_i}{\sum_i p_{T,i}}$, where $\Delta R_i = \sqrt{(\eta_i - \eta_J)^2 + (\phi_i - \phi_J)^2}$

**Transverse Momentum Dispersion**  
Momentum distribution measure:
$p_T^D = \sqrt{\frac{\sum_i p_{T,i}^2}{\sum_i p_{T,i}}}$

### 3. Lorentz Boost to Rest Frame
Each jet was transformed to its center-of-mass frame using the boost vector:
$\vec{\beta} = \frac{\vec{p}_J}{E_J}$

This removes the effect of overall jet motion, allowing direct comparison of intrinsic jet structure. Features were recomputed in the rest frame for comparison.

### 4. Classification
- **Model**: Logistic Regression (scikit-learn)
- **Train/Test Split**: 80/20 with random_state=42 for reproducibility
- **Features**: Jet mass, jet width, transverse momentum dispersion
- **Evaluation**: ROC curves, AUC scores, confusion matrices

## Results

### Classification Performance
| Feature Set | AUC Score |
|-------------|-----------|
| Lab Frame | 0.822 |
| Rest Frame | 0.813 |
| Multi-File (300k jets) | 0.823 |

### Confusion Matrices (Lab Frame)
[[7855 2034] # True gluons correctly identified: 7855, misclassified: 2034

[2977 7134]] # True quarks correctly identified: 7134, misclassified: 2977


### Feature Importance
Coefficients from logistic regression (lab frame):
- Jet mass: -0.056
- Jet width: 23.54
- pT dispersion: 0.566

Jet width dominates the classification, consistent with the physical expectation that gluon jets are significantly wider due to stronger radiation patterns.

## Key Findings

1. **Jet width is the most powerful discriminant** between quark and gluon jets
2. **Gluon jets** have higher multiplicity, wider angular spread, and lower leading pT
3. **Quark jets** are more collimated with higher leading particle momentum
4. **Rest frame transformation** provides comparable performance (AUC 0.813 vs 0.822)
5. **Multi-dataset validation** confirms results are stable and generalizable

## Installation and Requirements

### Dependencies
```bash
pip install numpy matplotlib scikit-learn
```

Running the Notebook
The analysis is contained in HEPSIM_GSoC.ipynb. To run:

Clone this repository

Ensure dataset files (QG_jets.npz, QG_jets_1.npz, QG_jets_2.npz) are in the same directory

Open the notebook and run all cells

Dataset Format
Each .npz file contains:

X: Array of shape (n_jets, max_constituents, 4) with (pT, η, φ, 0) for each constituent

y: Labels (1 for quark, 0 for gluon)

Zero-padding indicates missing particles (pT = 0).

Project Structure
```bash
├── HEPSIM_GSoC.ipynb    # Main analysis notebook
├── README.md            # This file
└── [dataset files]      # QG_jets.npz, QG_jets_1.npz, QG_jets_2.npz
```

Author
Pratap Kumar Yadav

License
This project is part of the ML4SCI GSoC evaluation and is intended for educational and research purposes.
