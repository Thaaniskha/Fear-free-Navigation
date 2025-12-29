# Fear-free-Navigation
Fear-Free Navigation is a safety-focused navigation system that provides real-time threat detection, safe-path suggestions, and automatic alerts. Unlike traditional navigation apps that only show directions, this system enhances user safety by detecting nearby persons or animals and assessing danger levels. 
import cv2
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

            # Check for person
            if class_name == "person":
                person_detected = True
            # Check for common animals in COCO dataset
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

# Main loop
last_alert_time = 0
cooldown = 3  # seconds between alerts

while True:
    ret, frame = cap.read()
    if not ret:
        print("[ERROR] Failed to grab frame.")
        break

    # Run YOLO detection
    results = model(frame, verbose=False)
    annotated_frame = results[0].plot()

    # Analyze detections
    person_detected, animal_detected = analyze_detections(results)

    # Alert logic
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

    # Show annotated video
    cv2.imshow("Smart Nearby Detector", annotated_frame)

    # Exit if 'q' is pressed
    if cv2.waitKey(1) & 0xFF == ord('q'):
        print("[SYSTEM] Shutting down Smart Detection.")
        break

# Cleanup
cap.release()
cv2.destroyAllWindows()

