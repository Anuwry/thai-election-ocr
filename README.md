# Super AI Engineer Season 6: Robust OCR Pipeline for Voting Results

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-GPU_Enabled-EE4C2C)
![OpenCV](https://img.shields.io/badge/OpenCV-Computer_Vision-5C3EE8)
![EasyOCR](https://img.shields.io/badge/EasyOCR-Deep_Learning-brightgreen)

## Executive Summary
This repository contains a highly robust, end-to-end Optical Character Recognition (OCR) pipeline engineered specifically for extracting voting results from complex, noisy, and potentially skewed document images. Developed for the **Super AI Engineer Season 6** competition, (The "Automated Masterpiece") integrates computer vision heuristics, deep learning-based text extraction, and advanced fuzzy matching logic to achieve near-perfect transcription accuracy even on heavily degraded input data

## System Architecture & Pipeline Flow

The pipeline is divided into four distinct phases, ensuring data integrity from raw pixel inputs to the final structured CSV output

### Image Preprocessing & Auto-Deskewing
Scanned or photographed documents frequently suffer from rotational deviations. The pipeline employs an automated deskewing algorithm:
1. **Adaptive Binarization:** Converts the image to grayscale and applies `cv2.adaptiveThreshold` (Gaussian) to handle uneven lighting conditions
2. **Morphological Extraction:** Uses custom kernel sizes (`w // 20`) to isolate horizontal line structures
3. **Hough Transform:** Implements `cv2.HoughLinesP` to detect lines and calculates the median angular deviation
4. **Affine Transformation:** Rotates the image via `cv2.warpAffine` to perfectly align the document matrix

> **Visualizing the Deskew Process:**
> *(Replace the path below with your actual image showing the Before/After of the deskewing process)*
> 
> <img width="1432" height="708" alt="Untitled" src="https://github.com/user-attachments/assets/810bda67-bd17-486c-b943-614b6bca2106" />
> *Figure 1: Comparison of the raw input image (left) and the geometrically corrected image (right) after Hough Transform processing*

### Table Segmentation & Adaptive Row Interpolation
Instead of relying purely on deep learning to find text, the system reconstructs the physical table structure using classic Computer Vision techniques:
* **Grid Reconstruction:** Extracts vertical and horizontal lines using multi-iteration erosion and dilation
* **Row Detection:** Identifies text blob contours and horizontal line intersections to map out the vertical coordinates (Y-centers) of each row
* **Adaptive Interpolation (Missing Row Recovery):** If the distance between two rows exceeds standard deviation thresholds (`std_h * 1.5 < gap < std_h * 5.0`), the system mathematically estimates the position of missing rows and probes for pixel noise to dynamically rebuild the table structure
* **Contrast Enhancement:** Applies CLAHE (Contrast Limited Adaptive Histogram Equalization) to each cropped row segment to maximize text clarity before feeding it to the OCR engine

### Deep Learning OCR & Hybrid Decoding
The core text extraction relies on **EasyOCR** (GPU-accelerated), but raw OCR output on Thai/Arabic numeric data is inherently flawed. We implemented a hybrid decoding mechanism:
* **Thai Text Checksum Decoder:** A custom NLP heuristic dictionary (`extract_thai_checksum`) maps misread or phonetically written Thai numbers (e.g., "หนงยมื่น" -> 10000, "ฮอง" -> 2) into valid integers
* **Arabic Digit Fallback:** Cleans noise characters (e.g., reading "o" as "0", "l" as "1") and resolves comma punctuation bugs
* **Smart Reconciliation (`clean_vote_string`):** Compares the results of the Thai Text Checksum and Arabic extraction, intelligently defaulting to the most logical integer value based on length and context

### Fuzzy Matching & Smart Imputation
Extracting data is only half the battle; mapping it to the provided template is the other:
* **Levenshtein Distance Matching:** Uses the `rapidfuzz` library to calculate the partial ratio score between the OCR-extracted party name and the template CSV
* **Contextual Boost:** Exact matches on the party number append a definitive confidence boost (`+30 score`) to prevent naming collisions
* **Algorithmic Imputation (Target Sum Correction):** The system actively searches for aggregate keywords (e.g., "รวมคะแนน"). If the sum of the extracted votes falls short of the detected target sum, and exactly one template ID remains unmapped (value = 0), the script dynamically calculates the differential (`target_sum - sum_votes`) and seamlessly imputes the missing data

---

## Environment Setup & Requirements

It is highly recommended to run this pipeline in a **CUDA-enabled GPU environment** to ensure EasyOCR operates at maximum efficiency

### Dependencies
Install all required packages via pip:

```bash
pip install -r requirements.txt
