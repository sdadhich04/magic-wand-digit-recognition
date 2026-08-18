# TinyML Magic Wand Digit Recognition — Arduino Nano 33 BLE Sense

**EE 446: Tiny Machine Learning for Ultra Low-Power Edge Computing | University of Washington, Spring 2026**

Recognizing air-drawn digits (0–9) from IMU motion traces, fine-tuned on custom collected data and deployed on an Arduino Nano 33 BLE Sense. Also includes a pre-built transport mode classification firmware from Edge Impulse.

---

## What it does

The system captures IMU traces of digits drawn in the air with the board and classifies them as one of 10 digits (0–9). A baseline model is fine-tuned on newly collected data to improve accuracy on the user's personal drawing style.

---

## Repository contents

```
EE446_TinyML_Lab7_(Magic_Dataset_Training).ipynb   ← Full pipeline: load data → train → quantize → fine-tune
TinyML-Lab#7.pdf                                   ← Lab instructions
Lab-7-Data-Collection-Instruction.pdf              ← How to collect your own IMU traces
model.png                                          ← Model architecture diagram
wanddata.json                                      ← Collected wand trace data (212 KB)
data/
  magic_wand_digit_data/                           ← JSON IMU recordings for digits 0–9
  finetune/                                        ← Augmented PNG traces for fine-tuning (per-person)
models/
  float_model.tfl                                  ← Float32 TFLite model (47 KB)
  quantized_model.tfl                              ← Int8 quantized baseline model (16 KB)
  finetuned_quantized_model.tfl                    ← Int8 fine-tuned model (16 KB)
  saved_model.keras                                ← Keras model (168 KB)
  magic_wand_model_data.cc                         ← Baseline model as C array for Arduino (95 KB)
  magic_wand_finetuned_model_data.cc               ← Fine-tuned model as C array (96 KB)
arduino/
  magic_wand_capture/                              ← Sketch for capturing raw IMU data via Serial
  magic_wand_lab7/                                 ← Main sketch: loads baseline model, runs inference
  magic_wand_baseline_int8/                        ← Baseline int8 model sketch
  magic_wand_fine_tuned_int8/                      ← Fine-tuned int8 model sketch (best accuracy)
screenshots/
  pre_fine_tuning/                                 ← Model performance before fine-tuning
  post_fine_tuning/                                ← Model performance after fine-tuning
transport/                                         ← Pre-built Edge Impulse transport mode firmware
```

---

## Quick start

### Run the notebook (train / fine-tune)

```bash
pip install numpy tensorflow matplotlib scikit-learn
jupyter notebook "EE446_TinyML_Lab7_(Magic Dataset Training).ipynb"
```

The notebook loads `data/magic_wand_digit_data/` (baseline training data), trains and quantizes a model, then fine-tunes on `data/finetune/` (custom collected traces). Outputs are saved to `models/`.

### Flash the Arduino sketch

**To collect your own training data:**
1. Open `arduino/magic_wand_capture/magic_wand_capture.ino` in Arduino IDE
2. Upload to Nano 33 BLE Sense, open Serial Monitor at 9600 baud
3. Draw digits in the air — traces are printed as JSON for export

**To run inference:**
1. Open `arduino/magic_wand_fine_tuned_int8/` in Arduino IDE (best accuracy)
   — or `arduino/magic_wand_lab7/` for the float baseline
2. Upload to Nano 33 BLE Sense, open Serial Monitor at 9600 baud
3. Draw a digit in the air — predicted class prints after each gesture

**Arduino library required:** `TensorFlowLite` (install via Arduino Library Manager)

### Flash transport classification firmware

Pre-built Edge Impulse firmware in `transport/` — classifies transport modes (walking, running, cycling, etc.):

```bash
# Windows
transport\flash_windows.bat

# Mac
transport/flash_mac.command

# Linux
bash transport/flash_linux.sh
```

---

## Hardware

- **Arduino Nano 33 BLE Sense** (Nordic nRF52840, 256 KB flash, 64 KB RAM, onboard IMU)

---

## Authors

Sparsh Dadhich — University of Washington, ECE / Neuroscience
