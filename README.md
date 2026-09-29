# Gaze Tracking Mouse

Gaze Tracking Mouse is a webcam-based pointer interface developed for a 2025 image-processing course. It estimates where a user is looking, calibrates the estimate to the screen and moves the mouse pointer. A gaze-hold and blink interaction provides clicking without dedicated eye-tracking hardware.

[Watch the team demonstration](https://www.youtube.com/watch?v=VR9T6X-zanU).

## Project goal

Use a normal webcam to move and click the mouse pointer with eye gaze, without dedicated eye-tracking hardware.

![Project goal: gaze-mouse-course-project](docs/goals/gaze-concept-v3.png)

AI-generated concept illustration of gaze-based interaction; not a photograph of the project or an application screenshot.

## Where it could be used

This could serve as a starting point for hands-free pointer interaction and accessibility-interface research. Looking at a target and using a deliberate blink or gaze hold could provide an alternative input method when operating a conventional mouse is inconvenient. Practical use would need user-specific calibration and evaluation of comfort, accidental clicks and performance under changing camera and lighting conditions.

## At a glance

![Gaze-controlled mouse](docs/flowcharts/gaze.png)

Overview reconstructed from the documented project and code. Results and verification limits are described below. [SVG](docs/flowcharts/gaze.svg)

## Project configuration and team

The project connects labelled eye-image collection, a learned gaze model and a live interface with calibration and mouse interaction. Sangheon Park carried out data collection and preprocessing, model implementation and training, calibration, pointer and click behaviour, and performance evaluation and integration. 김선우 developed the project's keyboard component and prepared the presentation materials.

The gaze model builds on published FGI-Net research. These responsibilities describe the team's project work; the original model research and supporting libraries retain their own attribution.

## How the interface works

The working sequence starts before the live application: collect labelled eye images, train and evaluate the gaze model, then load a compatible checkpoint. At the start of a session, calibration pairs the model's estimates with known screen targets. The live loop then captures a frame, extracts the eye regions, predicts a position, applies the calibration and smoothing, and updates the pointer. Gaze-hold and blink checks provide the click interaction.

Calibration is repeated for the current user and setup; model training is not repeated for every camera frame. This distinction explains how the data tools, model and live interface fit together.

Calibration is a separate step from training. Looking at several known screen targets gives pairs of predicted and actual positions; the program uses those pairs to fit a linear correction for the current setup. It collects stable recent predictions rather than relying on one frame. Moving the camera or changing the user's position can change that relationship.

## Data and model

Eye images were collected while participants looked at targets with known screen coordinates. Image paths and coordinates were stored for training, and the trained gaze model was connected to the live interface. The model was informed by an FGI-Net paper, with modifications described in the project report; it is not claimed as an exact reproduction of the published architecture.

One complication is that ordinary image augmentation is not automatically valid for gaze estimation. Flipping or rotating an eye image can change the meaning of its direction label. Another is that a low coordinate error alone does not make a comfortable mouse interface: calibration, smoothing and click behaviour must also work together.

## Results

MAE is the mean absolute error in screen-coordinate pixels; FPS is the number of frames processed per second. The original report recorded a validation MAE of 66.77 px, a test MAE of 67.71 px and approximately 22–24 FPS. The split was made at image level, so nearby frames may have appeared in different splits. These figures are internal results from the course project, not an independent-user benchmark. Training and measurement were not repeated for this portfolio copy.

## Reading the implementation

Start with [main.py](main.py) to follow the live frame loop and pointer interaction. Next, read [gaze_utils.py](gaze_utils.py) for the calibration and preprocessing steps, then [fginet.py](fginet.py) for the network. This order shows what the interface needs from the model before going into the model's internal structure.

To inspect the project, no webcam is needed. To reproduce the live demo, first prepare a compatible environment and model checkpoint, then calibrate for the user and screen. Merely opening the repository or installing the listed environment does not supply a trained model or a valid calibration.

## Running

The recorded environment is in [environment.yml](environment.yml). The live program needs compatible model weights, a webcam and user calibration. It also controls the mouse pointer. The weights and personal calibration data are not supplied here, so the repository cannot run the full demo immediately after cloning.

The Python source was checked for syntax without opening the camera or controlling the pointer. The original report and source history retain the joint authorship.

[Original repository](https://github.com/oldprize47/DLIP_FinalProject2025_GazeMouse). Original history and attribution are retained.
