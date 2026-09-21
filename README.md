# Seren

## Real-Time GSR-Based Physiological Stress Monitoring System

Seren is a real-time physiological monitoring system that combines **Galvanic Skin Response (GSR) sensing, signal processing, machine learning, and interactive visualization** to estimate stress-related physiological activity.

The system acquires GSR signals through an Arduino-connected sensor, streams the measurements to a Flutter Web application through a local Node.js communication layer, processes the signal, extracts statistical features, and sends the resulting feature vector to a machine learning inference service.

The project is designed as an experimental and educational platform for exploring the relationship between electrodermal activity and stress-related physiological responses.

> **Research note:** Seren is a physiological signal analysis prototype and is not a medical diagnostic or clinical assessment device. GSR reflects electrodermal activity associated with sympathetic nervous system activation and may be influenced by factors other than psychological stress.

---

## System Overview

Seren follows an end-to-end physiological signal processing pipeline:

```text
GSR Sensor
     │
     ▼
 Arduino Microcontroller
     │
     │ USB Serial
     ▼
 Node.js Communication Layer
     │
     │ WebSocket
     ▼
 Flutter Web Application
     │
     ├── Real-Time Visualization
     ├── Signal Filtering
     ├── Normalization
     └── Feature Extraction
             │
             ▼
      Machine Learning API
             │
             ▼
      Physiological State
             │
             ▼
       Session Analysis
             │
             ▼
          Firebase
```

The architecture separates **signal acquisition, communication, signal processing, machine learning inference, and visualization**, allowing individual components to be developed and evaluated independently.

---

## Objectives

The primary objectives of Seren are:

* Acquire GSR signals from a low-cost physiological sensor.
* Stream physiological measurements in real time.
* Reduce measurement noise using signal-processing techniques.
* Extract meaningful statistical characteristics from GSR signals.
* Apply a machine learning model for physiological state classification.
* Provide real-time signal visualization.
* Store and review previous measurement sessions.
* Establish a modular foundation for future multi-sensor physiological analysis.

---

## Key Features

### Real-Time GSR Acquisition

GSR measurements are acquired through an Arduino-connected sensor and transmitted to the software system through USB serial communication.

### Real-Time Signal Streaming

A Node.js server receives the serial data and broadcasts measurements to connected clients using WebSockets.

### Signal Processing

The acquired signal undergoes preprocessing before classification, including:

* Kalman filtering
* Signal normalization
* Statistical feature extraction

### Feature Extraction

The current processing pipeline derives features including:

* Mean
* Standard deviation
* Variance
* Peak-related characteristics
* Signal slope/trend

These features form the input representation used by the machine learning inference pipeline.

### Machine Learning Inference

The processed GSR feature vector is submitted to a machine learning inference service for physiological-state classification.

The current implementation uses a binary classification stage representing:

```text
Relaxed
Stressed
```

The application can subsequently represent the detected physiological response using multiple intensity levels for visualization and interpretation.

### Four-Class Stress Classification

The system classifies the processed GSR signal into four physiological stress-intensity categories:

| Class         | Description                        |
| ------------- | ---------------------------------- |
| **Relaxed**   | Low observed physiological arousal |
| **Medium**    | Moderate physiological arousal     |
| **High**      | Elevated physiological arousal     |
| **Very High** | Strong physiological arousal       |

The classification pipeline is:

```text
Processed GSR Window
        │
        ▼
 Feature Extraction
        │
        ▼
 Feature Vector
        │
        ▼
 Machine Learning Classifier
        │
        ├── Relaxed
        ├── Medium
        ├── High
        └── Very High
```

The four classes are intended to represent different levels of GSR-associated physiological arousal. They should not be interpreted as clinical diagnoses or definitive measurements of psychological stress.

Model performance should be evaluated separately for each class using metrics such as precision, recall, F1-score, and a confusion matrix.


### Interactive Visualization

The Flutter Web interface provides graphical visualization of the acquired and processed GSR signal, allowing signal behavior to be inspected during and after acquisition.

### Session-Based Analysis

Measurement sessions can be stored and reviewed, allowing previous recordings and their associated analysis to be examined.

---

## Signal Processing Pipeline

The signal-processing pipeline can be summarized as:

```text
Raw GSR
   │
   ▼
Noise Reduction
(Kalman Filter)
   │
   ▼
Normalization
   │
   ▼
Feature Extraction
   │
   ├── Mean
   ├── Standard Deviation
   ├── Variance
   ├── Peak Characteristics
   └── Slope / Trend
   │
   ▼
Feature Vector
   │
   ▼
Machine Learning Model
   │
   ▼
Physiological State
```

### Kalman Filtering

Physiological measurements can contain sensor noise and short-term fluctuations caused by measurement conditions and movement.

A Kalman filter is used to smooth the acquired signal while preserving its overall temporal behavior.

### Normalization

The signal is normalized before feature analysis to provide a consistent numerical representation for downstream processing.

The current implementation uses min-max normalization:

```text
x_normalized = (x - x_min) / (x_max - x_min)
```

The normalization strategy is intended to reduce scale differences between signal windows.

---

## Machine Learning

The machine learning component operates on statistical characteristics extracted from processed GSR windows rather than directly using the complete raw signal.

### Current Classification Pipeline

```text
Processed GSR Window
        │
        ▼
 Feature Extraction
        │
        ▼
 Feature Vector
        │
        ▼
 ML Classifier
        │
        ├──────────────┐
        ▼              ▼
    Relaxed         Stressed
```

The application layer can further interpret the physiological response using intensity categories for user-facing visualization.

This distinction is intentional: the current ML stage performs the physiological-state classification, while the application layer provides a more granular interpretation for monitoring purposes.

---

## Hardware

### Required Components

| Component                          | Purpose                                      |
| ---------------------------------- | -------------------------------------------- |
| Arduino-compatible microcontroller | Sensor acquisition and ADC conversion        |
| GSR sensor                         | Electrodermal activity measurement           |
| Skin-contact electrodes            | Electrical contact with the subject          |
| USB cable                          | Arduino-to-computer communication            |
| Computer                           | Runs the application and processing services |

### Hardware Data Flow

```text
Skin
 │
 ▼
GSR Electrodes
 │
 ▼
GSR Sensor
 │
 ▼
Arduino ADC
 │
 ▼
USB Serial
 │
 ▼
Node.js
```

GSR measures changes in the electrical conductance of the skin. These changes are influenced by sweat gland activity and sympathetic nervous system activation.

Because electrodermal activity is affected by multiple physiological and environmental factors, GSR should be interpreted as a measure of physiological arousal rather than as a direct measurement of psychological stress.

---

## Software Architecture

Seren consists of several software layers.

### 1. Firmware / Hardware Layer

The Arduino acquires the analog GSR signal and transmits measurements through the serial interface.

### 2. Communication Layer

Node.js acts as the bridge between the Arduino and the Flutter application.

Responsibilities include:

* Serial-port communication
* Parsing incoming sensor measurements
* Maintaining the latest measurement
* WebSocket communication
* Broadcasting measurements to connected clients

### 3. Application Layer

Flutter Web provides:

* Real-time monitoring
* Signal visualization
* Session management
* Analysis interfaces
* Communication with the inference service

### 4. Machine Learning Layer

The ML service receives processed signal features and returns the predicted physiological state.

### 5. Storage Layer

Firebase is used for session-related data storage in the application workflow.

---

## Technology Stack

### Frontend

* Flutter
* Dart
* Flutter Web
* Interactive charting

### Backend / Communication

* Node.js
* Express
* WebSocket
* SerialPort

### Machine Learning

* Python
* Machine learning classification
* Statistical feature extraction
* Signal preprocessing

### Data Storage

* Firebase
* Cloud Firestore

### Hardware

* Arduino
* GSR sensor
* Skin-contact electrodes

---

## Project Structure

The repository contains the Flutter application together with the local Node.js communication layer.

```text
seren_stress_app/
│
├── android/                 # Android platform configuration
├── ios/                     # iOS platform configuration
├── linux/                   # Linux platform configuration
├── macos/                   # macOS platform configuration
├── windows/                 # Windows platform configuration
├── web/                     # Flutter Web configuration
│
├── lib/
│   ├── pages/               # Application screens
│   ├── models/              # Application data models
│   ├── utils/               # Utility and processing logic
│   └── main.dart            # Application entry point
│
├── test/                    # Flutter tests
├── Assets/                  # Application assets
│
├── server.js                # Arduino-to-WebSocket communication server
├── package.json             # Node.js dependencies
├── pubspec.yaml             # Flutter dependencies
├── firebase.json            # Firebase configuration
├── analysis_options.yaml    # Dart analysis configuration
└── README.md
```

The machine learning inference component is maintained separately from the Flutter application and is accessed through an API.

---

## Installation

### Prerequisites

Install the following before running Seren:

* Flutter SDK
* Dart SDK
* Node.js
* Arduino IDE
* Chrome or another supported browser
* A compatible GSR sensor
* Arduino-compatible microcontroller

Verify Flutter installation:

```bash
flutter doctor
```

Verify Node.js installation:

```bash
node --version
npm --version
```

---

## Clone the Repository

```bash
git clone https://github.com/Vishnu-V-Dev/seren_stress_app.git
cd seren_stress_app
```

---

## Install Flutter Dependencies

```bash
flutter pub get
```

---

## Install Node.js Dependencies

```bash
npm install
```

---

## Arduino Setup

1. Connect the GSR sensor to the Arduino.
2. Connect the Arduino to the computer using USB.
3. Upload the sensor acquisition firmware.
4. Identify the serial port assigned to the Arduino.
5. Configure the Node.js communication server with the correct serial port.
6. Ensure the Arduino transmits one sensor measurement per line.

Example serial stream:

```text
512
518
523
519
527
```

The exact sensor conversion and calibration procedure depends on the GSR hardware being used.

---

## Start the Communication Server

From the project directory:

```bash
node server.js
```

The server establishes a serial connection with the Arduino and exposes the measurements to connected WebSocket clients.

The default local server configuration is:

```text
http://localhost:5000
```

The Arduino serial port must be configured according to the host system.

---

## Run the Flutter Application

Run the web application using:

```bash
flutter run -d chrome
```

The application will launch in Chrome.

---

## Machine Learning Service

The Flutter application communicates with a separate machine learning inference service.

The inference pipeline is conceptually:

```text
Flutter
   │
   │ HTTP
   ▼
ML Inference API
   │
   ▼
Preprocessing
   │
   ▼
Feature Vector
   │
   ▼
Trained Classifier
   │
   ▼
Prediction
   │
   ▼
Flutter
```

The inference service must be running and accessible from the environment where the Flutter application is executed.

Configuration such as API endpoints should be provided through environment-specific configuration rather than hard-coded production values.

---

## Experimental Protocol

For a consistent measurement session, the following conditions are recommended:

1. Place the electrodes consistently on the same measurement locations.
2. Keep the subject relatively still during acquisition.
3. Allow sufficient time for the sensor signal to stabilize.
4. Record a fixed-duration signal window.
5. Avoid changing electrode placement between sessions.
6. Maintain consistent sampling conditions when comparing sessions.

Signal quality can be affected by:

* Electrode placement
* Skin moisture
* Temperature
* Movement
* Contact pressure
* Sensor characteristics
* Individual physiological differences

Therefore, experimental consistency is important when evaluating the system.

---

## Limitations

Seren is an experimental physiological monitoring system and has several important limitations.

### GSR Is Not a Direct Stress Measurement

GSR reflects electrodermal activity and sympathetic activation. Increased conductance can occur due to stress, excitement, physical activity, temperature, movement, or other forms of physiological arousal.

Therefore, a GSR-based prediction should not be interpreted as a definitive psychological assessment.

### Subject Variability

Baseline skin conductance varies considerably between individuals. A model trained on one population may not generalize reliably to another population without appropriate validation.

### Motion Artifacts

Movement and changes in electrode contact can introduce artifacts into the signal.

### Sensor Limitations

Low-cost GSR sensors may have limited precision and stability compared with research-grade physiological acquisition equipment.

### Model Generalization

Machine learning performance depends on the quality, size, diversity, and labeling methodology of the training dataset.

Consequently, model performance should be evaluated using appropriate subject-independent validation before making claims about generalization.

---

## Validation and Evaluation

Future experimental evaluation should include:

* Accuracy
* Precision
* Recall
* F1-score
* Confusion matrix
* True Positive / True Negative analysis
* False Positive / False Negative analysis
* Subject-independent validation
* Cross-session validation

Where possible, training and evaluation data should be separated by subject rather than randomly splitting samples from the same subject across both sets. This helps reduce the risk of subject-specific information appearing in both training and testing data.

---

## Research Considerations

For more rigorous physiological stress research, Seren can be extended beyond single-sensor GSR analysis.

Potential additional physiological signals include:

* Heart rate
* Heart-rate variability
* ECG
* PPG
* Skin temperature
* Accelerometer data

Multimodal physiological sensing can help distinguish stress-related responses from other causes of increased electrodermal activity.

---

## Future Development

Planned and potential improvements include:

### Personalized Baseline Calibration

Establish an individual baseline before classification to account for differences in resting skin conductance.

### Multimodal Physiological Sensing

Integrate GSR with:

* ECG
* PPG
* Heart-rate variability
* Temperature
* Motion sensors

### Improved Machine Learning

Evaluate alternative models and compare their performance under subject-independent validation.

Potential approaches include:

* Random Forest
* Support Vector Machine
* Gradient Boosting
* Neural Networks
* Temporal models

### Edge Inference

Move inference closer to the sensing device to reduce dependence on a remote or local server.

### Wearable Integration

Replace the current Arduino-based acquisition setup with a wearable platform capable of continuous physiological monitoring.

### Improved Experimental Validation

Expand the dataset across subjects, sessions, and controlled experimental conditions to evaluate robustness and generalization.

---

## Ethical and Safety Considerations

Seren is intended for research, experimentation, and educational purposes.

It should not be used to:

* Diagnose medical conditions
* Replace professional psychological assessment
* Make medical decisions
* Determine a person's mental-health status
* Make high-stakes decisions about an individual

Physiological measurements should be collected with appropriate informed consent and handled according to applicable privacy and research requirements.

---

## Project Status

**Status:** Experimental / Academic Prototype

The current system demonstrates the complete pipeline from physiological signal acquisition to real-time visualization and machine learning inference.

The architecture is intended to serve as a foundation for further experimentation with signal processing, machine learning, wearable sensing, and multimodal physiological analysis.

---

## References and Background

The project is based on concepts from electrodermal activity measurement, physiological signal processing, and machine learning-based stress recognition.

Related open-source work demonstrates the use of GSR and other physiological signals for signal acquisition, feature extraction, and stress-state classification.

For GSR hardware and electrodermal activity measurement concepts, see the documentation and open-source resources provided by ProtoCentral.

---


## License

This project is intended for academic and experimental use.

Add an explicit open-source license to the repository if redistribution, modification, or reuse is intended.
