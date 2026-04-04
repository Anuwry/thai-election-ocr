# Election Results OCR Pipeline

This repository contains an Optical Character Recognition (OCR) pipeline designed to automatically extract vote counts and political party affiliations from scanned election result documents.

*(Note: Personally Identifiable Information (PII), including candidate names and committee signatures, has been redacted from all sample images to ensure compliance with data privacy regulations and the Personal Data Protection Act)*

## Project Objectives
* **Automated Data Extraction:** Extract vote counts and party names from complex scanned documents containing tabular structures and Thai typography
* **Image Preprocessing:** Implement advanced image preprocessing techniques, notably skew correction, to rectify document alignment and enhance OCR accuracy
* **Data Formatting:** Process and structure the extracted raw text into a standardized format (e.g., Pandas DataFrame or CSV) suitable for downstream data analysis

## Tools and Technologies
* **Computer Vision and Preprocessing:** OpenCV, NumPy (Deskewing, Binarization, Line Detection)
* **OCR Engine:** EasyOCR
* **Data Manipulation:** Pandas, Regular Expressions (Regex)
* **Development Environment:** Python, Jupyter Notebook

## Methodology
1. **Image Preprocessing:**
   * Load the document image and apply grayscale conversion
   * Utilize line detection algorithms (e.g., Hough Line Transform) to calculate the document's skew angle and perform rotational alignment (Deskewing)
   * <img width="1432" height="708" alt="Untitled" src="https://github.com/user-attachments/assets/86af0199-602e-46d5-b319-77bd5a5f7733" />

2. **Text Extraction (OCR):**
   * Isolate Regions of Interest (ROI) specifically targeting the table columns containing vote counts and political party names
   * Deploy the OCR engine to extract Thai characters and numerical data from the designated regions
3. **Data Post-processing:**
   * Apply Regular Expressions (Regex) and rule-based logic to sanitize the extracted text, mitigating common OCR misclassifications (e.g., distinguishing between Thai numerals and English characters)
   * Format the sanitized data into a structured Pandas DataFrame for further utilization

## Installation and Usage
1. Clone the repository: 
   ```bash
   git clone [[https://github.com/Anuwry/thai-election-ocr.git](https://github.com/Anuwry/thai-election-ocr.git)](https://github.com/Anuwry/thai-election-ocr.git)
