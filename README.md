# Posture Detector

A real-time computer vision posture detection system built in Python using **MediaPipe, OpenCV, and NumPy**.

The detector analyzes body landmarks from a camera frame and evaluates posture using geometric measurements of the **neck, spine, shoulders, and head**. It produces a posture score along with detailed indicators explaining what is causing poor posture.

## How It Works

The system uses MediaPipe Pose to extract body landmarks from each frame.

These landmarks are then used to calculate:

* **Forward neck position** — compares the nose position with the center of the shoulders.
* **Spine angle** — measures the angle between the shoulder and hip centers.
* **Shoulder lean** — detects uneven shoulder height and determines the leaning direction.
* **Head tilt** — compares the vertical positions of the ears.
* **Posture score** — combines the detected issues into a score from `0–100`.

The detector also applies score smoothing to reduce sudden changes caused by noisy pose detection.

## Posture Analysis

The detector identifies four main posture problems:

| Check           | What it detects                                |
| --------------- | ---------------------------------------------- |
| Neck Forward    | Head/neck positioned too far forward           |
| Spine Alignment | Excessive deviation of the torso from vertical |
| Shoulder Lean   | Uneven shoulder positioning                    |
| Head Tilt       | Excessive difference between ear positions     |

Each frame returns structured information including the posture score, detected issues, measured angles, lean direction, and duration of poor posture.

## Poor Posture Tracking

The detector does more than classify individual frames.

It maintains state between frames to determine how long poor posture has continued.

Features include:

* **7-second grace period** to avoid immediately flagging temporary movements.
* **30-second notification cooldown** to prevent repeated alerts.
* Continuous tracking of bad-posture duration.
* Smoothed posture scoring using an exponential moving average.

This makes the detector more suitable for continuous monitoring rather than treating every frame as an independent prediction.

## Example Output

```python
{
    "posture_score": 72,
    "neck_bad": False,
    "spine_bad": False,
    "leaning": True,
    "leaning_direction": "RIGHT",
    "head_tilt": False,
    "neck_forward_x": 0.018,
    "neck_forward_z": 0.006,
    "spine_angle_val": 8.42,
    "lean_angle_val": 10.31,
    "bad_posture_duration": 0.0,
    "should_notify": False
}
```

## Technologies

* **Python**
* **MediaPipe Pose** — body landmark detection
* **OpenCV** — image decoding, resizing, and color conversion
* **NumPy** — vector and geometric calculations

## Project Structure

```text
Posture-Monitor-Web/
│
├── posture_detector.py
├── requirements.txt
└── README.md
```

The core implementation is contained in `posture_detector.py`, making the detector reusable independently of any specific web framework or frontend.

## Running the Detector

Clone the repository and install the dependencies:

```bash
git clone https://github.com/cezium55/Posture-Monitor-Web.git
cd Posture-Monitor-Web

pip install -r requirements.txt
```

The detector can then be imported and used from another Python application:

```python
from posture_detector import PostureDetector

detector = PostureDetector()

result = detector.analyze_frame(base64_jpeg)

if result:
    print(result["posture_score"])
```

`analyze_frame()` accepts a Base64-encoded JPEG frame, processes it through MediaPipe Pose, and returns the posture analysis result.

## Design Focus

The project focuses on implementing the posture analysis logic rather than relying on a pre-trained posture classification model.

The main goal was to build the detection pipeline around **pose landmarks, geometric calculations, configurable thresholds, temporal smoothing, and state tracking**.

## Limitations

This is a landmark-based posture detector, so accuracy can be affected by:

* Camera angle and placement
* Poor lighting
* Occluded body landmarks
* Extreme body positions
* Partial visibility of the upper body

The thresholds are configurable and can be adjusted depending on the camera setup and desired sensitivity.
