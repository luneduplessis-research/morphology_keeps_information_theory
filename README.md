# morphology_keeps_information_theory
Two-stage info-theoretic framework separating morphology's pre-learning contribution (representation-free) from learning's extraction (representation-shaping). Shows via tactile palpation that compliant morphology preserves I(S;T)=0.40 bits that stiff morphology destroys; capacity scaling cannot compensate. Code: Python/PyTorch. Data: 105k trials.

# What Morphology Keeps: Physical Form as an Information-Theoretic Bound on Learning

**Luné du Plessis, Ryan Nefdt, Geoff Nitschke**  
University of Cape Town  
*Submitted to Artificial Life 2026 (ALIFE 2026), MIT Press*

---

## Overview

This repository contains the code, data, and analysis scripts for the paper
*What Morphology Keeps: Physical Form as an Information-Theoretic Bound on Learning*.

We introduce a two-stage framework that separates:

- **Representation-free effects**: what morphology preserves in sensory signals 
  before any learning, quantified via mutual information I(S;T)
- **Representation-shaping effects**: what learning extracts from those signals, 
  quantified via classifier performance across capacity levels

Using simulated tactile palpation of embedded nodules, we show that compliant 
probe morphology preserves I(S;T) = 0.40 bits of task-relevant information that 
stiff morphology irreversibly destroys. A 9.5-fold increase in network capacity 
cannot recover this difference. The Data Processing Inequality guarantees this: 
information absent from the sensory signal cannot be recovered by downstream 
processing.

---

## Repository Status

> **Note (submission date: 30 March 2026):** Code is being cleaned for 
> reproducibility and will be fully documented within the next week. 
> The repository structure below reflects what will be present. 
> Contact dpllun001@myuct.ac.za if you need access to specific scripts 
> before that.

---

## Repository Structure
```
morphology_keeps_information_theory/
│
├── README.md
├── LICENSE                         # MIT License
├── requirements.txt                # Python dependencies with pinned versions
├── environment.yml                 # Conda environment (recommended)
│
├── data/
│   ├── README_data.md              # Data dictionary and generation details
│   ├── conditions_A_F/             # 105,000 simulated trials (7 conditions × 
│   │                               # 10 seeds × 1,500 trials)
│   └── condition_G/                # Downsampled stiff (resolution control)
│
├── simulation/
│   ├── tissue_model.py             # 60×60mm workspace, nodule placement
│   ├── probe_morphology.py         # Compliant (Gaussian σ=20mm) and stiff 
│   │                               # (2mm radius) contact mechanics
│   ├── palpation_protocol.py       # 8×8 raster scan, spatial jitter, 
│   │                               # sensor noise
│   └── config.py                   # All experimental parameters (Table 1)
│
├── analysis/
│   ├── mi_estimation.py            # KSG mutual information estimator
│   │                               # (k=30, Chebyshev metric, d=8,000)
│   ├── bootstrap.py                # Block bootstrap CI correction
│   │                               # (200 replicates, spatial autocorrelation)
│   ├── pca_metrics.py              # Participation ratio and PC_90
│   └── feature_construction.py    # 5-statistic per taxel, d=8,000 for MI;
│                                   # d=1,600 for networks
│
├── networks/
│   ├── architectures.py            # Small (105k), Large (450k), 
│   │                               # Very Large (994k) MLPs
│   ├── train.py                    # Adam, lr=1e-3, batch=32, 
│   │                               # early stopping patience=15
│   └── evaluate.py                 # F1, accuracy, convergence epoch
│
├── scripts/
│   ├── run_simulation.py           # Generate all 7 conditions
│   ├── run_mi_analysis.py          # Reproduce Figures 3 (MI) and 4 (PCA)
│   ├── run_training.py             # Reproduce Figure 5 (F1 performance)
│   └── run_all.sh                  # Full pipeline, requires SLURM or 
│                                   # equivalent HPC access
│
├── figures/
│   ├── figure1_contact_mechanics/  # Force propagation patterns
│   ├── figure2_protocol/           # Palpation protocol and footprint geometry
│   ├── figure3_mi/                 # I(S;T) by condition
│   ├── figure4_pca/                # PCA dimensionality metrics
│   └── figure5_f1/                 # F1 by condition and capacity
│
└── hpc/
    ├── slurm_array.sh              # SLURM job array (70 tasks: 7 × 10 seeds)
    └── seeding_protocol.md         # Deterministic seeding for bit-identical 
                                    # reproduction
```

---

## Key Results

| Condition | Morphology | Capacity | I(S;T) (bits) | F1 |
|-----------|-----------|----------|---------------|----|
| A | Compliant | 105k | 0.402 [0.375, 0.429] | 0.993 ± 0.000 |
| B | Stiff | 105k | 0.000 [−0.003, 0.003] | 0.550 ± 0.007 |
| C | Stiff | 450k | 0.000 | 0.550 ± 0.004 |
| D | Stiff | 994k | 0.000 | 0.545 ± 0.003 |
| E | Compliant | 450k | 0.403 | 0.993 ± 0.001 |
| F | Compliant | 994k | 0.405 | 0.993 ± 0.001 |
| G | Stiff 3×3 | 105k | 0.009 | 0.350 ± 0.004 |

A 9.5-fold capacity increase (B→D) yields zero performance gain.  
Compliant morphology with minimal capacity (A) outperforms stiff morphology 
at maximal capacity (D) by F1 = 0.448.

---

## Reproducing the Paper's Results

### Environment
```bash
git clone https://github.com/luneduplessis-research/morphology_keeps_information_theory
cd morphology_keeps_information_theory
conda env create -f environment.yml
conda activate morphology_info
```

### Software versions

| Component | Version |
|-----------|---------|
| Python | 3.12 |
| PyTorch | 2.1.0 (CUDA 12.1) |
| NumPy | 1.24.3 |
| Pandas | 2.0.3 |
| scikit-learn | (see requirements.txt) |

### Quick reproduction (single condition)
```bash
# Reproduce MI result for Condition A (compliant baseline)
python scripts/run_mi_analysis.py --condition A --seed 0

# Expected output: MI = 0.402 bits [95% CI: 0.394, 0.410]
```

### Full pipeline
```bash
# Requires HPC with SLURM. Approximate runtime: 4-6 hours on NVIDIA L40S.
bash scripts/run_all.sh
```

All results are deterministically reproducible via hierarchical seeding 
(see `hpc/seeding_protocol.md`).

---

## Data Availability

Complete datasets (105,000 simulated palpation trials across 7 experimental 
conditions) are available at:

**OSF:** https://osf.io/XXXXX/ (DOI assigned upon acceptance)

Data released under CC0 1.0 Universal (Public Domain Dedication).  
Code released under MIT License.

---

## Citation

If you use this code or data, please cite:
```bibtex
@inproceedings{duplessis2026morphology,
  title     = {What Morphology Keeps: Physical Form as an 
               Information-Theoretic Bound on Learning},
  author    = {du Plessis, Lun\'{e} and Nefdt, Ryan and Nitschke, Geoff},
  booktitle = {Proceedings of the Artificial Life Conference (ALIFE 2026)},
  publisher = {MIT Press},
  year      = {2026}
}
```

---

## Ethics and Reproducibility

This research used exclusively simulated data. No human participants or 
animals were involved. UCT HREC approval waiver granted for purely 
computational research.

AI-assisted coding: Claude (Anthropic, Sonnet 4.5) assisted with 
implementation of KSG estimation, bootstrap procedures, and PyTorch 
training loops. All code was verified, tested, and scientifically 
validated by the authors.

---

## Contact

Luné du Plessis — dpllun001@myuct.ac.za  
Division of Neurosurgery, Department of Surgery  
Neuroscience Institute, University of Cape Town
