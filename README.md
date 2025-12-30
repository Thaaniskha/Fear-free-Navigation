import cv2
from ultralytics import YOLO
import winsound
import time

# ================== MODEL ==================
model = YOLO("yolov8n.pt")
CLASS_NAMES = model.names

# ================== COLORS (BGR) ==================
GREEN = (0, 255, 0)
YELLOW = (0, 255, 255)
RED = (0, 0, 255)
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# ================== ALERT ==================
def play_alert(freq, dur=600):
    winsound.Beep(freq, dur)

# ================== DETECTION ANALYSIS ==================
ANIMALS = {
    "cat", "dog", "horse", "sheep", "cow",
    "elephant", "bear", "zebra", "giraffe", "bird"
}

def analyze_detections(results):
    threat_level = "SAFE"
    color = GREEN

    for r in results:
        for box in r.boxes:
            cls_id = int(box.cls[0])
            class_name = CLASS_NAMES[cls_id].lower()

            if class_name == "person":
                return "PERSON DETECTED", RED
            elif class_name in ANIMALS:
                threat_level = "ANIMAL DETECTED"
                color = YELLOW

    return threat_level, color

# ================== CAMERA ==================
cap = cv2.VideoCapture(0)
if not cap.isOpened():
    print("[ERROR] Webcam not accessible")
    exit()

print("[SYSTEM] Fear-Free Navigation Activated (Press Q to quit)")

last_alert_time = 0
cooldown = 3

# ================== MAIN LOOP ==================
while True:
    ret, frame = cap.read()
    if not ret:
        break

    results = model(frame, verbose=False)
    threat_text, theme_color = analyze_detections(results)

    annotated = frame.copy()

    # Draw bounding boxes manually for color control
    for r in results:
        for box in r.boxes:
            x1, y1, x2, y2 = map(int, box.xyxy[0])
            cls_id = int(box.cls[0])
            label = CLASS_NAMES[cls_id]

            cv2.rectangle(annotated, (x1, y1), (x2, y2), theme_color, 2)
            cv2.putText(
                annotated, label.upper(),
                (x1, y1 - 10),
                cv2.FONT_HERSHEY_SIMPLEX,
                0.6, theme_color, 2
            )

    # ================== HUD BAR ==================
    cv2.rectangle(annotated, (0, 0), (annotated.shape[1], 50), theme_color, -1)
    cv2.putText(
        annotated,
        f"STATUS: {threat_text}",
        (20, 35),
        cv2.FONT_HERSHEY_SIMPLEX,
        1, BLACK, 2
    )

    # ================== ALERT CONTROL ==================
    now = time.time()
    if now - last_alert_time > cooldown:
        if threat_text == "PERSON DETECTED":
            play_alert(1200)
            last_alert_time = now
        elif threat_text == "ANIMAL DETECTED":
            play_alert(800)
            last_alert_time = now

    cv2.imshow("Fear-Free Navigation | Smart Safety View", annotated)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()

