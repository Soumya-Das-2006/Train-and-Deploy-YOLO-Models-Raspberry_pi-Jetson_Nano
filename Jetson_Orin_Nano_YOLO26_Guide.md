# How to Run YOLO 26 Detection Models on NVIDIA Jetson Orin Nano

A complete step-by-step guide on setting up, optimizing, and running Ultralytics **YOLO 26** object detection models on the **NVIDIA Jetson Orin Nano** for real-time edge vision applications.

Based on the repository by **Soumya Das**.

---

## 📋 Prerequisites & Hardware Requirements

- **Device**: NVIDIA Jetson Orin Nano (Developer Kit or Super Developer Kit)
- **Storage**: MicroSD card (64 GB+ recommended)
- **Peripherals**: USB Camera, Keyboard, Mouse, Display, and Power Supply
- **Network**: Internet connection during setup

---

## 🛠️ Step-by-Step Setup Guide

### Step 1: Flash JetPack 6.1 OS Image

Ultralytics explicitly supports **JetPack 6.1**. Ensure you flash this version for maximum stability and compatibility.

1. Download the JetPack 6.1 SD Card Image for Jetson Orin Nano.
2. Use **BalenaEtcher** or **Raspberry Pi Imager** to flash the downloaded `.img` file onto your MicroSD card.
3. Insert the MicroSD card into your Jetson Orin Nano and power on the device.
4. Complete the initial Ubuntu desktop OS setup wizard (keep default settings) and connect to Wi-Fi/Ethernet.

---

### Step 2: System Performance & Package Updates

#### Set Maximum Power Mode (MAX N)
1. Click the power mode icon in the top-right corner of the desktop.
2. Change the mode to **MAX N** (Super Performance Mode) to ensure full computational output for TensorRT inference.

#### System Updates & Pip Installation
Open a terminal (`Ctrl+Alt+T`) and run the following commands:

```bash
# Update software package repositories and upgrade existing packages
sudo apt update && sudo apt upgrade -y

# Install Python 3 pip package manager
sudo apt install python3-pip -y

# Upgrade pip to the latest version
pip install --upgrade pip

```

---

### Step 3: Clone Repository & Run Automated Setup Script

Clone the deployment repository and execute the setup script to automatically install PyTorch, Ultralytics YOLO, and dependencies optimized for Jetson.

```bash
# Clone the repository into a local 'yolo' folder
git clone https://github.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano.git yolo

# Navigate into the project folder
cd yolo

# Run the automated Jetson installation script
bash utils/jetson_install_yolo.sh

```

*Note: The script downloads ~1 GB of required libraries and packages. Once complete, perform a system reboot:*

```bash
# Reboot the system to finalize installations
sudo reboot

```

---

## ⚡ Converting YOLO 26 Models to TensorRT (FP16 Engine)

To achieve maximum FPS on Jetson hardware, convert the standard PyTorch (`.pt`) model into NVIDIA's optimized **TensorRT (`.engine`)** format with **FP16 (Half-Precision)** enabled.

### Export Pre-Trained YOLO 26 Small Model

```bash
# Navigate to the project directory
cd yolo

# Download and export YOLO26s to TensorRT FP16 engine format
yolo export model=yolo26s.pt format=engine half=true

```

* **`format=engine`**: Converts model to TensorRT format.
* **`half=true`**: Enables FP16 precision for faster processing with minimal accuracy degradation.
* **Output**: Generates `yolo26s.engine` in the working directory.

---

## 📹 Running Real-Time Object Detection

### 1. Live USB Camera Detection

Plug in a USB camera and launch the real-time detection script:

```bash
python3 yolo_detect.py --model yolo26s.engine --source USB0 --resolution 1280x720

```

*Press `Q` while focusing on the video window to stop execution.*

### 2. Custom Model Export & Video Testing

If you have a custom-trained model (e.g., `traintracker2.pt`):

```bash
# Convert custom PyTorch weights to TensorRT engine
yolo export model=traintracker2.pt format=engine half=true

# Run object detection on a video file
python3 yolo_detect.py --model traintracker2.engine --source train_video1.mp4

```

---

## 🚀 Key Performance Insights & Application Example

* **Performance**: YOLO 26S runs at **20–25 FPS** on Jetson Orin Nano with FP16 TensorRT optimization.
* **Improvements in YOLO 26**:
* NMS-free inference
* DFL removal
* Improved loss functions and optimizers
* Up to 43% CPU speed improvements and enhanced small object detection.


* **Example Edge AI Project — Train Tracker Camera**:
* Continuous live detection & object tracking.
* Automatic counting of passing train car types.
* Automated email summary reporting and local video recording upon detection event completion.



---

## 📌 Summary Command Reference

```bash
# System Update
sudo apt update && sudo apt upgrade -y
sudo apt install python3-pip -y
pip install --upgrade pip

# Repository Setup
git clone https://github.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano.git yolo
cd yolo
bash utils/jetson_install_yolo.sh
sudo reboot

# Export & Run
cd yolo
yolo export model=yolo26s.pt format=engine half=true
python3 yolo_detect.py --model yolo26s.engine --source USB0 --resolution 1280x720

```

---

## 🔗 Helpful Links and Resources

* **Learn more about Ultralytics YOLO models**: [https://docs.ultralytics.com/models/](https://docs.ultralytics.com/models/)
* **Ultralytics Platform**: [https://platform.ultralytics.com/](https://platform.ultralytics.com/)
* **Jetson Orin Nano Get Started Guide**: [https://developer.nvidia.com/embedded/learn/get-started-jetson-orin-nano-devkit](https://developer.nvidia.com/embedded/learn/get-started-jetson-orin-nano-devkit)
* **Ultralytics NVIDIA Jetson Guide**: [https://docs.ultralytics.com/guides/nvidia-jetson/](https://docs.ultralytics.com/guides/nvidia-jetson/)
* **My GitHub repository with YOLO code examples**: [https://github.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano](https://github.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano)
