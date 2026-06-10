# Object Detection Program

---

## Overview

- Collecting image datas and labeling them
- Training a object detection model
- Testing if the model distinguishs the objects well

---

## Features

- Object detection model detects the objects through webcam
- Draw boxes around each object and indicate their name

---

## Tech Stack

- Label Studio
- YOLO
- Python
- Opencv

---

## Installation

- Install Label Studio in Anaconda Prompt

  ```bash
  pip install label-studio
  ```

- Install YOLO in Anaconda Prompt

  ```bash
  pip install ultralytics
  ```

---

## Step-by-Step Development Process

### Data Collection

Take images of the objects that you want your detection model to distinguish.

It would work better if you took the images with the same camera that you will use for your detection model.

It is recommended to take images as many as possible for your detection model to work stably.

Include some images that feature all the objects.

The objects that I used and the number of images for each objects were
- Spacula -> 11
- Petri dish -> 11
- Weighing dish -> 11
- Mixed -> 11

Run the following capture code in Spyder to take images:

```Python

import cv2
import os

save_dir = (#Write the directory path where you want to save your images)

os.makedirs(save_dir, exist_ok=True)

cap = cv2.VideoCapture(0)

count = 0

while True:

    ret, frame = cap.read()

    cv2.imshow("Webcam", frame)

    key = cv2.waitKey(1)

    if key == ord("s"):

        filename = os.path.join(
            save_dir,
            f"image_{count}.jpg"
        )

        cv2.imwrite(filename, frame)

        print("Saved:", filename)

        count += 1

    elif key == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()

```

### Data Labeling

In Label Studio, create a new project and import the images of the objects. 

Choose 'Object detection with bounding boxes' in labeling setup and add labels of the objects that you use.

Start labeling by drawing bounding boxes around each objects in the images.

If you are done with the labeling process, export the datasets with 'YOLO with images'.

Create a new folder, make the following structure to optimize your dataset for YOLO,
and move the files of images and labels that was exported to the appropriate folder:

```bash

YOLO_model(folder)
 │
 ├── data.yaml
 │
 ├── images(folder)
 │    ├── train(folder)
 │    │     ├── image_0.jpg
 │    │     ├── image_1.jpg
 │    │     └── ...
 │    │
 │    └── val(folder)
 │          ├── image_10.jpg
 │          ├── image_11.jpg
 │          └── ...
 │
 └── labels(folder)
          ├── train(folder)
          │     ├── image_0.txt
          │     ├── image_1.txt
          │     └── ...
          │
          └── val(folder)
                ├── image_10.txt
                ├── image_11.txt
                └── ...

```

images and labels in 'train' folder is used for trainng the model
and images and labels in 'val(idation)' folder is used to test whether the model was well trained or not.
For the ratio of the images in each folder, 8:2 ~ 9:1 (train:val) is recommended.

'data.yaml' is a file which YOLO program refer to identify the names for each objects and which images are used for each train and val procedure.
Inside the file, It should be organized in the following structure:

```bash

path: (#Write the directory path of the folder)

train: images/train
val: images/val

names:
  0: petri_dish
  1: spatula
  2: weighing_dish

```

### Model Training

Train your model running the following code in Spyder:

```Python

from ultralytics import YOLO

model = YOLO("yolov8n.pt")

model.train(
    data=r"(#Write the path of 'data.yaml' file)",
    epochs=50,
    imgsz=640,
    batch=8,
    name="Objects_train"
)

```

'epochs' represents the number of times the model is trained on the entire dataset.
The bigger the number is, the more accurate the model becomes and the longer it takes to train your model.

### Testing

Run the following code in Spyder:

```Python

from ultralytics import YOLO

model = YOLO(
r"(#Write the directory path where the model is saved and add '\weights\best.pt' at the end)"
)

model.predict(
    source=(#Write the camera number you use),
    show=True,
    conf=0.5
)

```

'conf(idence)' determines whether a bounding box should be displayed based on similarity between the detected object and the object which the model was trained of.

---

## Results

<img width="954" height="688" alt="스크린샷 2026-06-10 150116" src="https://github.com/user-attachments/assets/1b712bc5-bd1e-4b27-affd-7d85b8f13ffa" />

---

## Problems & Solutions

### Problem 1 : Bounding boxes did not pop out on the screen

#### Cause

The filenames of images and labels did not match.

#### Solution

Change the filenames of images and labels to match each other and train the model again.

### Problem 2 : Objects and their displayed names did not match

#### Cause

The class number that I assigned to each object in 'data.yaml' and the number that Label Studio assigned to each label when it was exported did not match.

#### Solution

Find out the correct class number for each object, change it in 'data.yaml', and train the model again.
