# Deep Learning-Based Semantic Segmentation of Intertidal Sediments at Chandipur Coast

This repository contains the implementation for our research paper:

> **"Deep Learning-Based Semantic Segmentation of Intertidal Flat Surface Sediments from Sentinel-2 Imagery at the Chandipur Coast, India"**
>
> Rhythm Shaha, Dhyey Shekhaliyaa, Pooja Shaha, Urbashi Sarkarb,c, Ratheesh Ramakrishnand, Jumee Dowaric
>
> *Procedia Computer Science, 2025* — International Conference on Machine Learning and Data Engineering (ICMLDE)

---

## Overview

Accurate mapping of surface sediment distributions in intertidal flats is crucial for environmental monitoring and sustainable coastal management. This project implements and compares two deep learning architectures — **U-Net** and **DeepLabV3+** — for pixel-wise semantic segmentation of 7 coastal land cover classes using **multi-spectral Sentinel-2** satellite imagery of the Chandipur coast, Odisha, India.

**Key Highlights:**
- Tidally-synchronized low-tide Sentinel-2 L2A imagery across 3 seasons (2017, 2018, 2020)
- 5-band input: Blue (B2), Green (B3), Red (B4), NIR (B8), SWIR1 (B11)
- Ground truth validated with in-situ sediment grain size analysis
- DeepLabV3+ outperforms U-Net across all metrics

---

## Study Area

The Chandipur coast (~21°27′N, 87°02′E), Balasore district, Odisha, India — a macrotidal, low-energy environment where the sea recedes up to **5 km** offshore during low tide, exposing an extensive and geomorphologically complex intertidal zone.

---

## Land Cover Classes

| Class ID | Label              | Description                                |
|----------|--------------------|--------------------------------------------|
| 0        | TFNS               | Tidal Flat — Near Shore (Non-Sandy)        |
| 1        | TFAS               | Tidal Flat — Away from Shore (Attached Sandy) |
| 2        | Vegetation         | Mudflat + Mangrove vegetation zones        |
| 3        | Sandy Beach        | Sandy beach areas                          |
| 4        | Agri. Patches      | Agricultural land parcels                  |
| 5        | Aqua. Ponds        | Aquaculture pond areas                     |
| 6        | Water              | Open water bodies                          |

---

## Model Architectures

### U-Net (`U-Net_7_Class_Training.ipynb`)
- Standard encoder-decoder architecture with skip connections
- Trained from scratch for **75 epochs**
- ~7.8M parameters

### DeepLabV3+ (`deeplab.ipynb`)
- **MobileNetV2** backbone (pre-trained on ImageNet) with 1×1 Conv2D adapter for 5-channel input
- **ASPP module** (Atrous Spatial Pyramid Pooling) with dilation rates 6, 12, 18 + global image pooling
- Decoder fuses upsampled ASPP output with low-level encoder features
- Trained for **30 epochs**
- ~6.5M parameters (5.9M trainable)

### Loss Function
A **combined loss** (excluding background/no-data class):
- **Focal Loss** (γ=2.0, α=0.25) — addresses class imbalance
- **Dice Loss** — maximizes spatial overlap (IoU)

---

## Results

| Metric         | U-Net   | DeepLabV3+ |
|----------------|---------|------------|
| Test Accuracy  | 95%     | **97%**    |
| Mean IoU       | 0.7927  | **0.8860** |
| Cohen's Kappa  | 0.8828  | **0.9518** |

### Per-Class F1-Scores (DeepLabV3+)

| Class           | F1-Score |
|-----------------|----------|
| TFNS            | 0.99     |
| TFAS            | 0.92     |
| Vegetation      | 0.93     |
| Sandy Beach     | 0.72     |
| Agri. Patches   | 0.97     |
| Aqua. Ponds     | 0.97     |
| Water           | 0.97     |

The superiority of DeepLabV3+ was confirmed to be **statistically significant** via McNemar's test (p < 0.001).

---

## Repository Structure

```
unet/
├── deeplab.ipynb                    # DeepLabV3+ — training, evaluation & visualization
├── U-Net_7_Class_Training.ipynb     # U-Net — training, evaluation & visualization
├── data/
│   ├── images/                      # 5-band GeoTIFF satellite image patches
│   └── masks/                       # Corresponding segmentation mask patches
└── README.md
```

---

## Training Configuration

| Parameter      | U-Net           | DeepLabV3+       |
|----------------|-----------------|------------------|
| Optimizer      | Adam            | Adam             |
| Learning Rate  | 0.0005          | 0.0005           |
| Batch Size     | 2               | 2                |
| Epochs         | 75              | 30               |
| Input Size     | 608 × 496 × 5  | 608 × 496 × 5   |
| Loss Function  | Focal + Dice    | Focal + Dice     |
| Hardware       | MacBook Air M1  | MacBook Air M1   |

---

## Requirements

```
tensorflow >= 2.19
rasterio
numpy
matplotlib
scikit-learn
```

Install dependencies:
```bash
pip install tensorflow rasterio numpy matplotlib scikit-learn
```

---

## Usage

1. Place your 5-band GeoTIFF image patches in `data/images/` and corresponding masks in `data/masks/`
2. Open and run `deeplab.ipynb` or `U-Net_7_Class_Training.ipynb` to train, evaluate, and visualize predictions

---

## Citation

If you use this work, please cite:



---

## License

This project is developed for academic research purposes. The paper is published under the [CC BY-NC-ND 4.0](http://creativecommons.org/licenses/by-nc-nd/4.0/) license.
