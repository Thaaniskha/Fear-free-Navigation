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
- import cv2
from ultralytics import YOLO
import winsound
import time

# Initialize YOLOv8 model (make sure yolov8n.pt is in same folder)
model = YOLO("yolov8n.pt")

# Set class names for COCO dataset
CLASS_NAMES = model.names

# Function to play a simple Windows beep alert
def play_alert(frequency=1000, duration=500):
    winsound.Beep(frequency, duration)

# Function to check for people or animals in detections
def analyze_detections(results):
    person_detected = False
    animal_detected = False

    for r in results:
        boxes = r.boxes
        for box in boxes:
            cls_id = int(box.cls[0])
            class_name = CLASS_NAMES[cls_id].lower()

            if class_name == "person":
                person_detected = True
            elif class_name in [
                "cat", "dog", "horse", "sheep", "cow",
                "elephant", "bear", "zebra", "giraffe", "bird"
            ]:
                animal_detected = True

    return person_detected, animal_detected

# Start video capture
cap = cv2.VideoCapture(0)

if not cap.isOpened():
    print("[ERROR] Could not open webcam.")
    exit()

print("[SYSTEM] Smart Nearby Detection Active — press 'q' to quit")

last_alert_time = 0
cooldown = 3

while True:
    ret, frame = cap.read()
    if not ret:
        print("[ERROR] Failed to grab frame.")
        break

    results = model(frame, verbose=False)
    annotated_frame = results[0].plot()

    person_detected, animal_detected = analyze_detections(results)

    current_time = time.time()
    if current_time - last_alert_time > cooldown:
        if person_detected:
            print("[ALERT] Person detected nearby! Paying attention...")
            play_alert(1200, 700)
            last_alert_time = current_time
        elif animal_detected:
            print("[WARNING] Animal detected nearby! Possible danger!")
            play_alert(800, 700)
            last_alert_time = current_time

    cv2.imshow("Smart Nearby Detector", annotated_frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        print("[SYSTEM] Shutting down Smart Detection.")
        break

cap.release()
cv2.destroyAllWindows()


---

## ⚙️ Installation

```bash
pip install opencv-python ultralytics
