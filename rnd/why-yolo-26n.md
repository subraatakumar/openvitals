The absolute best model for your **OpenMedLens** project is the **Ultralytics YOLO26 Nano (`yolo26n.pt`)**.

Because OpenMedLens is designed to run locally on mobile phones and edge hardware in compliance-heavy healthcare environments, you need a model that prioritizes extreme latency efficiency and smooth exports without sacrificing accuracy. YOLO26 Nano hits the absolute sweet spot.

---

## 🏆 Why YOLO26 Nano is the Winner for OpenMedLens

The architectural shifts introduced in YOLO26 resolve the historical bottlenecks that developers face when moving vision models from Python onto mobile chips:

### 1. Native NMS-Free Design (No More Mobile Lag Spikes)

In legacy models like YOLOv8 or YOLO11, the model outputs thousands of overlapping potential boxes. The phone's CPU then has to run a heavy mathematical filter called **Non-Maximum Suppression (NMS)** to clean them up. This creates latency spikes on phones.

* **The YOLO26 Advantage:** It is natively end-to-end. It predicts the exact coordinates of the medical screen in a single mathematical pass, completely eliminating NMS lag and providing deterministic, predictable performance on device CPUs.

### 2. DFL Removal (Seamless LiteRT / TFLite Exports)

Older versions use *Distribution Focal Loss (DFL)* to fine-tune box boundaries, which relies on heavy operations that mobile NPU/GPU neural compilers often struggle to convert cleanly.

* **The YOLO26 Advantage:** The removal of DFL significantly simplifies the output graph architecture. When you export your model to a `.tflite` (LiteRT) or Apple `CoreML` format, it compiles seamlessly into hardware-accelerated instructions with zero structural friction.

### 3. Up to 43% Faster CPU Inference

If your app runs on an older Android device where the hardware NPU isn't available, the fallback is the main CPU.

* **The Benchmark:** A standard `yolo26n` achieves an inference speed of roughly **38.9 ms on a standard CPU** compared to `yolov8n` which clocks in at over **80.4 ms**. This massive speed boost ensures your screen-tracking bounding box feels buttery-smooth to a patient holding their phone camera up to a monitor.

---

## 📊 Quick Size & Parameter Reference

When deploying mobile assets, you must stay within reasonable app bundle size limitations:

* **File Footprint:** The raw PyTorch `yolo26n.pt` file is only about **5 MB**. When quantized to 8-bit integers (`int8`) for your LiteRT mobile engine, the production asset drops to an incredibly tiny size.
* **Memory Footprint:** At only **2.4 Million parameters**, it can easily sit continuously in a background camera frame loop without triggering the Android OS memory optimizer to kill the application process.

---

## 🚀 Execution Strategy

When configuring your `dataset.yaml` file for training, structure the project to take advantage of these capabilities:

```python
from ultralytics import YOLO

# 1. Load the edge-optimized YOLO26 Nano model
model = YOLO("yolo26n.pt")

# 2. Run local training loop using the advanced MuSGD optimizer 
# (MuSGD provides incredibly stable training runs on custom layouts)
model.train(
    data="openmedlens_devices.yaml",
    epochs=100,
    imgsz=640,          # Standard crisp resolution for capturing screen bezels
    optimizer="MuSGD"   # Leverages YOLO26's optimized training convergence
)

# 3. Export straight to standard Google LiteRT format
model.export(format="tflite", int8=True)

```

By standardizing OpenMedLens on **YOLO26 Nano**, you ensure your open-source engine remains future-proof, builds cleanly into Android and iOS application wrappers, and delivers instant, private on-device medical vision features for zero external cloud runtime fees.
