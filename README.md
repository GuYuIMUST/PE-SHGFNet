# PE-SHGFNet
Official implementation of PE-SHGFNet, a specificity-enhanced hypergraph fusion network for multimodal pulmonary embolism diagnosis using 3D CTPA and structured EHR data.
**PE-SHGFNet: A Specificity-Enhanced Hypergraph Fusion Network for Multimodal Pulmonary Embolism Diagnosis**

## Overview

PE-SHGFNet contains three main components:

1. **PE-specific image encoder**
   - 3D CasViT backbone
   - PE-Specific Three-Branch Enhancement module (**PTBE**)
   - PE-specific Atrous Spatial Pyramid Pooling module (**PE-ASPP**)

2. **Structured EHR encoder**
   - Pulmonary Embolism Deep Abstract Network (**PEDANet**)
   - Global interaction modeling, sparse feature grouping, sample-specific gating, and hierarchical abstraction

3. **Higher-order multimodal fusion**
   - **PE-HypergraphFusion**
   - Learnable hyperedges for many-to-many interactions between imaging and EHR features

## Framework

```text
3D CTPA ──► CasViT + PTBE + PE-ASPP ──┐
                                       ├─► PE-HypergraphFusion ─► Classifier
EHR data ─────────► PEDANet ──────────┘
```
<img width="927" height="336" alt="overall framework" src="https://github.com/user-attachments/assets/d8cb74cb-28a4-49ce-b4ec-f0eafc225bac" />

## Main Contributions

- PE-specific modeling of vascular orientation, density variation, and shape-texture patterns.
- Structured abstraction of heterogeneous EHR variables through sparse grouping and sample-specific gating.
- Hypergraph-based modeling of higher-order cross-modal relationships.
- Evaluation on the public RadFusion dataset with backbone comparisons, ablation studies, multimodal experiments, and visualization.

## Dataset

Experiments are conducted on the public **RadFusion** dataset.

| Item | Number |
|---|---:|
| Total CTPA studies | 1,837 |
| PE-positive cases | 726 |
| PE-negative cases | 1,111 |
| Central PE | 257 |
| Segmental PE | 387 |
| Subsegmental PE | 82 |
| Data split | 80% / 10% / 10% |

The dataset is not redistributed in this repository. Please obtain it through the official RadFusion release and comply with its license and data-use requirements.

## Installation

```bash
git clone https://github.com/<YOUR_USERNAME>/PE-SHGFNet.git
cd PE-SHGFNet

conda create -n pe-shgfnet python=3.10 -y
conda activate pe-shgfnet

pip install -r requirements.txt
```

Recommended dependencies include PyTorch, NumPy, pandas, scikit-learn, SciPy, SimpleITK, NiBabel, pydicom, PyYAML, matplotlib, and tqdm. Pin the exact versions used for the final experiments.

## Preprocessing
This work does **not** introduce a new data-preprocessing pipeline. CTPA and EHR data are prepared using established procedures from PENet and RadFusion, respectively.

### CTPA Preparation

CTPA preprocessing follows the pipeline used in **PENet**. The PENet-based procedure is used to prepare the volumetric CTPA inputs for the image branch. No additional preprocessing method is claimed as a contribution of PE-SHGFNet.

Users should follow the corresponding PENet preprocessing procedure and then provide the processed CTPA paths in the project configuration files. Any repository-specific data-loading instructions will be documented in `data/README.md`.

### EHR Preparation

Structured EHR data are prepared according to the official **RadFusion** processing and feature organization. PE-SHGFNet does not introduce additional handcrafted feature engineering or a separate EHR preprocessing pipeline.

Users should obtain the RadFusion EHR data and official split information through the original release, then configure the corresponding file paths in the project configuration files.

To ensure a fair evaluation, the patient-level train, validation, and test splits should remain consistent with the RadFusion protocol.

## Training

### Image branch

The image branch, EHR branch, and multimodal fusion model are trained through a unified training script:

```bash
python train.py
```

Before training, update the dataset paths, output directory, and other experiment settings in `train.py` according to the released code.

Main settings reported in the manuscript:

| Setting | Value |
|---|---|
| Optimizer | SGD |
| Initial learning rate | `1e-4` |
| Batch size | `8` |
| Epochs | `100` |
| Scheduler | Cosine annealing |
| Warm-up | Linear warm-up |
| Image input | `1 × 32 × 192 × 192` |

## Evaluation

```bash
python evaluate.py
```

Reported metrics include AUROC, accuracy, F1 score, sensitivity, specificity, PPV, and NPV.

## Results

| Model | AUROC | Accuracy | F1 | Sensitivity | Specificity | PPV | NPV |
|---|---:|---:|---:|---:|---:|---:|---:|
| PE-SHGFNet | 0.975 | 0.925 | 0.929 | 0.950 | 0.900 | 0.905 | 0.947 |

### Image branch ablation

| CasViT | Residual | PTBE | PE-ASPP | AUROC | Accuracy | F1 |
|:---:|:---:|:---:|:---:|---:|---:|---:|
| ✓ |  |  |  | 0.827 | 0.759 | 0.738 |
| ✓ | ✓ |  |  | 0.841 | 0.778 | 0.791 |
| ✓ | ✓ | ✓ |  | 0.867 | 0.815 | 0.810 |
| ✓ | ✓ |  | ✓ | 0.845 | 0.796 | 0.759 |
| ✓ | ✓ | ✓ | ✓ | 0.895 | 0.852 | 0.842 |

## Citation

```bibtex
@article{zhang2026peshgfnet,
  title   = {Multimodal Computer-Aided Diagnosis of Pulmonary Embolism via a Specificity-Enhanced Vision Transformer and Higher-Order Hypergraph Fusion},
  author  = {Zhang, Wei and Gu, Yu and Fu, Xuke and Yang, Lidong and Zhang, Baohua and Li, Jianjun and Liu, Xin and Hao, Juan and Tang, Siyuan and Chen, Meng and Ma, Hao and He, Qun},
  journal = {To be updated},
  year    = {2026},
  doi     = {To be updated}
}
```

Before publication, add:

> Manuscript under review. Citation information will be updated after publication.

## Related Work

PE-SHGFNet extends our previous multimodal PE framework:

```bibtex
@article{zhang2025htctnet,
  title   = {A novel multimodal computer-aided diagnostic model for pulmonary embolism based on hybrid transformer-CNN and tabular transformer},
  author  = {Zhang, Wei and Gu, Yu and others},
  journal = {Physical and Engineering Sciences in Medicine},
  year    = {2025},
  volume  = {48},
  number  = {3},
  pages   = {1107--1126},
  doi     = {10.1007/s13246-025-01568-4}
}
```

## License

Select a license before release. Common choices are:

- MIT License
- Apache License 2.0
- GNU GPLv3

Confirm that the selected license is compatible with all third-party code, pretrained backbones, and dataset terms.

## Clinical Disclaimer

This repository is intended for academic research only. PE-SHGFNet is not a certified medical device and must not be used for clinical diagnosis, treatment decisions, or patient management without independent validation and regulatory approval.

## Acknowledgements

We thank the creators of the RadFusion dataset and the developers of the open-source libraries used in this work.
