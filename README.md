# CVD Risk Prediction from Retinal Fundus Images

Predicting cardiovascular disease (CVD) risk — specifically carotid intima-media thickness (CIMT), a validated early marker of atherosclerosis — from retinal fundus photographs, using a CNN baseline, a patient-similarity Graph Attention Network (GAT), and a CNN+GAT ensemble.

> **Code availability notice**
> This repository contains the model architecture and evaluation code for illustration only. The full training pipeline — data preparation, the train/validation/test split, training configuration, random seeds, and trained model weights — is intentionally **not released** to prevent direct replication or resubmission of this work. The notebooks are not runnable end-to-end. For research collaboration inquiries, please contact the author.

## Overview

Retinal microvasculature shares developmental origin with systemic vasculature, making fundus images a viable non-invasive window into cardiovascular risk. This project:

1. Establishes a CNN (ResNet18) baseline for binary CIMT classification (normal vs. thickened)
2. Introduces a **patient similarity graph GAT**, which models each patient as a node connected to similar patients (by image-embedding and clinical similarity), letting a Graph Attention Network reason over relationships between patients rather than treating each image in isolation
3. Combines both models into a weighted ensemble for complementary signal capture

## Motivation

Existing literature on retinal-fundus CVD prediction (Poplin et al. 2018, Diaz-Pinto et al. 2022, Zhang et al. 2023) treats each fundus image independently via CNNs or transformers. Graph-based approaches for retinal CVD prediction do exist (e.g. RGAT, vessel-topology GNNs) but build graphs from **vessel anatomy**. This project instead builds a graph from **patient-to-patient similarity** (image + clinical features), a distinct graph construction paradigm not covered in prior work, to our knowledge.

## Dataset

**[China-Fundus-CIMT](https://springernature.figshare.com/articles/dataset/High-resolution_fundus_images_for_ophthalmomics_and_early_cardiovascular_disease_prediction_China_Fundus_Carotid_Intima-Media_Thickness_dataset/27907056)** (Guo, Fu, Li et al., *Scientific Data*, 2025)
- 2,903 patients, ~5,806 bilateral fundus images
- Label: CIMT thickened (≥0.9mm) vs. normal (<0.9mm)
- Metadata: age, gender, raw CIMT value

The dataset is not included in this repository (CC BY-NC-ND).

## Method Summary

- **CNN baseline:** ImageNet-pretrained ResNet18 fine-tuned for binary classification, with a patient-level data split to prevent left/right-eye leakage.
- **Patient similarity graph GAT:** image embeddings from the trained CNN are combined with clinical features to build a mutual k-nearest-neighbour patient graph; a two-layer Graph Attention Network with residual connections, batch normalisation, and edge-weighted attention classifies each patient node. The decision threshold is tuned on validation data under a minimum-sensitivity constraint, suited to clinical screening.
- **CNN + GAT ensemble:** weighted combination of CNN and GAT predicted probabilities.

Full methodological details are given in the accompanying paper.

## Results

| Model | Accuracy | AUC-ROC | Sensitivity | Specificity | F1-score |
|---|---|---|---|---|---|
| ResNet18 (CNN) | 0.7913 | 0.8430 | 0.8523 | 0.6445 | 0.8523 |
| GAT (standalone) | 0.7798–0.7959 | 0.8195–0.8509 | 0.83–0.93 | 0.50–0.83 | 0.80–0.87 |
| **CNN + GAT Ensemble (final)** | **0.8119** | **0.8430** | **0.9253** | 0.5391 | **0.8742** |

Metrics reported: Accuracy, AUC-ROC (with 95% bootstrap CI), Sensitivity, Specificity, PPV, NPV, F1-score, Youden's J Index, Balanced Accuracy.

## Interpretability

The GAT's attention weights can be inspected per patient to identify which neighbouring patients most influenced a given prediction, a step toward clinically explainable risk scoring.

## Repository Structure

```
├── china_fundus_cimt_pipeline final.ipynb   # CNN baseline: architecture, evaluation, plots
├── patient_gat_pipeline.ipynb               # Graph construction, GAT architecture, ensemble, analysis
├── ablation_*.csv                           # Ablation study results
├── figures/                                 # Figures reported in the paper
├── LICENSE
└── README.md
```

## Academic Integrity

This is original university research work. Copying, re-implementing, or submitting this code, its methods, or its results (in whole or in part) as your own coursework or research is not permitted and may constitute academic misconduct.

## Citation

If using the China-Fundus-CIMT dataset, cite:
> Guo, Fu, Li et al. "High-resolution fundus images for ophthalmomics and early cardiovascular disease prediction: China Fundus-Carotid Intima-Media Thickness dataset." *Scientific Data*, 2025.

## Author

Anusmita Baruah
B.Tech Computer Science (Data Science), Manipal University Jaipur
Mentor: Ms. Shatabdi Basu

## Copyright

Copyright (c) 2026 Anusmita Baruah. All rights reserved.
The code and methods in this repository are the author's own work. No part of this repository may be copied, modified, or redistributed without written permission from the author.

The China-Fundus-CIMT dataset is not covered by this copyright notice; it is released by its authors under CC BY-NC-ND.
