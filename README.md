# Gaze Tracking Mouse

Sunwoo Kim and I built this webcam-based mouse interface for our 2025 image-processing course. It estimates where the user is looking, converts the estimate into screen coordinates and moves the pointer. Holding the gaze and blinking provides a click interaction.

We worked together on data collection, the model and the real-time interface. The available records do not separate every module by author, so this README describes our joint work.

[Watch the team demonstration](https://www.youtube.com/watch?v=VR9T6X-zanU).

## Implementation

[main.py](main.py) connects webcam input, gaze prediction, calibration and pointer control. The model is defined in [fginet.py](fginet.py), with preprocessing and calibration helpers in [gaze_utils.py](gaze_utils.py).

Calibration maps the model's output to the user's screen. This is needed because the camera position, screen size and user's position affect the predicted coordinates.

## Results

The original report recorded a validation MAE of 66.77 px, a test MAE of 67.71 px and approximately 22–24 FPS. The split was made at image level, so nearby frames may have appeared in different splits. These figures are internal results from the course project, not an independent-user benchmark. We have not repeated training or measurement for this portfolio copy.

## Running

The recorded environment is in [environment.yml](environment.yml). The live program needs compatible model weights, a webcam and user calibration. It also controls the mouse pointer. The weights and personal calibration data are not supplied here, so the repository cannot run the full demo immediately after cloning.

The Python source was checked for syntax without opening the camera or controlling the pointer. The original report and source history retain the joint authorship.

[Original repository](https://github.com/oldprize47/DLIP_FinalProject2025_GazeMouse). Original history and attribution are retained.
