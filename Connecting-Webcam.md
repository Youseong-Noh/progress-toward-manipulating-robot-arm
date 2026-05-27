# Connecting Webcam

NOTE: This step is connected with Connecting-Arduino

---

## Overview

- Install software required to perform project through webcam
- Testing a color detection program to check if the software works well with the webcam

---

## Features

- Webcam detects a red-colored object when it appears on the screen
- Displays the word 'red detected'
- Displays the word 'no red' when nothing is detected

---

## Tech Stack

- Python
- Opencv
- Numpy

---

## Installation

- Install Opencv in Anaconda Prompt

  ```bash
  pip install opencv
  ```

- Install Numpy in Anaconda Prompt

  ```bash
  pip install numpy
  ```

---

## Usage

Run this code in Spyder:

```python

import cv2
import time
import numpy as np

cam = cv2.VideoCapture(1)

while True:
    ret, frame = cam.read()

    if not ret:
        print("CANNOT READ THE CAMERA.")
        break

    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    lower_red1 = np.array([0, 100, 100])
    upper_red1 = np.array([10, 255, 255])

    lower_red2 = np.array([160, 100, 100])
    upper_red2 = np.array([179, 255, 255])

    mask1 = cv2.inRange(hsv, lower_red1, upper_red1)
    mask2 = cv2.inRange(hsv, lower_red2, upper_red2)
    mask = mask1 + mask2

    red_pixels = cv2.countNonZero(mask)

    if red_pixels > 5000:
        cv2.putText(frame, "RED DETECTED", (30, 50),
                    cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 255), 2)
    else:
        cv2.putText(frame, "NO RED", (30, 50),
                    cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)

    cv2.imshow("Webcam", frame)
    cv2.imshow("Red Mask", mask)

    if cv2.waitKey(1) == 27:
        break

cam.release()
cv2.destroyAllWindows()

```

---

## Results

---
