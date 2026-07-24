# How to Run YOLO Object Detection Models on the Raspberry Pi

A complete step-by-step guide on setting up, optimizing, and running Ultralytics **YOLO 11** object detection models on the **Raspberry Pi 4 / 5** using **NCNN** export for fast edge performance.

Based on the repository by **Soumya Das**.

---

## 📋 Prerequisites & Hardware Requirements

- **Device**: Raspberry Pi 4 or Raspberry Pi 5 (64-bit OS recommended)
- **Camera**: USB Webcam or Raspberry Pi Camera module
- **Network**: Active internet connection during setup
- **OS**: 64-bit Raspberry Pi OS

---

## 🛠️ Step-by-Step Setup Guide

### Step 1: System Update & Directory Setup

1. Open a terminal on your Raspberry Pi and ensure system packages are up to date:

```bash
# Update repository package lists and upgrade existing software
sudo apt update && sudo apt upgrade -y
```

Create a dedicated project directory named YOLO and navigate into it:

```bash
# Create and move into project folder
mkdir YOLO && cd YOLO
```

### Step 2: Set Up Virtual Environment & Dependencies

Using a virtual environment prevents package conflicts with the host system Python libraries.

Create a Python virtual environment named env:

```bash
# Create virtual environment with system site packages enabled
python3 -m venv --system-site-packages env
```

Activate the virtual environment:

```bash
# Activate virtual environment
source env/bin/activate
```

*(Note: Whenever you open a new terminal window, reactivate the environment using `cd YOLO` and `source env/bin/activate`.)*

Install Ultralytics and NCNN libraries:

```bash
# Install required Python libraries
pip install ultralytics ncnn
```

### Step 3: Hardware Verification (Camera Setup)

**USB Webcam**: Plug in your camera and run:

```bash
ls /dev/video*
```

Confirm `/dev/video0` or `/dev/video1` is listed.

**Raspberry Pi Camera**: Ensure the Pi is powered off before connecting the ribbon cable.

---

## ⚡ Converting YOLO Models to NCNN Format

Ultralytics `.pt` models are optimized for PyTorch. Converting them to NCNN format optimizes execution for ARM CPUs on Raspberry Pi devices.

### 1. Download Pre-trained YOLO 11 Nano Model

```bash
# Download off-the-shelf YOLO11 nano model weights
yolo detect predict model=yolo11n.pt
```

### 2. Export Model to NCNN Engine Format

```bash
# Convert PyTorch (.pt) weights to NCNN format for ARM performance
yolo export model=yolo11n.pt format=ncnn
```

*Output: Generates a directory named `yolo11n_ncnn_model` containing the NCNN weights.*

---

## 📹 Running Real-Time Object Detection

### 1. Download Detection Script

Download the custom Python detection script provided by **Soumya Das**:

```bash
# Download custom detection script
wget https://raw.githubusercontent.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano/refs/heads/main/yolo_detect.py
```

### 2. Run Detection on Live Camera Feed

```bash
# Run real-time detection on USB camera
python3 yolo_detect.py --model yolo11n_ncnn_model --source USB0 --resolution 1280x720
```

*Press `Q` while focusing on the stream window to exit.*

### 3. Run Detection on Video or Image Folder (Custom Models)

If using a custom-trained model (e.g., `candy.pt` converted to `candy_ncnn_model`):

```bash
# Export custom model to NCNN
yolo export model=candy.pt format=ncnn

# Run detection on image folder
python3 yolo_detect.py --model candy_ncnn_model --source candy_pick_folder
```

### 4. Display Script Options

```bash
python3 yolo_detect.py --help
```

---

## 🚀 Key Performance Insights & Application Examples

* **Performance**: YOLO 11 Nano runs at 6–7 FPS on a Raspberry Pi 5 using NCNN format.

* **Example Edge Application — Smart Reading Lamp**:
  * Uses Raspberry Pi GPIO to interface with an AC power relay.
  * Monitors sofa/chair area with camera feed.
  * Automatically triggers relay HIGH to turn on lamp when a person is detected on the couch.

---

## 📌 Summary Command Reference

```bash
# System Update
sudo apt update && sudo apt upgrade -y

# Setup Directory & Virtual Environment
mkdir YOLO && cd YOLO
python3 -m venv --system-site-packages env
source env/bin/activate

# Install Dependencies
pip install ultralytics ncnn

# Model Export & Run
yolo detect predict model=yolo11n.pt
yolo export model=yolo11n.pt format=ncnn
wget https://raw.githubusercontent.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano/refs/heads/main/yolo_detect.py
python3 yolo_detect.py --model yolo11n_ncnn_model --source USB0 --resolution 1280x720
```
