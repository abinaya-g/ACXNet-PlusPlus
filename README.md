# ACXNet++

Attention-augmented hybrid deep learning (Autoencoder → CNN → multi-head self-attention → XGBoost) for cross-subject mental workload estimation from 14-channel EEG on the STEW dataset.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/abinaya-g/ACXNet-PlusPlus/blob/main/ACXNet_plusplus_Colab.ipynb)
[![Python](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**Paper:** *ACXNet++: An Attention Augmented Hybrid Deep Learning Framework for Cross Task Mental Workload Estimation from EEG Neural Manifolds*, P. Sundaravadivel and G. Abinaya, *Discover Artificial Intelligence*.

## Abstract

ACXNet++ extends ACXNet by inserting a lightweight 4-head self-attention layer between the CNN feature extractor and an XGBoost classifier, so CNN-derived feature tokens are re-weighted in a data-dependent way before classification. It is designed for cross-subject workload estimation without subject-specific calibration. It is evaluated on the public STEW dataset (No-Task vs SIMKAP multitasking) with a subject-grouped cross-validation protocol.

## Highlights

- Attention module between CNN and XGBoost (residual connection, layer norm, global average pooling).
- Subject-independent evaluation: 5-fold Stratified Group CV, with subject ID as the group, so no subject appears in both train and test.
- Ablations over handcrafted, autoencoder, CNN, CNN + attention, and fused configurations.
- Fold-level results released as JSON; Colab notebook provided.

## Method overview

```
EEG preprocessing -> Autoencoder -> CNN -> Multi-Head Self-Attention -> Feature fusion -> XGBoost
```

| Stage | Details |
|-------|---------|
| Preprocessing | FIR band-pass 1-40 Hz; channel-wise min-max to [0, 1]; 2 s windows, 1 s step (50% overlap); 256 samples × 14 channels |
| Autoencoder | Flattened window 3584 → 512 → 256 → 128 (latent) |
| CNN | Two 1-D conv blocks (BatchNorm, ReLU, max-pool) on the latent vector |
| Attention | 4-head self-attention, residual + LayerNorm, global average pooling |
| Classifier | XGBoost on the learned representation, with an optional handcrafted-feature branch |

## Data

- **Dataset:** STEW (Simultaneous Task EEG Workload), 14-channel Emotiv EPOC, 128 Hz.
- **Conditions:** No-Task (rest) and SIMKAP (multitasking).
- **Subjects:** 48 released; 45 used after the data-quality and workload-rating criteria described in the paper.
- **Access:** not redistributed here. Download it from the original public source and follow its terms of use.

## Evaluation protocol

- 5-fold Stratified Group Cross-Validation, grouped by subject ID.
- A further 15% of the training subjects is held out for validation and early stopping.
- All metrics are computed across the five folds.

## Results

Full ACXNet++ model (CNN + attention + handcrafted features):

| Condition | Accuracy |
|-----------|----------|
| SIMKAP | 87.59% |
| No-Task | 75.59% |

Attention ablation (SIMKAP):

| Configuration | Accuracy |
|---------------|----------|
| CNN | 80.73% |
| CNN + Attention | 91.33% (AUC ≈ 0.937) |

> The 91.33% figure is the CNN + Attention ablation configuration. It is **not** the full ACXNet++ result (87.59%). Please do not compare the two directly.

Other ablation configurations (handcrafted, autoencoder latent, CNN + handcrafted) and per-fold metrics are in [`ACXNet_plusplus_full_results.json`](ACXNet_plusplus_full_results.json).

**Statistics.** Paired Wilcoxon signed-rank tests were run across the five folds. With only five paired observations the smallest attainable two-sided p-value is 0.0625, so significance is interpreted together with confidence intervals and effect sizes (see the paper).

## Repository structure

```
.
├── ACXNet_plusplus_Colab.ipynb          # main notebook: preprocessing, training, evaluation
├── ACXNet_plusplus_full_results.json    # fold-level results for all configurations
├── CITATION.cff
├── requirements.txt
├── LICENSE
└── README.md
```

## Installation

```bash
git clone https://github.com/abinaya-g/ACXNet-PlusPlus.git
cd ACXNet-PlusPlus
pip install -r requirements.txt
```

## Usage

1. Obtain the STEW dataset (see Data).
2. Open `ACXNet_plusplus_Colab.ipynb` in Colab (badge above) or locally in Jupyter.
3. Point the notebook at your copy of the dataset and run all cells.

## Reproducibility

- **Software used for the reported runs:** Python 3.12, PyTorch 2.12, XGBoost 3.3, scikit-learn 1.8, SciPy 1.17, NumPy, Pandas, Matplotlib, Seaborn, PyWavelets.
- **Hardware:** CPU-only.
- **Protocol:** subject-grouped 5-fold CV; hyperparameters and model configuration are in the notebook.

## Limitations

- Single dataset (STEW), 45 subjects, two conditions; no external validation on other datasets or headsets.
- Five CV folds give limited statistical power (minimum p = 0.0625), so differences between configurations should be read with the reported confidence intervals.
- Consumer-grade 14-channel EEG at 128 Hz.
- The full model (87.59%) scores lower than the CNN + Attention ablation (91.33%) on SIMKAP; see the paper for the discussion of this.

## Citation

```bibtex
@article{sundaravadivel_acxnetpp,
  title   = {ACXNet++: An Attention Augmented Hybrid Deep Learning Framework for Cross Task Mental Workload Estimation from EEG Neural Manifolds},
  author  = {Sundaravadivel, P. and Abinaya, G.},
  journal = {Discover Artificial Intelligence}
}
```

Please update the citation with the volume, article number, DOI and year after publication.

## License

Code is released under the MIT License. See [LICENSE](LICENSE). The STEW dataset has its own terms.

## Contact

G. Abinaya, Saveetha Engineering College, Chennai, Tamil Nadu, India. For questions or collaboration, please open a GitHub issue.
