# 🖐️ Finger Count LED Control

This project combines Python libraries and an Arduino board to control LEDs based on the number of fingers detected through a webcam. The system uses OpenCV, a hand tracking module, and `pyfirmata` for Arduino communication to create a real-time finger counting system.

## 📦 Setup and Installation

### Required Libraries

Make sure you have the following Python libraries installed:
- `pyfirmata` for Arduino communication
- `opencv-python` for video processing
- `cvzone` for hand tracking

####Install these libraries using pip:

pip install pyfirmata opencv-python cvzone
Hardware Requirements
Arduino board (e.g., Arduino Uno)
LEDs (5 pieces)
Resistors (220Ω recommended for LEDs)
Jumper wires
Wiring'''


Connect LEDs to the Arduino:

LED 1 to digital pin 8
LED 2 to digital pin 9
LED 3 to digital pin 10
LED 4 to digital pin 11
LED 5 to digital pin 12
Connect the Arduino to your computer via USB.

💻 Arduino Sketch
Upload the Arduino Sketch:

Use the Arduino IDE to upload the following code to your Arduino board. This sketch sets up the digital pins for controlling the LEDs:

cpp
Copy code
#include <Firmata.h>

void setup() {
    // Initialize communication with the computer
    Firmata.begin(57600);
    // Set digital pins as outputs
    pinMode(8, OUTPUT);
    pinMode(9, OUTPUT);
    pinMode(10, OUTPUT);
    pinMode(11, OUTPUT);
    pinMode(12, OUTPUT);
}

void loop() {
    // Handle Firmata commands
    Firmata.processInput();
}
Save this sketch as firmata_sketch.ino and upload it to the Arduino.

🚀 Running the Code
Run the Python Script:

Save the following Python script as finger_count_led_control.py and execute it:

python
Copy code
import pyfirmata
import cv2
import controller as cnt
from cvzone.HandTrackingModule import HandDetector

# Initialize Arduino board
comport = 'COM5'  # Update this if necessary
board = pyfirmata.Arduino(comport)

# Define LEDs
led_1 = board.get_pin('d:8:o')
led_2 = board.get_pin('d:9:o')
led_3 = board.get_pin('d:10:o')
led_4 = board.get_pin('d:11:o')
led_5 = board.get_pin('d:12:o')

def led(fingerUp):
    if fingerUp == [0, 0, 0, 0, 0]:
        led_1.write(0)
        led_2.write(0)
        led_3.write(0)
        led_4.write(0)
        led_5.write(0)
    elif fingerUp == [0, 1, 0, 0, 0]:
        led_1.write(1)
        led_2.write(0)
        led_3.write(0)
        led_4.write(0)
        led_5.write(0)
    elif fingerUp == [0, 1, 1, 0, 0]:
        led_1.write(1)
        led_2.write(1)
        led_3.write(0)
        led_4.write(0)
        led_5.write(0)
    elif fingerUp == [0, 1, 1, 1, 0]:
        led_1.write(1)
        led_2.write(1)
        led_3.write(1)
        led_4.write(0)
        led_5.write(0)
    elif fingerUp == [0, 1, 1, 1, 1]:
        led_1.write(1)
        led_2.write(1)
        led_3.write(1)
        led_4.write(1)
        led_5.write(0)
    elif fingerUp == [1, 1, 1, 1, 1]:
        led_1.write(1)
        led_2.write(1)
        led_3.write(1)
        led_4.write(1)
        led_5.write(1)

# Initialize hand detector
detector = HandDetector(detectionCon=0.8, maxHands=1)
video = cv2.VideoCapture(0)

while True:
    ret, frame = video.read()
    frame = cv2.flip(frame, 1)
    hands, img = detector.findHands(frame)
    if hands:
        lmList = hands[0]
        fingerUp = detector.fingersUp(lmList)
        print(fingerUp)
        cnt.led(fingerUp)
        cv2.putText(frame, f'Finger count: {fingerUp.count(1)}', (20, 460), cv2.FONT_HERSHEY_COMPLEX, 1, (255, 255, 255), 1, cv2.LINE_AA)
    cv2.imshow("frame", frame)
    if cv2.waitKey(1) == ord("k"):
        break

video.release()
cv2.destroyAllWindows()
Check Output: The LEDs should light up based on the number of fingers detected. The finger count will also be displayed on the video feed.

⚠️ Common Errors and Troubleshooting
Error: AttributeError: 'NoneType' object has no attribute 'shape'

Cause: This error occurs if the webcam is not properly initialized or if it cannot read frames.

Solution: Ensure that your webcam is connected and functioning. Verify that the cap = cv2.VideoCapture(0) line uses the correct camera index.

Error: ConnectionRefusedError

Cause: This error may occur if there’s an issue with the communication between the Python script and the Arduino board.

Solution: Check the COM port specified ('COM5' in the example) and ensure it matches the port your Arduino is connected to. Update the comport variable if necessary.

Error: ModuleNotFoundError

Cause: This error happens if required libraries are not installed.

Solution: Install the missing libraries using pip, as shown in the setup section.
🎉 Conclusion

This project demonstrates a simple way to control LEDs using hand gestures. Modify the code and wiring to suit your needs and explore additional features like adding more LEDs or improving hand detection accuracy.

Feel free to contribute improvements or report issues.

Created by Brave Boy Codes 👨‍💻.