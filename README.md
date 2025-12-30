# 🚦 Fear-Free Navigation – Smart Nearby Detection System

Fear-Free Navigation is a **real-time safety awareness system** that enhances personal security by detecting **nearby persons and animals** using computer vision and triggering **instant visual and audio alerts**.

Unlike traditional navigation systems that only focus on directions, this project prioritizes **situational awareness and user safety**.

---

## 🎯 Why Fear-Free Navigation?

Most navigation apps:
- ❌ Ignore real-world threats
- ❌ Provide zero environmental awareness
- ❌ React only after danger occurs

**Fear-Free Navigation** actively monitors surroundings and alerts users **before** a situation escalates.

---

## ✨ Key Features

🟢 **Live Webcam Monitoring**  
🧍 **Human Detection (High Priority Alert)**  
🐕 **Animal Detection (Medium Priority Alert)**  
🔊 **Automatic Sound Alerts with Cooldown Control**  
📦 **YOLOv8 Object Detection**  
🎨 **Real-Time Bounding Boxes on Video Feed**

---

## 🧠 How It Works

1. Webcam captures live video frames  
2. YOLOv8 processes each frame  
3. Objects are classified using the COCO dataset  
4. System checks for:
   - 🔴 Person detected → High-frequency alert
   - 🟡 Animal detected → Medium-frequency alert
5. Bounding boxes are displayed in real time  

---

## 🚨 Alert Logic

| Detected Object | Alert Type | Sound |
|----------------|----------|-------|
| Person | 🔴 High Risk | High-frequency beep |
| Animal | 🟡 Medium Risk | Medium-frequency beep |
| None | 🟢 Safe | No alert |

Alerts are controlled using a **cooldown timer** to avoid noise overload.

---

## 🛠️ Technologies Used

- **Python**
- **OpenCV**
- **YOLOv8 (Ultralytics)**
- **COCO Dataset**
- **winsound (Windows Audio Alerts)**

---

## ⚙️ Installation

```bash
pip install opencv-python ultralytics
