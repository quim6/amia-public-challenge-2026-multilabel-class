<div align="center">

# Chest X-Ray Abnormality Classification

### Multi-label classification with EfficientNet-B4

**Computer Vision for Biomedical Images · Freie Universität Berlin · 2026**

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white) ![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?logo=pytorch&logoColor=white) ![Computer Vision](https://img.shields.io/badge/Computer_Vision-334155) ![EfficientNet](https://img.shields.io/badge/EfficientNet--B4-2563EB) ![Albumentations](https://img.shields.io/badge/Albumentations-8B5CF6)

**Macro PR-AUC: 0.651** · **Macro ROC-AUC: 0.935** · **~+0.08 Macro PR-AUC over baseline**

</div>

## Overview

This project explores **multi-label classification of thoracic abnormalities in chest X-ray images** using deep learning. The goal is to predict the presence of **14 abnormality categories** in a radiograph, where multiple findings may be present in the same image.

Rather than focusing only on training a classifier, the project investigates how **dataset imbalance, image preprocessing, data augmentation, and evaluation choices** affect model performance.

An **ImageNet-pretrained EfficientNet-B4** model was fine-tuned on chest radiographs. A series of controlled experiments compared geometric augmentation, contrast enhancement, noise injection, and border cropping. The final augmentation strategy improved **macro PR-AUC by approximately 0.08** relative to the baseline.

> **Scope:** This is an academic image-level classification project, not a lesion-localization system or a clinically validated diagnostic tool. Although the original annotations contain bounding boxes, the trained model predicts image-level labels.

## Key Results

| Metric | Final model |
|:--|--:|
| **Macro PR-AUC** | **0.6510** |
| Macro ROC-AUC | 0.9352 |
| Macro precision | 0.5974 |
| Macro recall | 0.6566 |
| Macro F1-score | 0.5959 |
| Improvement in macro PR-AUC vs. baseline | **≈ +0.08** |

The improvement was **not uniform across classes**. Some abnormalities benefited substantially from augmentation, while rare or visually heterogeneous classes remained challenging. In particular, the project highlights why **per-class PR-AUC** is important when evaluating strongly imbalanced medical-image datasets.

## Dataset and Challenges

The dataset used in the project contains **15,000 chest X-ray images**:

- **8,573** images in the provided training set
- **6,427** images in the provided test set
- **14** abnormality categories, with multiple possible labels per image
- Radiologist annotations, including bounding boxes and potentially differing interpretations of the same image

The main challenges were:

1. **Class imbalance:** some findings are much rarer than others.
2. **Annotation disagreement:** different radiologists may label the same image differently.
3. **Acquisition variability:** radiographs differ in contrast, positioning, borders, and other artifacts.
4. **Computational constraints:** images were resized from 1024 × 1024 to **512 × 512** pixels to make training feasible.

### Target construction

The original radiologist annotations were converted into a **14-dimensional multi-label target** per image. An abnormality was marked as present if at least one radiologist annotated it. The `No Finding` target was assigned only when no other abnormality was marked as present.

## Approach

### 1. Model architecture

- **Backbone:** EfficientNet-B4
- **Initialization:** ImageNet-pretrained weights, loaded with `timm`
- **Fine-tuning:** all model parameters updated during training
- **Classification head:** linear layer mapping **1,792 features to 14 logits**
- **Output:** independent sigmoid probabilities for each abnormality

### 2. Training configuration

| Setting | Value |
|:--|:--|
| Training / validation split | 80% / 20% of provided training data |
| Split strategy | Multilabel stratification |
| Image resolution | 512 × 512 |
| Batch size | 4 |
| Loss | `BCEWithLogitsLoss` |
| Optimizer | AdamW |
| Learning rate | `1e-4` |
| Weight decay | `1e-4` |
| Model-selection criterion | Macro PR-AUC |

### 3. Experiments

The project compares several strategies rather than assuming that every image transformation improves performance:

| Experiment | Purpose | Main observation |
|:--|:--|:--|
| **Baseline** | Establish reference performance without additional preprocessing | Strong differences between classes |
| **Geometric augmentation** | Introduce small rotations and scaling variations | Improved PR-AUC for several classes |
| **Contrast preprocessing** | Test CLAHE, percentile normalization, and gamma correction | Mixed, class-dependent results |
| **Contrast preprocessing + geometric augmentation** | Combine fixed intensity preprocessing with geometric transformations | Did not outperform geometric augmentation alone |
| **Intensity transformations as augmentation** | Apply contrast transformations and Gaussian noise probabilistically during training | More promising overall improvements |
| **Border cropping** | Remove black/white image borders | No consistent performance improvement |

**Final augmentation pipeline:** geometric transformations (**rotation and scaling**), **percentile-based contrast normalization**, and **Gaussian noise**.

### 4. Evaluation and interpretability

**Macro PR-AUC** was the main model-selection metric because the classes are highly imbalanced. The evaluation also included:

- Macro and per-class **ROC-AUC / PR-AUC**
- **Precision, recall, F1-score**, and false-positive rate
- **Per-class decision thresholds**, selected on validation data to maximize F1-score
- **Grad-CAM visualizations** to investigate which image regions contributed to predictions

Grad-CAM was used as an exploratory interpretability tool; its visualizations do not establish that the model reliably localizes abnormalities.

## Visualizations

The final report includes several useful figures: class distribution, radiologist disagreement, augmentation comparisons, Grad-CAM examples, and per-class PR-AUC changes.

## Repository Structure

The following structure is based on the provided repository screenshots. Some file extensions were hidden in Windows Explorer, so only clearly identifiable paths are listed.

```text
.
├── README.md
├── FInal_Report_CVfBI.pdf       # Full project report
├── .gitignore
└── src/
    ├── figures/                # Plots and visualizations
    ├── models/                 # Model-related files
    ├── train/                  # Training-related files
    ├── test/                   # Testing-related files
    ├── amia_challenge_EDA_and_preprocessing
    ├── interpret_results
    ├── interpret_results_overall
    └── train_classificaiton_efficientnetb4...
```


## Limitations and Future Work

- **Imbalanced labels and annotation disagreement** make some categories difficult to learn and evaluate.
- **Resolution reduction** may remove details needed to identify small abnormalities.
- **Limited compute** restricted the number of architectures and hyperparameter configurations tested.
- **Experiment tracking** could be improved by systematically saving configurations, seeds, checkpoints, and metrics.
- **Bounding-box annotations** could be incorporated into future localization-aware approaches.
- Future work could compare **other CNNs or vision transformers** against EfficientNet-B4.

## Report

For experimental details, figures, and the full discussion, see **[Final Project Report](FInal_Report_CVfBI.pdf)**.

---

<div align="center">

*Academic project · Computer Vision for Biomedical Images · Freie Universität Berlin, 2026*

</div>
