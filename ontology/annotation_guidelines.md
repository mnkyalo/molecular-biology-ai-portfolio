# 🧬 Agarose Gel Annotation Guidelines & Taxonomy Standards

This document outlines the standard operating procedure (SOP) for annotating agarose gel electrophoresis images for computer vision instance segmentation and object detection models.

## 🏷️ Class Definitions & Geometry Rules

| Class ID | Class Name | Geometry Type | Annotation Criteria & Boundary Rules |
| :---: | :--- | :---: | :--- |
| **0** | `Well` | Polygon / Box | Outline the rectangular sample loading wells located at the top of the gel matrix. Include the visible outline of the gel pocket. |
| **1** | `Artifact` | Polygon | Outline non-biological visual noise, dust particles, air bubbles, gel tears, or camera reflection glares. |
| **2** | `DNA_Ladder` | Bounding Box | Enclose the entire vertical reference ladder lane from the top band down to the lowest visible benchmark band. |
| **3** | `Gel_Lane` | Bounding Box | Enclose the full length of an individual sample lane, starting below the loading well down to the gel dye front. |
| **4** | `DNA_Band` | Polygon | Draw a tight contour mask around distinct, illuminated target PCR product bands. The boundary must closely hug the signal edge. |
| **5** | `Negative control` | Bounding Box | Enclose the entire lane designated for the negative control reaction (e.g., master mix without template DNA). |
| **6** | `Control/No_Amp` | Bounding Box | Outline specific reaction wells or lane regions where no amplification was observed (absence of target PCR band). |
| **7** | `Ladder_Band` | Polygon | Draw tight polygon contours around individual benchmark bands within the molecular ladder (`DNA_Ladder`). |
| **8** | `DNA_Smear` | Polygon / Box | Enclose continuous, diffuse vertical streaks indicating degraded DNA, over-loaded samples, or non-specific amplification. |


## 📐 General Annotation Quality Rules

1. Polygon Tightness: For DNA_Band and Ladder_Band, avoid loose bounding boxes. Polygon vertices must tightly map the fluorescent perimeter of the DNA signal.
2. Overlapping Features: Smaller sub-features (e.g., Ladder_Band) live inside macro-structures (e.g., DNA_Ladder). Both should be annotated independently to support multi-scale evaluation.
3. Faint Bands: Any signal visible to the human eye above background noise should be annotated as a band, with its metadata attribute set to Intensity: Low.
4. Artifact Isolation: If an artifact directly touches a DNA_Band, segment the band clean up to the artifact border and annotate the artifact separately under Class 1.
