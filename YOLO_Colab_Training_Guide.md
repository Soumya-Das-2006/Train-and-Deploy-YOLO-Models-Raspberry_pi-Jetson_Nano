# How to Train YOLO Object Detection Models in Google Colab (YOLO26, YOLO11, YOLOv8)

A comprehensive step-by-step guide on gathering a dataset, labeling images with Label Studio, training custom Ultralytics YOLO models (YOLO26, YOLO11, YOLOv8) in Google Colab using a T4 GPU, and running inference locally on your PC via a custom Python script.

Based on the repository by **Soumya Das**.

---

## 📋 Prerequisites & Tools

- **Hardware**: PC/Laptop (NVIDIA GPU optional for local run, free GPU provided in Google Colab)
- **Software**: Anaconda / Miniconda, Web Browser
- **Accounts**: Google Account (for Google Colab), Label Studio (local account)

---

## 🛠️ Step 1: Dataset Gathering & Setup

1. **Collect Images**:
   - Gather **100–200 images** for a proof-of-concept dataset.
   - Capture images under varied backgrounds, angles, and lighting conditions.
2. **Organize Folders**:
   - Create a main project directory on your local PC: `Documents/YOLO/`
   - Create a subfolder for raw images: `Documents/YOLO/candy_images/`
   - Move all captured/downloaded training photos into `candy_images/`.

---

## 🏷️ Step 2: Image Labeling with Label Studio

### 1. Install Anaconda & Create Environment
Download Anaconda from [anaconda.com](https://www.anaconda.com) and install it with default options. Open **Anaconda Prompt** and run:

```bash
# Create a dedicated Python 3.12 conda environment
conda create --name YOLO_env1 python=3.12 -y

# Activate the environment
conda activate YOLO_env1

# Install Label Studio
pip install label-studio

# Launch Label Studio (opens automatically in your web browser)
label-studio
```

### 2. Configure Label Studio Project
* **Sign Up**: Register a local account (e.g., fake@fake.com / fake-password).
* **Create Project**: Click Create Project and name it (e.g., Candy Detection).
* **Import Data**: Go to Data Import and drag-and-drop images (import up to 100 images per batch).
* **Labeling Setup**:
  * Go to Labeling Setup -> Object Detection.
  * Remove default classes (Airplane, Car) and add custom class names (e.g., AirHeads, Skittles, Snickers).
  * Click Save.

### 3. Label Images & Export
* Draw tight bounding boxes around target objects using hotkeys (1, 2, 3...).
* Click Submit after each image.
* Once finished, click Export at the top right -> Select YOLO format -> Click Export.
* Extract/rename the downloaded zip file to `data.zip` and place it in your local `Documents/YOLO/` directory.

---

## ☁️ Step 3: Train YOLO Model in Google Colab

Open the training notebook in Google Colab.

### 1. Enable T4 GPU Acceleration
Click Runtime -> Change runtime type.
Select T4 GPU -> Click Save.

Verify GPU access:

```python
!nvidia-smi
```

### 2. Upload Dataset & Prepare Directories
* Click the Files (folder icon) on the left sidebar in Colab.
* Drag and drop your `data.zip` file into the sidebar.

Unzip the dataset:

```bash
!unzip data.zip -d /content/
```

Run the train/val split script (90% training, 10% validation) and install Ultralytics:

```python
!pip install ultralytics
```

### 3. Generate data.yaml Configuration
Create the dataset configuration file defining paths and classes:

```yaml
path: /content/data
train: images/train
val: images/val

names:
  0: AirHeads
  1: Nerds
  2: Skittles
  # Add remaining classes...
```

### 4. Execute Model Training
Train a YOLO11s (or YOLO26s / YOLOv8s) model for 60 epochs:

```bash
yolo detect train data=data.yaml model=yolo11s.pt epochs=60 imgsz=640
```

* **Best Weights**: Saved at `runs/detect/train/weights/best.pt`.

Download Weights: Compress and download the trained model:

```bash
zip -r my_model.zip runs/detect/train/weights/best.pt
```

Download `my_model.zip` to your PC's `Documents/YOLO/` folder and extract it (`my_model.pt`).

---

## 💻 Step 4: Run Inference Locally on PC

Return to your local Anaconda Prompt:

```bash
# Reactivate environment
conda activate YOLO_env1

# Navigate to your project folder
cd C:\Users\YourUsername\Documents\YOLO\my_model

# Install Ultralytics & Dependencies
pip install ultralytics
```

*(Optional for NVIDIA GPU acceleration)*: Install CUDA-enabled PyTorch from pytorch.org:

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121 --upgrade
```

### Download & Execute Detection Script
Download the custom detection script:

```bash
curl -o yolo_detect.py https://raw.githubusercontent.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano/refs/heads/main/yolo_detect.py
```

#### 1. Live USB Webcam Detection
```bash
python yolo_detect.py --model my_model.pt --source usb0 --resolution 1280x720
```

#### 2. Detection on Video File
```bash
python yolo_detect.py --model my_model.pt --source candy_test1.mov
```

---

## 📌 Summary Command Reference

```bash
# --- LOCAL: Anaconda Label Studio Setup ---
conda create --name YOLO_env1 python=3.12 -y
conda activate YOLO_env1
pip install label-studio
label-studio

# --- COLAB: Model Training Command ---
yolo detect train data=data.yaml model=yolo11s.pt epochs=60 imgsz=640

# --- LOCAL: Run Inference ---
conda activate YOLO_env1
pip install ultralytics
curl -o yolo_detect.py https://raw.githubusercontent.com/Soumya-Das-2006/Train-and-Deploy-YOLO-Models-Raspberry_pi-Jetson_Nano/refs/heads/main/yolo_detect.py
python yolo_detect.py --model my_model.pt --source usb0 --resolution 1280x720
```
