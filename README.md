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
