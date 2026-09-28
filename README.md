# Gaze Tracking Mouse

Sunwoo Kim and I built this webcam-based mouse interface for our 2025 image-processing course. It estimates where the user is looking, converts the estimate into screen coordinates and moves the pointer. Holding the gaze and blinking provides a click interaction.

We worked together on data collection, the model and the real-time interface. The available records do not separate every module by author, so this README describes our joint work.

[Watch the team demonstration](https://www.youtube.com/watch?v=VR9T6X-zanU).

## Implementation

[main.py](main.py) connects webcam input, gaze prediction, calibration and pointer control. The model is defined in [fginet.py](fginet.py), with preprocessing and calibration helpers in [gaze_utils.py](gaze_utils.py).

Calibration maps the model's output to the user's screen. This is needed because the camera position, screen size and user's position affect the predicted coordinates.

## How the interface works

The webcam supplies frames from which the program extracts eye regions. The model predicts a two-dimensional gaze position, and a calibration mapping converts that prediction to coordinates on the user's screen. Smoothing reduces visible pointer jitter. When the gaze stays within a small region, the interface can hold the pointer before checking a sequence of closed-eye observations for a click.

Calibration is a separate step from training. Looking at several known screen targets gives pairs of predicted and actual positions; the program uses those pairs to fit a linear correction for the current setup. It collects stable recent predictions rather than relying on one frame. Moving the camera or changing the user's position can change that relationship.

## Data, model and my contribution

We collected eye images while looking at targets with known screen coordinates, stored the image paths and coordinates, trained the gaze model and connected it to the live interface. I was involved across these stages with my teammate. Our model was informed by an FGI-Net paper, with modifications described in the project report; it is not claimed as an exact reproduction of the published architecture.

One complication is that ordinary image augmentation is not automatically valid for gaze estimation. Flipping or rotating an eye image can change the meaning of its direction label. Another is that a low coordinate error alone does not make a comfortable mouse interface: calibration, smoothing and click behaviour must also work together.

## Results

MAE is the mean absolute error in screen-coordinate pixels; FPS is the number of frames processed per second. The original report recorded a validation MAE of 66.77 px, a test MAE of 67.71 px and approximately 22–24 FPS. The split was made at image level, so nearby frames may have appeared in different splits. These figures are internal results from the course project, not an independent-user benchmark. We have not repeated training or measurement for this portfolio copy.

## Reading the implementation

Start with [main.py](main.py) to follow the live frame loop and pointer interaction. Next, read [gaze_utils.py](gaze_utils.py) for the calibration and preprocessing steps, then [fginet.py](fginet.py) for the network. This order shows what the interface needs from the model before going into the model's internal structure.

To inspect the project, no webcam is needed. To reproduce the live demo, first prepare a compatible environment and model checkpoint, then calibrate for the user and screen. Merely opening the repository or installing the listed environment does not supply a trained model or a valid calibration.

## Running

The recorded environment is in [environment.yml](environment.yml). The live program needs compatible model weights, a webcam and user calibration. It also controls the mouse pointer. The weights and personal calibration data are not supplied here, so the repository cannot run the full demo immediately after cloning.

The Python source was checked for syntax without opening the camera or controlling the pointer. The original report and source history retain the joint authorship.

[Original repository](https://github.com/oldprize47/DLIP_FinalProject2025_GazeMouse). Original history and attribution are retained.
