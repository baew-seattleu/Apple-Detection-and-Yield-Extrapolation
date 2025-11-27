# Apple Detection Framework

## A Modular UAV-Based Framework for Apple Detection and Yield Extrapolation from 3D Point Clouds

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **Paper:** Submitted to ACM SAC 2026 (41st ACM/SIGAPP Symposium on Applied Computing)

This repository contains the complete implementation of our modular framework for automated apple detection, counting, and yield estimation from UAV-derived 3D point clouds.

### Key Results
- **Regression R² ≈ 0.99** for apple count estimation from red-point distributions
- **Clustering R² ≈ 0.75** (M1: HSV + DBSCAN + MBB) for spatial localization
- Successfully processed **260M+ points** across 50 orchard sections
- Achieves **relative error ≤ 3%** with neural network regression

---

## Dependencies

The project relies on standard scientific computing and point cloud processing libraries. All required packages are listed in `requirements.txt`.

To install all dependencies at once, run:

```bash
pip install -r requirements.txt
```

## Quick Start

1. **Open the notebook**: `AppleDetection.ipynb`

2. **Configure paths** (Cell 1):
```python
METHOD = "M1"  # Options: "M1", "M2", "M3", or "all"
INPUT_DIR = "./data/las_files"
GT_CSV = "./data/ground_truth.csv"
```

3. **Run all cells** to execute the pipeline

---

## Framework Methods

| Method | Color Filter | Clustering | Object Approximation | Paper Reference |
|--------|-------------|------------|---------------------|-----------------|
| **M1** | HSV | DBSCAN | MBB (Minimum Bounding Box) | Table 5, Fig. 2 |
| **M2** | HSV | DBSCAN | Sphere | Table 5 |
| **M3** | ExR-LAB | DBSCAN | Sphere | Table 5 |

### Method Selection

- **M1**: Best for overall count accuracy (R² ≈ 0.75)
- **M2**: Sphere-based validation with HSV filtering (R² ≈ 0.66)
- **M3**: More selective filtering with ExR-LAB (R² ≈ 0.58)
- **all**: Run all methods and generate comparison

---

## Directory Structure

### Input Requirements

```
data/
├── las_files/           # LAS point cloud files
│   ├── sample1.las
│   ├── sample2.las
│   └── ...
└── ground_truth.csv     # Ground truth counts
```

#### Ground Truth CSV Format

Option 1:
```csv
filename,true_count
sample1,15
sample2,22
```

Option 2:
```csv
sample,count
sample1,15
sample2,22
```

### Output Structure

```
Results/
├── M1/
│   ├── intermediate/        # Intermediate files
│   ├── visualizations/      # Storyboard plots
│   ├── metrics/
│   │   ├── M1_results.csv   # Per-file results
│   │   └── M1_summary.csv   # Summary metrics
│   └── models/              # Saved models
├── M2/
│   └── (same structure)
├── M3/
│   └── (same structure)
└── Combined/
    ├── method_comparison.csv
    ├── method_comparison.png
    └── predicted_vs_actual.png
```

---

## Parameters (Table 1 in Paper)

### HSV Filtering
- Hue: H < 0.10 or H > 0.96
- Saturation: 0.20 ≤ S ≤ 0.80
- Value: 0.50 ≤ V ≤ 1.00

### ExR-LAB Filtering
- Excess Red threshold: 0.20
- LAB a* channel: 10 ≤ a* ≤ 60

### DBSCAN Clustering
- Neighbor radius (ε): 0.028 m
- MIN_SAMPLES: 136

### MBB Approximation
- Space diagonal (D): 4-20 cm
- Cube ratio min: ≥ 0.50

### Sphere Approximation
- Radius (R): 3-10 cm

---

## Pretrained Models

### Using Pretrained Models

1. Place model files in `./models/`:
   - `regression_dt.pkl` (Decision Tree)
   - `regression_rf.pkl` (Random Forest)
   - `regression_gb.pkl` (Gradient Boosting)
   - `regression_knn.pkl` (K-Nearest Neighbors)

2. Set in configuration:
```python
TRAIN_FROM_SCRATCH = False
PRETRAINED_MODEL_DIR = "./models"
```

### Training New Models

Set `TRAIN_FROM_SCRATCH = True` to retrain models from your data.

---

## Results Mapping to Paper

| Paper Section | Notebook Output |
|---------------|-----------------|
| Table 4 (Regression) | `Results/*/metrics/*_summary.csv` |
| Table 5 (Clustering) | `Results/Combined/method_comparison.csv` |
| Figure 4 (RE Dist.) | `Results/Combined/predicted_vs_actual.png` |
| Table 6 (Yield) | Volume calculations in results CSV |

---

## Dependencies

```
numpy>=1.20.0
pandas>=1.3.0
laspy>=2.0.0
scikit-learn>=1.0.0
matplotlib>=3.4.0
scikit-image>=0.18.0
pyproj>=3.0.0
```

Install all dependencies:
```bash
pip install numpy pandas laspy scikit-learn matplotlib scikit-image pyproj
```

---

## Citation

If you use this code in your research, please cite our paper:

```bibtex
@inproceedings{author2026apple,
  title={A Modular UAV-Based Framework for Apple Detection and Yield Extrapolation from 3D Point Clouds},
  author={[Authors]},
  booktitle={Proceedings of the 41st ACM/SIGAPP Symposium on Applied Computing (SAC)},
  year={2026},
  publisher={ACM},
  address={Thessaloniki, Greece},
  doi={10.1145/XXXXXXX.XXXXXXX}
}
```

---

## Acknowledgments

This work was conducted in collaboration with a commercial fruit producer. We thank them for providing access to orchard data.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Troubleshooting

### Common Issues

1. **No LAS files found**: Check `INPUT_DIR` path
2. **Ground truth not loaded**: Verify CSV column names
3. **Memory errors**: Process files in batches for large datasets
4. **Coordinate issues**: Set `FORCE_LONLAT = True` or `False` explicitly

### Contact

For questions about the implementation, please open an issue.
