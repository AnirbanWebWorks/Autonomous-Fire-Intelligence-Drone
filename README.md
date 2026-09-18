# 🚁 Autonomous Forest Intelligence (AFI) Drone

**AI-Enabled Autonomous Drone for Forest Fire Severity Analysis, Wildlife Monitoring & Anti-Poaching Surveillance**

*Jan 2025 – Apr 2025 (submitted June 2025) · Narula Institute of Technology (NIT)*

---

## 📌 Overview

The **Autonomous Forest Intelligence (AFI) Drone** — also known as the **Biodiversity Drone Project** — is an AI-powered unmanned aerial vehicle developed for ecological data collection, anomaly identification, and real-time forest observation.

Deforestation, illicit poaching, and forest fires are becoming increasingly common, driving the need for sophisticated, self-sufficient environmental monitoring systems. Traditional methods — manual field surveys, camera traps, satellite imagery — are slow, resource-intensive, and limited in geographic or temporal resolution. The AFI Drone closes this gap by pairing autonomous UAV hardware with onboard/edge AI to deliver rapid, accurate, and non-intrusive ecological assessments in remote or vulnerable forest regions.

> 🏆 Filed Indian Patent — **No. 202631041716** · Published as a research paper · Won **3rd Prize at BRICS**, representing India

---

## 🚀 Key Capabilities

### 🔥 Forest Fire Severity Analysis
- Real-time fire/smoke detection using a YOLOv8-based object detection model
- Fire severity estimation via **U-Net semantic segmentation** (pixel-level fire vs. background classification)
- Automatic alert generation with image data and GPS location sent to a web-based monitoring system

### 🐾 Wildlife Monitoring
- YOLOv8n-based animal detection across 10 species classes (bird, cat, dog, horse, sheep, cow, elephant, bear, zebra, giraffe)
- Running species count per flight for population tracking and biodiversity monitoring
- Non-intrusive aerial observation that minimizes disturbance to wildlife behavior

### 🚨 Anti-Poaching Surveillance
- Dual-model detection: a person-detection YOLO model + a custom weapon-detection model (`weapon.pt`)
- Flags a probable poacher when a person and a weapon bounding box overlap in the same frame — reducing false positives versus a single combined model
- Real-time "Poacher Detected!" alerts with GPS-tagged location logging

### 🛩️ Autonomous Flight & Navigation
- **APM 2.8** flight controller (ArduPilot firmware) for manual, stabilized, altitude-hold, and fully autonomous GPS-guided flight modes
- **NEO M8N GPS** (multi-GNSS: GPS, GLONASS, Galileo, QZSS) for waypoint navigation, loiter, and Return-to-Launch
- Configured and calibrated via **Mission Planner** ground control software

---

## 🎯 Objectives

- Design an AI-powered drone capable of independently exploring varied forest environments
- Capture high-resolution imagery for real-time wildlife and habitat monitoring
- Integrate YOLOv8-based deep learning for accurate detection of animals, human activity, and environmental risks
- Minimize ecological disruption by removing the need for on-site human presence
- Provide decision-makers (forest departments, NGOs, conservationists) with actionable, GPS-tagged data
- Enable early identification of environmental risks — wildfires, deforestation, habitat fragmentation

---

## 🏗️ System Architecture

![Block Diagram](images/block_diagram.png)

Environmental sensors — **DHT11** (temperature/humidity), **BMP280** (altitude/pressure), **MPU6050** (motion), and **MQ8** (hydrogen gas) — feed the **ESP32** microcontroller, which handles data logging, environmental parameter tracking, and communication. Meanwhile, the **APM 2.8** flight controller handles stability and autonomous navigation using GPS, compass, and power-module data, driving the motors/propellers and interfacing with the **FS-i6** receiver for manual override. A camera module feeds live video to a web-based dashboard, which hosts the fire, poacher, and animal detection AI models.

![Structural Design](images/structural_design.png)

The airframe is a standard quadcopter (X-configuration): four 1000KV BLDC motors + ESCs driven by the APM 2.8, a top-mounted GPS module, an FS-iA6 receiver for manual RC fallback, and a gimbal-mounted camera module underneath for aerial imaging.

---

## 🧠 AI / ML Pipeline

![Model Architecture](images/model_architecture.jpeg)

The fire-detection pipeline is trained on a ~66,000-image fire segmentation dataset (fire / non-fire classes), preprocessed (resizing, annotation, normalization) and augmented (rotation, flipping, mosaic) to improve robustness. Two model families work together:

- **YOLOv8** — object detection: backbone for spatial feature extraction → neck for multi-scale feature fusion → detection head predicting bounding boxes, confidence, and class probabilities. Used for fire/smoke, animal, and person detection.
- **U-Net** — semantic segmentation: encoder extracts contextual features, decoder reconstructs spatial information, classifying each pixel as fire or background to estimate **fire severity**, not just presence.

Both models are optimized to be lightweight enough for real-time inference on resource-constrained UAV/edge hardware.

**Software stack:** Flask (Python) backend serving MJPEG video streams and running all three YOLO models; HTML/CSS/JS frontend dashboard with live bounding-box overlays, species counts, and alerts; `pyttsx3` for offline text-to-speech alerts; `pygame` for distinct audio alert tones per detection type (fire / animal / weapon); GPS coordinates pulled from the camera feed metadata and logged with every detection.

![Website Dashboard](images/website_snapshot.png)

![Mission Planner](images/mission_planner.png)

---

## 🔎 Detection Results

| Fire Detection | Animal Detection | Poacher Detection |
|---|---|---|
| ![Fire before](images/fire_before.png) → ![Fire after](images/fire_after.png) | ![Animal before](images/animal_before.png) → ![Animal after](images/animal_after.png) | ![Poacher before](images/poacher_before.png) → ![Poacher after](images/poacher_after.png) |

The fire model achieved **76.8%** live-inference accuracy in the sample run above; the animal model correctly classified all four species present (elephant, giraffe, zebra, bear) with confidence scores of 75–94%; the poacher model correctly flagged an armed individual with an **83.7%** person-detection confidence.

---

## 🔧 Hardware Components

| # | Component | Role |
|---|---|---|
| 1 | **APM 2.8 Flight Controller** ![](images/apm28.png) | Primary flight processing unit — stabilization, autonomous missions, ArduPilot firmware |
| 2 | **NEO M8N GPS Module** ![](images/neo_m8n_gps.png) | Multi-GNSS navigation, waypoints, Return-to-Launch |
| 3 | **BMP280** ![](images/bmp280.png) | Barometric pressure & altitude sensing |
| 4 | **DHT11** ![](images/dht11.png) | Temperature & humidity sensing |
| 5 | **FS-i6 Receiver** ![](images/fsi6_receiver.png) | 6-channel manual RC fallback control |
| 6 | **ESP32 DevKit V1** ![](images/esp32_devkit.png) | Secondary processing — sensor aggregation, data logging, RF telemetry |
| 7 | **ESP32-CAM** ![](images/esp32_cam.png) | Onboard visual sensor — image capture & live streaming |
| 8 | **MQ-8 Sensor** ![](images/mq8.png) | Hydrogen gas / combustion byproduct detection |
| 9 | **2200mAh 3S LiPo Battery** ![](images/battery.png) | 11.1V primary power source (~8–12 min flight time) |
| 10 | **1000KV BLDC Motors** ![](images/bldc_motor.png) | Quadcopter propulsion (×4, X-configuration) |
| 11 | **1045 Propellers** ![](images/propeller.png) | CW/CCW propeller pairs for thrust & yaw control |

**Key specs at a glance:**
- APM 2.8: Atmel ATmega2560 @ 16 MHz, 3-axis gyro/accel/magnetometer, MS5611 barometer
- NEO M8N: ±2.5 m horizontal accuracy, up to 10 Hz update rate, integrated HMC5883L compass
- ESP32: dual-core Tensilica LX6 @ 240 MHz, 520 KB SRAM, Wi-Fi + BLE
- ESP32-CAM: OV2640 2MP camera, up to 1600×1200 (UXGA), microSD storage to 32GB
- Battery: 11.1V (3S) 2200mAh, XT60 connector, low-voltage failsafe triggers auto-RTL at ~10.5V
- Motors: 1000KV rating (~11,100 RPM at 11.1V), paired with 10×4.5" propellers, ~800–900g thrust each

---

## 📊 Performance Data

The report includes field-collected environmental data logged across ~30 days (May–June), captured three times daily (morning/afternoon/night):

- **Temperature:** ranged ~27–30°C (morning), ~34–38°C (afternoon), ~28–29°C (night)
- **Humidity:** ranged ~82–90% (morning), ~48–58% (afternoon), ~81–87% (night)
- **MQ-8 hydrogen readings:** ranged ~3.0–3.6 ppm (morning) up to ~5.0–6.2 ppm (afternoon peak)
- **Battery discharge:** 2200mAh LiPo depletes from 12.6V to ~9.3V (low-voltage cutoff) over ~29 minutes of flight, giving a practical flight window of 8–12 minutes depending on payload and conditions

---

## 🏆 Applications

- Early forest fire detection and severity mapping for rapid ranger response
- Illegal logging monitoring — detecting unauthorized human/equipment presence
- Wildlife population tracking and migration-pattern analysis
- Disaster area surveillance
- Smart forest management — unified GPS-tagged event database, heat maps of high-risk zones, patrol planning

---

## 📈 Future Scope

- Expanded fire-spread prediction using historical + live environmental data
- Species-specific classification expansion beyond the current 10-class model
- Integration with ranger dispatch/alert systems for faster ground response
- Extended flight autonomy and swarm coordination for larger forest coverage
- GPS-tagged detections rendered directly on GIS platforms

---

## 📚 Research & Recognition

- Filed Indian Patent — **No. 202631041716** ("AI-enabled biodiversity drone system... capable of monitoring the forest in real-time and early forest fire detection with the severity of the fire through image-based analysis using deep learning algorithms")
- Published as a research paper
- Won **3rd Prize at BRICS**, representing India
- Inventors: Dr. Bikas Mondal, Mrs. Sanghamitra Layek, Anirban Saha, Somshubhra Bose, Prachi Shaw

---


---

## 👨‍💻 Authors

**Anirban Saha** & **Somshubhra Bose**
B.Tech, Electronics and Instrumentation Engineering (EIE)
Narula Institute of Technology

Guided by **Mrs. Sanghamitra Layek**, Assistant Professor, Dept. of EIE, NIT

**Connect With Me**
- GitHub: [AnirbanWebWorks](https://github.com/AnirbanWebWorks)
- LinkedIn: *(Add your LinkedIn URL here)*
