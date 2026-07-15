Before you write a single line of training code for **OpenMedLens**, understanding the "YOLO matrix" will save you from deploying a model that either freezes the user's phone or completely misses the medical readings.

The modern Ultralytics YOLO ecosystem (spanning **YOLOv8, YOLO11, and the cutting-edge YOLO26**) scales models across 5 distinct sizes: **Nano (N), Small (S), Medium (M), Large (L), and Extra Large (X)**.

Here is exactly what you need to evaluate regarding size, speed, and hardware constraints to pick your perfect baseline.

---

## 📊 The Core Bounding Box Matrix (At a Glance)

For your Step 1 task—detecting the bounds of a blood pressure screen or a glucometer—you are dealing with a relatively simple geometry problem. Here is how the sizes stack up dynamically:

| Model Variant | Parameter Count | File Size (Approx. `.pt`) | Quantized Edge Size (`.tflite`) | Inference Speed (Edge CPU) | Best Use Case for OpenMedLens |
| --- | --- | --- | --- | --- | --- |
| **Nano (N)** | ~2.5M - 3.2M | ~6 MB - 10 MB | **~2 MB - 5 MB** | **~10ms - 20ms** (Blistering Fast) | **The Winner.** Running live on older Android/iOS camera views. |
| **Small (S)** | ~9M - 12M | ~20 MB - 25 MB | ~8 MB - 12 MB | ~25ms - 40ms (Smooth) | Good fallback if your screens have intense glare patterns. |
| **Medium (M)** | ~20M - 25M | ~50 MB - 60 MB | ~20 MB - 30 MB | ~50ms - 80ms (Noticeable Lag) | Server-side microservices processing batch image uploads. |
| **Large / XL** | 40M+ | 100 MB+ | 50 MB+ | >100ms (Heavy Lag) | Overkill. Avoid for home medical device tracking. |

---

## 🧠 The 3 Universal Variables You Must Balance

### 1. The Speed Threshold (Frames Per Second)

To give patients a smooth, premium experience, the camera view needs to track the device in real-time.

* To hit a seamless **30 to 60 Frames Per Second (FPS)** on a mobile phone without a high-end external GPU, the model must execute its math in **under 30 milliseconds per frame**.
* **Nano models** achieve this easily, consuming tiny slivers of processing overhead and keeping the user's smartphone from overheating.

### 2. The Accuracy Metric ($mAP_{50-95}$)

Mean Average Precision ($mAP$) measures how precisely the box fits the device screen.

* A higher size variant (like Medium) captures subtle background textures better.
* However, because home medical devices have very distinct visual structures (a square LCD bezel with clear contrast edges), a **Nano or Small** variant can easily achieve over 95% accuracy after being exposed to just a few hundred clean, diverse training images.

### 3. Image Input Size (`imgsz`)

By default, YOLO models are trained at **$640 \times 640$ pixels**.

* When building your data pipeline, stick to `imgsz=640`.
* Downscaling a 4K phone camera stream to $640 \times 640$ parameters keeps the input data array compact enough to process near-instantaneously, while retaining plenty of structural clarity to isolate the screen border perfectly.

---

## ⚡ Which Generation to Pick in 2026?

The choice of *which* generation of YOLO to load from the `ultralytics` package radically alters your edge performance:

* **YOLOv8:** The classic industry workhorse. It is incredibly stable and has millions of community forum threads troubleshooting mobile exports, though it relies on standard post-processing filters.
* **YOLO11:** Introduces optimized parameter blocks (C3k2 modules). A YOLO11 Small model achieves the structural accuracy of an older YOLOv8 Medium model while using roughly **22% fewer parameters**, making it highly memory-efficient.
* **YOLO26:** The cutting-edge choice for edge engineering. It is completely **NMS-free (Non-Maximum Suppression-free)**, meaning it maps spatial boxes natively in a single end-to-end mathematical pass. This design delivers up to **43% faster inference on basic mobile phone CPUs** compared to older generations.

## 🛠️ The Strategic Recommendation for Your Initial Training Run

Start your project explicitly with **`yolo26n` (YOLO26 Nano)** or **`yolo11n` (YOLO11 Nano)**.

```python
from ultralytics import YOLO

# Load the ultra-lightweight Nano backbone built for edge AI
model = YOLO("yolo26n.pt") 

# Train on your custom medical device dataset
results = model.train(
    data="meddevices.yaml", 
    epochs=100, 
    imgsz=640, 
    device=0  # Uses your local GPU
)

# Export straight to an optimized edge format
model.export(format="tflite", int8=True)  # Ready for local mobile deployment!

```

**Why this is your sweet spot:** The Nano variant compiles into an incredibly small footprint, takes very little time to train on a standard home developer machine, handles spatial classification gracefully, and drops into your open-source framework with minimal architectural friction.
