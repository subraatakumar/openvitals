
# OpenVitals 🩺👁️

OpenVitals is a privacy-first, local-edge computer vision framework designed to automatically recognize home medical hardware (like blood pressure monitors, glucometers, and pulse oximeters) from image streams and extract their precise alphanumeric readings using lightweight, open-source AI models.

Built specifically for digital health products and telehealth architectures, OpenVitals executes its entire pipeline client-side (100% offline). This ensures strict HIPAA data sovereignty, zero external API fees, and instant response times even on lower-end mobile devices.

---

## 🔥 Key Features

- **Local-First Architecture:** Runs completely offline via Apple MLX (iOS/macOS) and Google LiteRT / ONNX Runtime (Android/Web). Patient biometric data never leaves the hardware boundary.
- **Two-Stage Intelligent Pipeline:** Chains hyper-fast structural bounding box detection (YOLOv8/v11) with fine-tuned Vision-Language OCR models (Qwen2.5-VL / PaddleOCR) optimized for challenging 7-segment digital displays.
- **Environmental Resiliency:** Pre-trained to withstand glare, motion blur, steep camera angles, and low-light environments common in home-care settings.
- **Extensible Schema Native:** Outputs structured JSON payloads mapping detected metrics directly to standardized clinical observations.

---

## 🏗️ Architectural Pipeline

OpenVitals splits computing logic into two modular steps to achieve maximum execution efficiency on mobile NPUs and edge gateways:


```

[ Camera Stream / Frame ]
│
▼
┌───────────────────────────────────┐
│ 1. Device Classifier & Bounding   │  ◄── YOLOv8-Nano (Runs locally in ~12ms)
└───────────────────────────────────┘       Identifies: "Glucometer", "BP_Monitor"
│
▼ [ Crops Display Bounds ]
┌───────────────────────────────────┐
│ 2. Digital Screen Extraction      │  ◄── Quantized Vision OCR (LiteRT / Qwen-VL)
└───────────────────────────────────┘       Handles glare, typography, and layout
│
▼
[ Clean JSON Output: Systolic, Diastolic, Pulse ]

```

---

## ⚙️ Getting Started

### Installation

#### Python (Server & Backend Automation)
```bash
pip install openvitals

```

#### Node.js / React Native (Mobile Client SDK)

```bash
npm install @openvitals/core @openvitals/react-native

```

### Quickstart Example (Python)

```python
from openvitals import OpenVitalsEngine

# Initialize engine with lightweight optimized edge weights
engine = OpenVitalsEngine(
    detector_model="yolov8n-vitals.onnx",
    ocr_model="qwen2.5-vl-vitals-quantized.gguf",
    execution_provider="cpu" # Or 'cuda', 'coreml', 'nnapi'
)

# Analyze a raw camera frame or image file
result = engine.analyze_image("path/to/patient_bp_photo.jpg")

# Print the structured clinical observation payload
print(result.to_json(indent=2))

```

#### Sample JSON Output Payload

```json
{
  "status": "success",
  "device_detected": "blood_pressure_monitor",
  "confidence_score": 0.964,
  "timestamp": "2026-07-15T07:55:00Z",
  "metrics": {
    "systolic": {
      "value": 120,
      "unit": "mmHg"
    },
    "diastolic": {
      "value": 80,
      "unit": "mmHg"
    },
    "pulse": {
      "value": 72,
      "unit": "bpm"
    }
  }
}

```

---

## 📊 Model Zoo & Supported Hardware

OpenVitals targets and isolates specific display structures out of the box. Model checkpoints are hosted publicly on Hugging Face:

| Target Device Class | Core Metrics Extracted | Baseline Model Stack | Edge Size (Quantized) |
| --- | --- | --- | --- |
| **Blood Pressure Monitors** | Systolic (mmHg), Diastolic (mmHg), Pulse (bpm) | YOLOv8n + 7-Seg Custom OCR | ~65 MB |
| **Blood Sugar Glucometers** | Blood Glucose (mg/dL or mmol/L) | YOLOv8n + Qwen2.5-VL-1.5B | ~140 MB |
| **Pulse Oximeters** | SpO2 (%), Pulse Rate (bpm) | YOLOv8n + PaddleOCR Lite | ~45 MB |
| **Digital Thermometers** | Body Temperature (°F / °C) | YOLOv8n + 7-Seg Custom OCR | ~38 MB |

---

## 🔒 Security, Compliance & Data Sovereignty

* **HIPAA Safe Harbor Compliance:** By design, OpenVitals does not intercept, extract, or transmit any Protected Health Information (PHI). Because it contains no cloud outbound networking libraries, it acts entirely as a zero-risk boundary utility inside telehealth systems.
* **Air-Gapped Auditing:** Enterprise teams can comfortably deploy OpenVitals inside fully firewalled, air-gapped container groups or private subnets.

---

## 🤝 Contributing

We welcome contributions from digital health engineers, data scientists, and clinical tools developers!

1. Fork the repository (`git checkout -b feature/amazing-feature`).
2. Commit your changes (`git commit -m 'Add support for specialized infusion pumps'`).
3. Push to the branch (`git push origin feature/amazing-feature`).
4. Open a Pull Request.

Please review our `CONTRIBUTING.md` for our dataset annotation standards and testing rules.

---

## 📄 License

OpenVitals is open-source software licensed under the **Apache License 2.0**. You are free to modify, distribute, and embed this package inside commercial telehealth systems with zero royalties.
