# Connecting Arduino

---

## Overview

- Installing software required to control arduino
- Testing a simple code to check the connection between arduino and computer

---

## Features

- Led repeating a progress being turned on for 3 second and off for a second

---

## Tech Stack

- Arduino
- Python
- Pyserial

---

## Installation

- Install Anaconda Navigator
- Install Spyder in Anaconda Navigator
- Install Pyserial in Anaconda Prompt
  
  ```bash
  pip install pyserial
  ```
  
- Install Arduino IDE
- Connect the Arduino board via USB
- Configure the correct COM port

---

## Usage

Upload a basic code in Arduino IDE:

```C++

void setup() {
  Serial.begin(9600);
  pinMode(13, OUTPUT);
}

void loop() {
  if (Serial.available()) {

    char c = Serial.read();

    if (c == '1') {
      digitalWrite(13, HIGH);
    }

    if (c == '0') {
      digitalWrite(13, LOW);
    }
  }
}

```

Run a test code in Spyder:

```Python

import serial
import time

ser = serial.Serial('COM7', 9600)
time.sleep(2)

ser.write(b'1')
print("LED ON")
time.sleep(3)

ser.write(b'0')
print("LED OFF")

ser.close()

```

---

## Results

---

## Problems & Solutions

### Problem : Result of the test code not showing up on Arduino

#### Cause

The test code and the default code of Arduino overlapped and caused error.

#### Solution

Upload a setup code in Arduino Ide:

``` C++

void setup() {
  Serial.begin(9600);
  pinMode(13, OUTPUT);
  digitalWrite(13, LOW);
}

```

#### Result

Led repeated the loop successfully.

---
