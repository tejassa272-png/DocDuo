# 🛡️ MedFuse OS | Edge-First Clinical Decision Support System

<div align="center">
  
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
![ONNX](https://img.shields.io/badge/ONNX_Runtime-005CED?style=for-the-badge&logo=onnx)
![Gemini](https://img.shields.io/badge/Google_Gemini-8E75B2?style=for-the-badge&logo=google)
![Llama3](https://img.shields.io/badge/Groq_LPU-F58025?style=for-the-badge&logo=groq)
![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv)

**MedFuse OS** is an offline-first, hybrid edge-cloud Clinical Decision Support System (CDSS) engineered for bandwidth-starved environments. 

Developed by **Team DocDuo**

</div>

---

## 🌍 The Clinical Infrastructure Problem
Rural and tier-2 healthcare centers suffer from a dual-infrastructure crisis:
1. **Specialist Scarcity:** A lack of specialized pulmonologists, neurologists, and dermatologists available on-site for immediate triage.
2. **Cloud Bottlenecks:** Uploading massive 50MB raw MRI or X-Ray files to cloud-based AI servers over unreliable 3G/4G clinic networks is impossible during a medical emergency. 

## 💡 The MedFuse Solution
**We bring the specialist to the clinic, directly on the edge.** MedFuse utilizes a decoupled architecture where heavy diagnostic imaging never leaves the local hospital network. Deep learning vision models execute instantly on edge hardware via ONNX Runtime. Simultaneously, lightweight text-based data (like OCR blood reports) is intelligently routed to a Highly Available (HA) cloud LLM engine for multimodal cognitive synthesis.

---

## ✨ System Architecture & Core Modules

### 1. 🧠 Zero-Latency Edge Vision Pipeline
Heavy diagnostic imaging is processed entirely offline using optimized ONNX models. Image preprocessing is handled dynamically via OpenCV (RGB conversion, 224x224 resizing, Mean/Std normalization).
* **Pulmonology:** ResNet-18 model for instantaneous Chest X-Ray Pneumonia detection.
* **Neurology:** EfficientNet model for Brain Tumor classification (Glioma, Meningioma, Pituitary).
* **Dermatology:** EfficientNet model for 8-class Skin Lesion/Cancer screening.

### 2. ⚡ Triple-Tier HA Cognitive Routing
To guarantee the CDSS *never* goes down during a clinical session, MedFuse utilizes a sophisticated failover LLM routing engine:
* **Tier 1 (Primary):** Google Gemini 2.5 Flash for rapid, state-of-the-art multimodal synthesis.
* **Tier 2 (LPU Failover):** Groq API (Llama 3.1 8B) running on high-speed hardware for sub-second text generation if Google's API limits are reached.
* **Tier 3 (Offline "God Mode"):** If the hospital loses WAN connectivity completely, the system auto-switches to an offline, deterministic clinical safety net—ensuring doctors are never locked out of their workflow.

### 3. 📄 Local Edge OCR Digitization
Zero cloud API dependencies for OCR. MedFuse utilizes **PyTesseract** combined with advanced **OpenCV** pipelines:
* Upscales the image by 2x.
* Applies `Adaptive Thresholding` to destroy shadows on crumpled paper.
* Uses Tesseract `PSM 4` configuration to read medical tables.
* Parses 8 key biomarkers (Hemoglobin, RBC, WBC, Platelets, etc.) into structured JSON arrays via regex.

### 4. 🏥 Decoupled Database State Management
* **`hospital_database.json`**: Handles lightweight, high-frequency queries like RBAC Auth, Triage Queues, and Outbreak tracking.
* **`medical_records.json`**: Dedicated heavy-storage container for decoupled patient history and base64 diagnostic images.
* **Automated Workflow:** Seamless state synchronization automatically moves patients from `Routine` -> `Workspace` -> `Closed Files` when a doctor syncs a medical record or a patient dismisses an appointment.

### 5. 🗺️ Epidemiological Radar & DDI Alerts
* **Global Outbreak Map:** A live, auto-purging telemetry map (Leaflet.js) that plots active infectious outbreaks (e.g., Pneumonia) based on dynamic patient geofencing. Cases older than 7 days are automatically purged.
* **DDI Safety Net:** GenAI cross-references new prescriptions with historical data to flag severe Drug-Drug Interactions, triggering a custom UI override modal for the physician.

---

## 🌐 API Reference (FastAPI Backend)

The backend provides a fully documented REST API. Key endpoints include:

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/auth/login` | RBAC Authentication for Admins, Doctors, and Patients. |
| `POST` | `/emergency/sos` | Escalates a patient's triage status to "Critical" system-wide. |
| `POST` | `/appointments/book` | Routes a patient into a specific physician's triage queue. |
| `POST` | `/predict/pneumonia` | Ingests X-Ray file, runs local ResNet18, updates Outbreak Map. |
| `POST` | `/analyze/blood-report` | Ingests physical report photo, runs OpenCV+Tesseract extraction. |
| `POST` | `/doctor/check-ddi` | Triggers HA LLM router to verify prescription safety. |
| `POST` | `/doctor/synthesize` | Multi-modal fusion of Edge Vision results and OCR Blood data. |
| `POST` | `/doctor/save-patient-record` | Commits medical payload to decoupled JSON DB and auto-closes queue status. |

---

## 🚀 Installation & Local Deployment

### Prerequisites
1. **Python 3.9+**
2. **Tesseract OCR:** Must be installed on the host machine. 
   * *Windows:* Install to `C:\Program Files\Tesseract-OCR\tesseract.exe` (Ensure this path matches path in `server.py`).
   * *Linux:* `sudo apt-get install tesseract-ocr`

### Step-by-Step Setup

**1. Clone the repository**
```bash
git clone https://github.com/tejassa272-png/DocDuo.git
cd DocDuo
```
**2. Run Server.py file**

**3. open the app.html file**
