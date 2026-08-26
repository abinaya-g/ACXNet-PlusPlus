# ACXNet++

## Attention-Augmented Hybrid Deep Learning Framework for Cross-Task Mental Workload Estimation from EEG

This repository contains the implementation and supplementary experimental results associated with the paper:

**ACXNet++: An Attention Augmented Hybrid Deep Learning Framework for Cross Task Mental Workload Estimation from EEG Neural Manifolds**

**Authors:** P. Sundaravadivel and G. Abinaya  
**Affiliation:** Saveetha Engineering College, Chennai, Tamil Nadu, India

---

## Overview

ACXNet++ extends the ACXNet framework by introducing a lightweight multi-head self-attention mechanism between the convolutional neural network (CNN) feature extractor and the XGBoost classifier.

The complete pipeline is:

**EEG preprocessing → Autoencoder → CNN → Multi-Head Self-Attention → Feature Fusion → XGBoost**

The framework is designed for cross-subject mental workload estimation without subject-specific calibration.

The self-attention module provides data-dependent weighting of CNN-derived representations before classification, allowing the model to emphasize potentially informative feature tokens.

---

## Dataset

Experiments were conducted using the publicly available **STEW (Simultaneous Task EEG Workload)** dataset.

The dataset contains 14-channel EEG recordings acquired using an Emotiv EPOC headset at 128 Hz. The study considers two conditions:

- **No-Task:** resting condition
- **SIMKAP:** multitasking condition

The released dataset contains 48 subjects. After applying the data-quality and workload-rating criteria described in the paper, 45 subjects were used for the reported experiments.

### Dataset access

The STEW dataset is **not redistributed in this repository**. Users should obtain the dataset from its original public source and comply with its applicable terms of use.

---

## Methodology

### EEG preprocessing

The preprocessing pipeline includes:

1. FIR band-pass filtering from 1–40 Hz
2. Channel-wise min-max normalization to [0, 1]
3. Segmentation into 2-second windows
4. 1-second step size (50% overlap)

Each window contains 256 samples from 14 EEG channels.

### Autoencoder

The autoencoder compresses each flattened EEG window from:

`3584 → 512 → 256 → 128`

The 128-dimensional latent representation is used by the CNN feature extractor.

### CNN backbone

The latent representation is processed using two 1-D convolutional blocks with batch normalization, ReLU activation, and max pooling.

### Multi-Head Self-Attention

ACXNet++ introduces a 4-head self-attention layer after the CNN feature extraction stage, followed by a residual connection, layer normalization, and global average pooling.

### XGBoost classifier

The final classification stage uses XGBoost on the learned CNN/attention representation, with an optional handcrafted-feature branch as described in the paper.

---

## Evaluation Protocol

To reduce subject leakage, the reported experiments use:

**5-fold Stratified Group Cross-Validation**

with subject ID as the grouping variable. Therefore, windows from the same subject are not simultaneously included in training and testing partitions.

An additional 15% of the training subjects is used for validation and early stopping.

All reported metrics are calculated across the five cross-validation folds.

---

## Main Results

The principal full-model results reported in the manuscript are:

| Condition | ACXNet++ Accuracy |
|---|---:|
| SIMKAP | **87.59%** |
| No-Task | **75.59%** |

For the CNN-only versus CNN+Attention ablation on SIMKAP:

| Configuration | Accuracy |
|---|---:|
| CNN | 80.73% |
| CNN + Attention | **91.33%** |

The CNN+Attention SIMKAP configuration achieved an AUC of approximately **0.937**.

The 91.33% result is an attention ablation result; it should not be confused with the 87.59% result of the full ACXNet++ configuration.

---

## Ablation Studies

The repository includes experimental results for the configurations evaluated in the manuscript, including:

- Handcrafted features
- Autoencoder latent features
- CNN without attention
- CNN with attention
- CNN + handcrafted features
- CNN + attention + handcrafted features (ACXNet++)

Complete fold-level experimental results are provided in:

`results/ACXNet_plusplus_full_results.json`

---

## Statistical Analysis

Paired Wilcoxon signed-rank tests were used for comparisons across the five cross-validation folds.

Because only five paired observations are available, the smallest attainable two-sided Wilcoxon p-value is **0.0625**. Therefore, statistical significance is interpreted together with confidence intervals and effect sizes, as described in the manuscript.

---

## Reproducibility

The repository provides:

- Experimental implementation
- Google Colab notebook
- Preprocessing pipeline
- Model configuration
- Subject-level cross-validation procedure
- Hyperparameter configuration
- Fold-level experimental results

Main notebook:

`ACXNet_plusplus_Colab.ipynb`

The notebook requires the publicly available STEW dataset.

---

## Software Environment

The reported experiments used:

- Python 3.12
- PyTorch 2.12
- XGBoost 3.3
- scikit-learn 1.8
- SciPy 1.17
- NumPy
- Pandas
- Matplotlib
- Seaborn
- PyWavelets

The reported experiments were executed using CPU-based computation.

Install the required Python packages using:

```bash
pip install -r requirements.txt
```

---

## Data Availability

The EEG data used in this study are publicly available through the STEW (Simultaneous Task EEG Workload) dataset repository. The source code and supplementary experimental results supporting the findings of this study are available in this repository.

The EEG dataset itself is not redistributed here.

---

## Citation

If you use this implementation or the ACXNet++ methodology in your research, please cite:

```text
Sundaravadivel, P., & Abinaya, G.
ACXNet++: An Attention Augmented Hybrid Deep Learning Framework
for Cross Task Mental Workload Estimation from EEG Neural Manifolds.
Discover Artificial Intelligence.
```

Please update the citation with the final volume, article number, DOI, and publication year after publication.

---

## License

The source code in this repository is released under the MIT License.

See `LICENSE` for details.

---

## Contact

**G. Abinaya**  
Saveetha Engineering College  
Chennai, Tamil Nadu, India

Email: abinaya.jone@gmail.com
