# ML Model for Detecting Gas Pipe Leakage

Machine-learning component of **IoT-Enabled Robot for Detecting Gas Pipe Leakage** — a mobile robot that autonomously navigates along gas pipelines and uses an unsupervised clustering model to flag leaks from live multi-sensor telemetry (LPG concentration, temperature, and humidity).

> 🏅 **Published Patent** — Indian Patent Office, Application No. `202421078054`, published 15 Nov 2024.
> The project was also recognized with a formal Letter of Appreciation from the college Principal.

---

## Overview

A Raspberry Pi-based inspection robot streams live readings from gas, temperature, and humidity sensors while moving through a pipe network. This repository holds the ML model that consumes that telemetry and decides whether a reading represents normal operation or a gas leak.

The model is trained with **K-Means clustering (k = 3, k-means++)** on IoT telemetry. New sensor readings are mapped to the learned clusters in real time; a reading that falls into the leak cluster — e.g. elevated LPG with a characteristic humidity/temperature range — triggers a **"Gas Leakage Detected"** alert. A **DBSCAN** variant is also included for comparison.

## How It Works

1. **Data** — IoT telemetry CSV (`iot_telemetry_data.csv`) with the three features used for clustering:
   - Humidity
   - LPG concentration
   - Temperature
2. **Choosing k** — the **elbow method** (WCSS over k = 1…10) is used to fix the optimal cluster count at 3.
3. **Training** — a K-Means model (`init='k-means++'`, `random_state=42`) separates normal readings from leak signatures.
4. **Prediction** — new humidity / LPG / temperature values are assigned to a cluster.
5. **Leak rule** — if the point falls in the leak cluster and the readings match leak thresholds (humidity 40–60, temperature 25–30 °C, LPG ≥ 0.02), the system reports **"Gas Leakage Detected"**.
6. **Visualization** — clusters are plotted in 3D (Humidity × LPG × Temperature) with centroids highlighted.

The notebook also includes a **DBSCAN** implementation (with feature standardization and a k-distance plot for choosing `epsilon`) as an alternative density-based approach.

## Tech Stack

- **Python**
- **scikit-learn** — K-Means, DBSCAN, NearestNeighbors, StandardScaler
- **NumPy**, **pandas** — data handling
- **Matplotlib** (mpl_toolkits 3D) — cluster visualization
- **Raspberry Pi + gas/temperature/humidity sensors** — IoT data source (hardware)

## Repository Contents

| File | Description |
|------|-------------|
| `IPD_Robot.ipynb` | Full notebook: K-Means and DBSCAN clustering, elbow method, prediction, and 3D visualizations |

## Getting Started

### Prerequisites

```bash
pip install numpy pandas matplotlib scikit-learn
```

### Run

1. Open `IPD_Robot.ipynb` in Jupyter or Google Colab.
2. Point the dataset path to your `iot_telemetry_data.csv` (the notebook reads it from Google Drive by default).
3. Run the cells top to bottom to:
   - find the optimal cluster count with the elbow method,
   - train the K-Means model,
   - enter humidity / LPG / temperature values to predict the cluster and check for a leak,
   - visualize the clusters in 3D.

## The Bigger Picture

This model is one part of an end-to-end system that combines **IoT**, **machine learning**, and a **companion mobile app** for early, autonomous pipeline-leak detection — the robot moves through the pipe network, streams telemetry, runs this model, and surfaces alerts to the app.