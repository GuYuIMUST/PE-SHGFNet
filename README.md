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

After adding the framework figure to the repository:

```markdown
![Framework of PE-SHGFNet](assets/framework.png)
```

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

Suggested data structure:

```text
data/
├── images/
├── ehr/
│   └── ehr_features.csv
├── splits/
│   ├── train.csv
│   ├── val.csv
│   └── test.csv
└── metadata.csv
```

## Repository Structure

```text
PE-SHGFNet/
├── assets/
│   └── framework.png
├── configs/
│   ├── image.yaml
│   ├── ehr.yaml
│   └── multimodal.yaml
├── datasets/
├── models/
│   ├── casvit_3d.py
│   ├── ptbe.py
│   ├── pe_aspp.py
│   ├── pedanet.py
│   ├── hypergraph_fusion.py
│   └── pe_shgfnet.py
├── preprocessing/
├── utils/
├── train_image.py
├── train_ehr.py
├── train_multimodal.py
├── evaluate.py
├── requirements.txt
├── LICENSE
└── README.md
```

Update this section after the final code structure is fixed.

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

### CTPA

```bash
python preprocessing/preprocess_ctpa.py \
  --input_dir <RAW_CTPA_DIR> \
  --output_dir <PROCESSED_CTPA_DIR> \
  --metadata <METADATA_FILE>
```

The reported preprocessing pipeline includes intensity normalization, extraction of 32 consecutive slices, resizing, training-time random cropping, rotation, and depth perturbation.

### EHR

```bash
python preprocessing/preprocess_ehr.py \
  --input_file <RAW_EHR_FILE> \
  --output_file <PROCESSED_EHR_FILE>
```

All EHR variables should be restricted to the predefined prediction time window. Post-diagnosis variables must be excluded to prevent information leakage.

## Training

### Image branch

```bash
python train_image.py --config configs/image.yaml
```

### EHR branch

```bash
python train_ehr.py --config configs/ehr.yaml
```

### Full multimodal model

```bash
python train_multimodal.py --config configs/multimodal.yaml
```

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
python evaluate.py \
  --config configs/multimodal.yaml \
  --checkpoint <CHECKPOINT_PATH>
```

Reported metrics include AUROC, accuracy, F1 score, sensitivity, specificity, PPV, and NPV.

## Results

| Model | AUROC | Accuracy | F1 | Sensitivity | Specificity | PPV | NPV |
|---|---:|---:|---:|---:|---:|---:|---:|
| PE-SHGFNet | 0.975 | 0.925 | 0.929 | 0.950 | 0.900 | 0.905 | 0.947 |

Update these values if the final paper, released split, or checkpoint differs from the current manuscript.

### Image branch ablation

| CasViT | Residual | PTBE | PE-ASPP | AUROC | Accuracy | F1 |
|:---:|:---:|:---:|:---:|---:|---:|---:|
| ✓ |  |  |  | 0.827 | 0.759 | 0.738 |
| ✓ | ✓ |  |  | 0.841 | 0.778 | 0.791 |
| ✓ | ✓ | ✓ |  | 0.867 | 0.815 | 0.810 |
| ✓ | ✓ |  | ✓ | 0.845 | 0.796 | 0.759 |
| ✓ | ✓ | ✓ | ✓ | 0.895 | 0.852 | 0.842 |

## Pretrained Models

| Model | Checkpoint | Status |
|---|---|---|
| Image branch | To be added | CasViT + PTBE + PE-ASPP |
| EHR branch | To be added | PEDANet |
| PE-SHGFNet | To be added | Full multimodal model |

## Reproducibility

The public release should include:

- fixed random seeds;
- exact patient-level data splits;
- configuration files;
- package versions;
- preprocessing parameters;
- checkpoint-selection criteria;
- hardware information;
- repeated-run results and confidence intervals.

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

## Contact

- **Wei Zhang:** `zhangwei@stu.imust.edu.cn`
- **Yu Gu:** `guyu2010023@imust.edu.cn`

Please use GitHub Issues for installation problems, reproducibility questions, and bug reports.
