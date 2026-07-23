# 🧬 Automated PCR Agarose Gel Instance Segmentation (CVAT & YOLOv8)

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

- **Architecture:** YOLOv8 Nano Segmentation (`yolov8n-seg.pt`)
- **Hardware Platform:** Tesla T4 GPU (Google Colab)
- **Training Setup:** 50 Epochs, Image Resolution 640x640

### 📊 Diagnostic Analysis & Error Diagnosis

Evaluating the baseline model yielded critical insights into dataset requirements for biological ML models:

1. **Background Confusion & Data Scarcity:** 
   - *Observation:* Initial precision/recall metrics (`mAP50-95 ~ 0.17`) reflected high falsing to the `background` class in the Confusion Matrix.
   - *Root Cause:* Extreme class diversity across a low-volume initial dataset (~6 primary images with 9 classes) caused severe class imbalance.
2. **Feature Overlap:**
   - High-luminance artifacts and faint ladder bands were occasionally conflated with target bands at high confidence thresholds ($>0.25$).

---

## 🚀 Iterative Solution & Roadmap

To transition this baseline model to production performance ($mAP > 0.85$):

1. **Taxonomy Consolidation:** Merging secondary background classes into a 2-tier primary hierarchy (`DNA_Feature` vs `Lane_Structure`).
2. **Confidence Calibration:** Adjusting inference confidence thresholds ($\text{conf}=0.05$ to $0.10$) to capture faint PCR product bands.
3. **Data Augmentation:** Incorporating illumination contrast shifting, Gaussian blur (simulating out-of-focus gel imagers), and vertical scaling.
4. **Active Learning Loop:** Re-exporting model predictions back into CVAT via model-assisted annotation (Nuclio) to scale the training set to 50+ samples efficiently.

---

## 📸 Visual Previews

| CVAT Polygon Annotation | Model Prediction Mask |
| :---: | :---: |
| ![CVAT Annotation](./assets/annotated_cvat_sample.png) | ![Prediction](./assets/predictions_preview.png) |
