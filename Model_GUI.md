# Model GUI

---

## Overview

- Building GUI of Object detection Model
- Modifying GUI based on personal preference

---

## Features

- Operating various functions through a GUI

---

## Tech Stack

- Python
- Opencv
- CustomTkinter
- Pillow
- PyInstaller

---

## Installation

- Install CustomTkinter in Anaconda Prompt

```bash
pip install CustomTkinter
```

- Install Pillow in Anaconda Prompt

```bash
pip install Pillow
```

- Install PyInstaller in Anaconda Prompt

```bash
pip install Pyinstaller
```

---

## Usage

Run the following code in Spyder:

```Python

import customtkinter as ctk
import tkinter as tk
from tkinter import ttk, filedialog
from PIL import Image, ImageTk
import cv2
from ultralytics import YOLO
from collections import Counter
import datetime
import time


MODEL_PATH = r"(#Write the directory path of 'best.pt' of your detection model)"
CAMERA_INDEX = (#Write the number of camera you use)

model = YOLO(MODEL_PATH)
cap = None
running = False
prev_time = 0


ctk.set_appearance_mode("light")
ctk.set_default_color_theme("blue")


root = ctk.CTk()
root.title("Object Detection GUI")
root.geometry("1350x850")
root.minsize(1200, 750)


# ================= LEFT PANEL =================

left_panel = ctk.CTkFrame(root, width=280, corner_radius=25)
left_panel.pack(side="left", fill="y", padx=20, pady=20)
left_panel.pack_propagate(False)

title_label = ctk.CTkLabel(
    left_panel,
    text="Object Detector",
    font=("Arial", 26, "bold")
)
title_label.pack(pady=(25, 5))

subtitle_label = ctk.CTkLabel(
    left_panel,
    text="YOLO Object Detection",
    font=("Arial", 13),
    text_color="gray"
)
subtitle_label.pack(pady=(0, 25))


# Model section
model_label = ctk.CTkLabel(
    left_panel,
    text="Model Path",
    font=("Arial", 15, "bold")
)
model_label.pack(anchor="w", padx=20, pady=(10, 5))

model_entry = ctk.CTkEntry(
    left_panel,
    height=35,
    corner_radius=12
)
model_entry.insert(0, MODEL_PATH)
model_entry.pack(fill="x", padx=20, pady=5)


def browse_model():
    global model

    file_path = filedialog.askopenfilename(
        filetypes=[("YOLO Model", "*.pt")]
    )

    if file_path:
        model_entry.delete(0, tk.END)
        model_entry.insert(0, file_path)
        model = YOLO(file_path)
        status_label.configure(text="Status: Model loaded")


browse_button = ctk.CTkButton(
    left_panel,
    text="Browse Model",
    corner_radius=18,
    height=38,
    command=browse_model
)
browse_button.pack(fill="x", padx=20, pady=(5, 20))


# Confidence section
conf_label_title = ctk.CTkLabel(
    left_panel,
    text="Confidence",
    font=("Arial", 15, "bold")
)
conf_label_title.pack(anchor="w", padx=20, pady=(5, 5))

conf_value = ctk.DoubleVar(value=0.5)

conf_number_label = ctk.CTkLabel(
    left_panel,
    text="0.50",
    font=("Arial", 18, "bold")
)
conf_number_label.pack(pady=(0, 5))


def update_conf_label(value):
    conf_number_label.configure(text=f"{float(value):.2f}")


conf_slider = ctk.CTkSlider(
    left_panel,
    from_=0.0,
    to=1.0,
    number_of_steps=20,
    variable=conf_value,
    command=update_conf_label
)
conf_slider.pack(fill="x", padx=20, pady=(0, 25))


# Camera section
camera_label_title = ctk.CTkLabel(
    left_panel,
    text="Camera Index",
    font=("Arial", 15, "bold")
)
camera_label_title.pack(anchor="w", padx=20, pady=(5, 5))

camera_entry = ctk.CTkEntry(
    left_panel,
    height=35,
    corner_radius=12
)
camera_entry.insert(0, str(CAMERA_INDEX))
camera_entry.pack(fill="x", padx=20, pady=5)


def start_camera():
    global cap, running, prev_time

    if running:
        return

    camera_index = int(camera_entry.get())
    cap = cv2.VideoCapture(camera_index)

    if not cap.isOpened():
        status_label.configure(text="Status: Camera error")
        return

    running = True
    prev_time = 0
    status_label.configure(text="Status: Running")
    update_frame()


def stop_camera():
    global cap, running

    running = False

    if cap is not None:
        cap.release()
        cap = None

    camera_view.configure(image=None, text="Camera stopped")
    fps_label.configure(text="FPS: 0.0")
    status_label.configure(text="Status: Stopped")


start_button = ctk.CTkButton(
    left_panel,
    text="Start Camera",
    height=42,
    corner_radius=20,
    command=start_camera
)
start_button.pack(fill="x", padx=20, pady=(20, 8))

stop_button = ctk.CTkButton(
    left_panel,
    text="Stop Camera",
    height=42,
    corner_radius=20,
    fg_color="#E74C3C",
    hover_color="#C0392B",
    command=stop_camera
)
stop_button.pack(fill="x", padx=20, pady=8)


def save_screenshot():
    if hasattr(camera_view, "current_frame"):
        now = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
        filename = f"detection_{now}.jpg"
        cv2.imwrite(filename, camera_view.current_frame)
        status_label.configure(text=f"Status: Saved {filename}")


save_button = ctk.CTkButton(
    left_panel,
    text="Save Screenshot",
    height=42,
    corner_radius=20,
    fg_color="#2ECC71",
    hover_color="#27AE60",
    command=save_screenshot
)
save_button.pack(fill="x", padx=20, pady=(8, 20))


# ================= MAIN AREA =================

main_area = ctk.CTkFrame(root, corner_radius=25)
main_area.pack(side="right", fill="both", expand=True, padx=(0, 20), pady=20)

top_bar = ctk.CTkFrame(main_area, height=70, corner_radius=20)
top_bar.pack(fill="x", padx=20, pady=(20, 10))
top_bar.pack_propagate(False)

main_title = ctk.CTkLabel(
    top_bar,
    text="Real-Time Object Detection",
    font=("Arial", 23, "bold")
)
main_title.pack(side="left", padx=25)

fps_label = ctk.CTkLabel(
    top_bar,
    text="FPS: 0.0",
    font=("Arial", 18, "bold"),
    text_color="#0078D7"
)
fps_label.pack(side="right", padx=25)


content_area = ctk.CTkFrame(main_area, corner_radius=20)
content_area.pack(fill="both", expand=True, padx=20, pady=10)


# Camera panel
camera_panel = ctk.CTkFrame(content_area, corner_radius=22)
camera_panel.pack(side="left", fill="both", expand=True, padx=(15, 10), pady=15)

camera_view = ctk.CTkLabel(
    camera_panel,
    text="Camera preview will appear here",
    font=("Arial", 18),
    corner_radius=20
)
camera_view.pack(fill="both", expand=True, padx=15, pady=15)


# Summary panel
summary_panel = ctk.CTkFrame(content_area, width=260, corner_radius=22)
summary_panel.pack(side="right", fill="y", padx=(10, 15), pady=15)
summary_panel.pack_propagate(False)

summary_title = ctk.CTkLabel(
    summary_panel,
    text="Detection Summary",
    font=("Arial", 18, "bold")
)
summary_title.pack(pady=(20, 10))

summary_box = ctk.CTkTextbox(
    summary_panel,
    width=220,
    height=250,
    corner_radius=15,
    font=("Arial", 15)
)
summary_box.pack(padx=20, pady=10)

total_label = ctk.CTkLabel(
    summary_panel,
    text="Total Objects: 0",
    font=("Arial", 18, "bold"),
    text_color="#0078D7"
)
total_label.pack(pady=15)


# ================= RESULT TABLE =================

table_panel = ctk.CTkFrame(main_area, height=220, corner_radius=20)
table_panel.pack(fill="x", padx=20, pady=(10, 20))
table_panel.pack_propagate(False)

table_title = ctk.CTkLabel(
    table_panel,
    text="Detection Log",
    font=("Arial", 18, "bold")
)
table_title.pack(anchor="w", padx=20, pady=(15, 5))

style = ttk.Style()
style.theme_use("default")
style.configure(
    "Treeview",
    rowheight=28,
    font=("Arial", 11)
)
style.configure(
    "Treeview.Heading",
    font=("Arial", 12, "bold")
)

columns = ("ID", "Object", "Confidence", "Coordinates")
tree = ttk.Treeview(
    table_panel,
    columns=columns,
    show="headings",
    height=5
)

for col in columns:
    tree.heading(col, text=col)

tree.column("ID", width=60, anchor="center")
tree.column("Object", width=180, anchor="center")
tree.column("Confidence", width=120, anchor="center")
tree.column("Coordinates", width=350, anchor="center")

tree.pack(fill="both", expand=True, padx=20, pady=(0, 15))


# ================= STATUS BAR =================

status_label = ctk.CTkLabel(
    root,
    text="Status: Ready",
    anchor="w",
    font=("Arial", 12),
    text_color="gray"
)
status_label.place(x=25, y=820)


# ================= FRAME UPDATE =================

def update_frame():
    global cap, running, prev_time

    if not running or cap is None:
        return

    ret, frame = cap.read()

    if ret:
        current_time = time.time()

        if prev_time == 0:
            fps = 0
        else:
            fps = 1 / (current_time - prev_time)

        prev_time = current_time
        fps_label.configure(text=f"FPS: {fps:.1f}")

        confidence = conf_value.get()

        results = model(frame, conf=confidence)
        result = results[0]

        annotated_frame = result.plot()

        cv2.putText(
            annotated_frame,
            f"FPS: {fps:.1f}",
            (20, 45),
            cv2.FONT_HERSHEY_SIMPLEX,
            1.2,
            (0, 255, 0),
            3
        )

        camera_view.current_frame = annotated_frame.copy()

        for item in tree.get_children():
            tree.delete(item)

        detected_names = []
        boxes = result.boxes

        if boxes is not None:
            for idx, box in enumerate(boxes, start=1):
                cls_id = int(box.cls[0])
                class_name = model.names[cls_id]
                conf = float(box.conf[0])

                x1, y1, x2, y2 = box.xyxy[0]
                coord = f"({int(x1)}, {int(y1)}, {int(x2)}, {int(y2)})"

                detected_names.append(class_name)

                tree.insert(
                    "",
                    "end",
                    values=(idx, class_name, f"{conf:.2f}", coord)
                )

        count_result = Counter(detected_names)

        summary_box.delete("1.0", tk.END)

        total = 0
        if count_result:
            for name, count in count_result.items():
                summary_box.insert(tk.END, f"{name}: {count}\n")
                total += count
        else:
            summary_box.insert(tk.END, "No object detected")

        total_label.configure(text=f"Total Objects: {total}")

        annotated_frame = cv2.cvtColor(annotated_frame, cv2.COLOR_BGR2RGB)
        img = Image.fromarray(annotated_frame)

        img = img.resize((850, 520))

        imgtk = ImageTk.PhotoImage(image=img)

        camera_view.imgtk = imgtk
        camera_view.configure(image=imgtk, text="")

    root.after(10, update_frame)


def on_closing():
    stop_camera()
    root.destroy()


root.protocol("WM_DELETE_WINDOW", on_closing)
root.mainloop()

```

Insert a code of your own to the appropriate section of GUI in order to add a function you would like to.

## Expansion

To operate GUI outside Python workspace,

Create a separate environment just in case unnecessary softwares don't conflict and cause error.

```bash
conda create -n yolo_gui python=3.11 -y
```

Activate the environment and install only the softwares required to operate GUI. 

```bash
conda activate yolo_gui
```

```bash
pip install ultralytics opencv-python pillow customtkinter pyinstaller
```

Create a new folder which includes the code of GUI and 'best.pt' of your detection model.

```bash
Object_Detection_Program
├─ Object_Detection_model.py
└─ best.pt
```

Move to the new folder.

```bash
cd (#Write the directory path of the new folder)
```

Create exe file using the following code in Anaconda Prompt:

```bash
pyinstaller --onefile --windowed --collect-all ultralytics Object_Detection_Program.py
```

To execute the program successfully, move 'best.pt' to 'dist' folder which is made after the previous step.

```bash
dist
├─ Object_Detection_Program.exe
└─ best.pt
```

---

## Results

<img width="1918" height="1002" alt="image" src="https://github.com/user-attachments/assets/227dbfe9-7538-4de2-adf8-b53f5ce70461" />

---
