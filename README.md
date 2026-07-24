# Automated PCR Agarose Gel Instance Segmentation: AI Model Training with CVAT & YOLOv8 🧬

[![GitHub Repo](https://img.shields.io/badge/GitHub-Repository-181717?style=flat&logo=github)](https://github.com/mnkyalo/Automated_PCR_Agarose_Gel_Instance_Segmentation_AI_Model_Training_with_CVAT_YOLOv8)
[![Format](https://img.shields.io/badge/Data_Format-YOLOv8_PyTorch_Segmentation-blue)](#)
[![Domain](https://img.shields.io/badge/Domain-Molecular_Biology_%2F_Bioinformatics-green)](#)


## Overview
This repository contains high-precision polygon instance segmentation annotations, schema definitions, and model training workflows for automating **PCR Agarose Gel Band Analysis** using **CVAT** and **YOLOv8-seg**.

### Key Features & Dataset Metadata
* **Standard Specification:** Custom polygon-based instance segmentation schema exported to **YOLOv8 PyTorch TXT** format for direct model ingestion.
* **Core Classes:** `DNA_Band`, `Ladder_Marker`, `Wells`, `Gel_Artifact`.
* **Primary Objective:** Pixel-accurate boundary detection and quantification of molecular target bands to automate gel documentation and automated density analysis.

## 📌 Project Overview
Agarose gel electrophoresis analysis requires high precision to distinguish target PCR products, molecular ladders, wells, and degraded non-specific smearing. This project establishes an **end-to-end Computer Vision & Annotation Pipeline** designed to segment, classify, and evaluate molecular features in gel images using CVAT and YOLOv8-Seg.

---

## 🛠️ Key Technical Features & Annotation Strategy

- **Granular Taxonomy (Ontology):** Designed a 9-class annotation scheme in CVAT separating target bands (`DNA_Band`), ladder benchmarks (`Ladder_Band`), wells (`Well`), negative controls (`Control/No_Amp`), artifacts, and continuous degradation (`DNA_Smear`).
- **Metadata Tagging:** Integrated key attributes into polygon boundaries, including molecular weight descriptors (`size_bp: 1000, 500`) and intensity markers (`High`, `Medium`, `Low/Faint`).
- **Instance Segmentation Boundaries:** Applied tight polygon contours rather than loose bounding boxes to enable exact volume/intensity quantification.

---

### 🎨 Data Annotation & Taxonomy Setup (CVAT)

Using **CVAT (Computer Vision Annotation Tool)**, an end-to-end multi-class instance segmentation ontology was established for agarose gel electrophoresis analysis.

- **Polygon Masks:** Applied precise polygon contours to isolate distinct `DNA_Band` instances and ladder structures.
- **Bounding Boxes & Tags:** Segmented empty lanes and controls (`Control/No_Amp`).
- **Metadata Attributes:** Enriched annotations with functional attributes such as signal intensity (`Intensity: High/Low`).

<p align="center">
  <img src="./assets/cvat_annotation_1.png" alt="CVAT Annotation Workspace" width="850"/>
</p>

---

## 🔬 Model Training & Baseline Evaluation

### 🛠️ Tech Stack & Dependencies
* **Core Frameworks:** Python, PyTorch, Ultralytics YOLOv8
* **Data Annotation & Processing:** CVAT (Computer Vision Annotation Tool), OpenCV
* **Hardware & Compute:** NVIDIA Tesla T4 GPU (Google Colab), CUDA 12.8


### ⚙️ Training Setup & Configuration
* **Architecture:** YOLOv8 Nano Instance Segmentation (`yolov8n-seg.pt`)
* **Epochs:** 50
* **Image Resolution:** 640 × 640
* **Batch Size:** 16

### 📊 Diagnostic Analysis & Error Diagnosis

Evaluating the baseline model yielded critical insights into dataset requirements for biological ML models:

1. **Background Confusion & Data Scarcity:** 
   - *Observation:* Initial precision/recall metrics (`mAP50-95 ~ 0.17`) reflected high falsing to the `background` class in the Confusion Matrix.
   - *Root Cause:* Extreme class diversity across a low-volume initial dataset (~6 primary images with 9 classes) caused severe class imbalance.
2. **Feature Overlap:**
   - High-luminance artifacts and faint ladder bands were occasionally conflated with target bands at high confidence thresholds ($>0.25$).

| Confusion Matrix Evaluation | Training Loss & Convergence Curves |
| :---: | :---: |
| ![Confusion Matrix Diagnostic](./assets/confusion_matrix.png) | ![YOLO Learning Curves](./assets/YOLO_Training_Results_Graph.png) |
| *Confusion matrix highlighting background false-negatives due to initial sample constraints.* | *Bounding box, segmentation mask, and classification loss curves.* |

---

### 🔬 Model Inference & Validation Diagnostic

Validation batch samples (`val_batch0_pred.jpg`) generated during training evaluation:

<p align="center">
  <img src="./assets/val_batch0_pred.jpg" alt="Validation Predictions Preview" width="800"/>
</p>

> **Diagnostic Note: Zero-Detection Output Analysis**  
> In the validation preview above, the model returned no overlay masks ($\text{confidence} < 0.25$). Rather than a pipeline failure, this highlights key data-centric constraints inherent to baseline training on small bio-imaging datasets:
>
> 1. **High Default Confidence Threshold:** The standard inference threshold ($\text{conf} = 0.25$) filtered out low-confidence predictions typical of early-stage training.
> 2. **High Taxonomy Granularity vs. Small Sample Size:** Partitioning ~6 source images across 9 detailed classes caused severe class scarcity, leading the model to conservatively default to background classifications.
> 3. **Mitigation Strategy:** Lowering inference confidence to $\text{conf} = 0.05$ reveals initial candidate segmentations. To achieve robust detections at standard confidence levels, the taxonomy will be consolidated (e.g., merging secondary background labels) and expanded via data augmentation and active learning.


---

## 🚀 Iterative Solution & Roadmap

To transition this baseline model to production performance ($mAP > 0.85$):

1. **Taxonomy Consolidation:** Merging secondary background classes into a 2-tier primary hierarchy (`DNA_Feature` vs `Lane_Structure`).
2. **Confidence Calibration:** Adjusting inference confidence thresholds ($\text{conf}=0.05$ to $0.10$) to capture faint PCR product bands.
3. **Data Augmentation:** Incorporating illumination contrast shifting, Gaussian blur (simulating out-of-focus gel imagers), and vertical scaling.
4. **Active Learning Loop:** Re-exporting model predictions back into CVAT via model-assisted annotation (Nuclio) to scale the training set to 50+ samples efficiently.

---

