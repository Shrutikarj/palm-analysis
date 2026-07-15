# PalmCV — Hand Landmark Detection & Palm Line Analysis

A complete computer-vision project that:
- Detects hand landmarks using **MediaPipe TFLite** (offline, no cloud API)
- Extracts the palm region with a **convex-hull ROI**
- Applies **CLAHE → Gaussian blur → adaptive threshold** preprocessing
- Finds palm creases via **Canny Edge Detection**
- Segments lines with **Probabilistic Hough Transform**
- Classifies and measures line geometry (length, curvature, position)

> **Disclaimer** — This is a pure computer-vision / feature-extraction demo.
> No palmistry predictions, health claims, or personality inferences are made.

---

## Project Structure

```
palm_analysis/
├── app.py                       Flask web backend (all routes)
├── hand_detection.py            Public landmark API (delegates to tflite_hand_detector)
├── tflite_hand_detector.py      TFLite palm + hand landmark inference pipeline
├── palm_extraction.py           Palm ROI crop + CLAHE/blur/threshold preprocessing
├── line_detection.py            Canny edge + Hough line detection + classification
├── feature_analysis.py          Geometric feature extraction (length/curvature/position)
├── setup.py                     One-time model download + demo image generator
├── requirements.txt             Python dependencies
├── palm_detection_lite.tflite   MediaPipe palm detector model  (auto-downloaded by setup.py)
├── hand_landmark_lite.tflite    MediaPipe hand landmark model  (auto-downloaded by setup.py)
├── templates/
│   └── index.html               Full-stack frontend (HTML/CSS/JS)
└── static/
    ├── uploads/                 Stored input images
    └── results/                 Pipeline output images
```

---

## Module Guide

| Module | What it does |
|--------|-------------|
| `tflite_hand_detector.py` | Stage 1: BlazePalm SSD → palm bounding box; Stage 2: crop → 21-landmark hand model; draws coloured skeleton |
| `hand_detection.py` | Thin re-export so app code uses a stable public API |
| `palm_extraction.py` | Convex hull of MCP joints → crop + CLAHE contrast enhancement → Gaussian blur → adaptive threshold |
| `line_detection.py` | Auto-threshold Canny with low-variance fallback; Probabilistic Hough; classifies segments by vertical zone |
| `feature_analysis.py` | Per-line: total length (px), mean angle (°), circular-std curvature (°), position fraction |
| `app.py` | Flask 3 backend; numpy-safe JSON encoder; `run_pipeline()` orchestrates all 8 stages |
| `setup.py` | Downloads models from PyPI wheel (no Google Cloud needed); generates demo image |

---

## Quick Start

```bash
# 1. Clone / unzip the project
cd palm_analysis

# 2. Create a virtual environment (Python 3.9 – 3.12)
python -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. One-time setup (downloads TFLite models + creates demo image)
python setup.py

# 5. Start the server
python app.py

# 6. Open your browser
#    http://127.0.0.1:5000
