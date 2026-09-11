# Magic Wand Digit Recognition

EE 446 lab work for recognizing air-drawn digits on an Arduino Nano 33 BLE Sense. The Arduino sketches collect IMU motion, derive a stroke, rasterize it to a 32 x 32 x 3 int8 input, and run a TensorFlow Lite Micro classifier for labels `0` through `9`.

## Hardware and tools

- Arduino Nano 33 BLE Sense
- On-board IMU, accessed through `Arduino_BMI270_BMM150`
- Bluetooth Low Energy, accessed through `ArduinoBLE`
- Arduino IDE and the `TensorFlowLite` Arduino library
- Python/Jupyter notebook workflow using TensorFlow/Keras, NumPy, Pillow, Matplotlib, scikit-learn, and pandas

## Model

The notebook defines a CNN with a rescaling layer, three ReLU convolution layers (8, 16, and 32 filters; 3 x 3 kernels; stride 2), flattening, and a 10-unit softmax output. The deployed sketches use a 30 KiB tensor arena and expect a `1 x 32 x 32 x 3` int8 input and 10 int8 outputs.

- Baseline int8 model: [`models/quantized_model.tfl`](models/quantized_model.tfl), 16,032 bytes.
- Fine-tuned int8 model: [`models/finetuned_quantized_model.tfl`](models/finetuned_quantized_model.tfl), 16,272 bytes.
- The corresponding C arrays are included with the baseline and fine-tuned Arduino sketch folders.

## Run on the board

1. In Arduino IDE, install the libraries named above and connect the board.
2. Open either [`arduino/magic_wand_baseline_int8/magic_wand_baseline_int8.ino`](arduino/magic_wand_baseline_int8/magic_wand_baseline_int8.ino) or [`arduino/magic_wand_fine_tuned_int8/magic_wand_fine_tuned_int8.ino`](arduino/magic_wand_fine_tuned_int8/magic_wand_fine_tuned_int8.ino), then upload it. Each folder includes its model array and helper sources.
3. Open the Serial Monitor at 9600 baud. After a completed gesture, the sketch prints the rasterized stroke and its highest-scoring digit label.

For BLE capture, upload [`arduino/magic_wand_capture/magic_wand_capture.ino`](arduino/magic_wand_capture/magic_wand_capture.ino). It exposes the stroke data over BLE; its source comments direct the user to the Arduino BLE Sense dashboard in Chrome.

## Notebook workflow

Open [`EE446_TinyML_Lab7_(Magic_Dataset_Training).ipynb`](EE446_TinyML_Lab7_(Magic_Dataset_Training).ipynb) in Jupyter to inspect or run the training, int8 conversion, and fine-tuning cells.

The notebook is not runnable from this checkout unchanged: its early cells require `magic_wand_digit_data.zip` at the notebook root, while this repository tracks the extracted JSON files under `data/magic_wand_digit_data/`. It also generates and replaces root-level `train`, `validation`, `test`, `finetune`, and `checkpoints` directories. Provide the expected archive or adjust the data path before running those cells.

## Credits

Repository copyright is Sparsh Dadhich, with an MIT license in [`LICENSE`](LICENSE). The Arduino Magic Wand helper source retains TensorFlow Authors copyright and Apache-2.0 notices; the capture sketch credits D. Pajak for the web dashboard and Sandeep Mistry for the sketch basis.
