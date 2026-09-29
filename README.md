# 시선 추적 마우스



[한국어](#korean) · [English](#english)



<a id="korean"></a>

## 한국어

[상세 사용법](#사용법-튜토리얼) · [원본 튜토리얼](README.original.md)



시선 추적 마우스는 2025년 영상처리 수업에서 개발한 웹캠 기반 포인터 인터페이스입니다. 사용자가 바라보는 위치를 추정하고, 추정값을 화면 좌표에 맞게 보정해 마우스 포인터를 움직입니다. 전용 시선 추적 장비 없이도 시선을 일정 시간 유지하거나 눈을 깜박이는 방식으로 클릭할 수 있습니다.



[팀 시연 영상 보기](https://www.youtube.com/watch?v=VR9T6X-zanU).



### 프로젝트 목표



전용 시선 추적 장비 없이 일반 웹캠으로 눈의 시선을 이용해 마우스 포인터를 움직이고 클릭하는 것이 목표입니다.



![프로젝트 목표: gaze-mouse-course-project](docs/goals/gaze-concept-v3.png)



<sub>AI 생성 개념도</sub>



### 활용할 수 있는 곳



손을 사용하지 않는 포인터 조작과 접근성 인터페이스 연구의 출발점으로 활용할 수 있습니다. 일반 마우스를 조작하기 불편한 상황에서 목표를 바라보고 의도적으로 눈을 깜박이거나 시선을 유지하는 방식이 대체 입력 수단이 될 수 있습니다. 실제로 사용하려면 사용자별 보정이 필요하며, 사용 편의성, 의도하지 않은 클릭, 카메라와 조명 조건의 변화에 따른 성능을 평가해야 합니다.



### 전체 흐름



![시선으로 제어하는 마우스](docs/flowcharts/gaze.png)



<sub>구성도 · <a href="docs/flowcharts/gaze.svg">SVG</a></sub>



### 프로젝트 구성과 팀 역할



이 프로젝트는 정답 라벨이 있는 눈 이미지 수집, 학습된 시선 추정 모델, 보정과 마우스 조작 기능을 갖춘 실시간 인터페이스를 연결합니다. 박상헌은 데이터 수집과 전처리, 모델 구현과 학습, 보정, 포인터 이동과 클릭 동작, 성능 평가와 통합을 수행했습니다. 김선우는 프로젝트의 키보드 구성요소를 개발하고 발표 자료를 준비했습니다.



시선 추정 모델은 공개된 FGI-Net 연구를 기반으로 합니다. 위 역할은 팀이 프로젝트에서 수행한 작업을 설명하며, 원래 모델 연구와 사용한 라이브러리의 출처 및 기여 표기는 별도로 유지됩니다.



### 인터페이스 작동 방식



작업은 실시간 애플리케이션을 실행하기 전부터 시작됩니다. 정답 라벨이 있는 눈 이미지를 수집하고, 시선 추정 모델을 학습·평가한 다음, 호환되는 체크포인트를 불러옵니다. 세션을 시작할 때는 보정을 통해 모델의 추정값과 화면상의 알려진 목표 위치를 짝지어 대응시킵니다. 이후 실시간 처리 루프에서 프레임을 촬영하고, 눈 영역을 추출하고, 위치를 예측한 뒤, 보정과 평활화를 적용해 포인터를 갱신합니다. 시선 유지와 눈 깜박임을 확인해 클릭 동작을 처리합니다.



보정은 현재 사용자와 사용 환경에 맞춰 다시 수행하지만, 카메라의 매 프레임마다 모델을 다시 학습하지는 않습니다. 이 차이를 구분하면 데이터 도구, 모델, 실시간 인터페이스가 어떻게 연결되는지 이해할 수 있습니다.



보정은 학습과 별개의 단계입니다. 화면에서 위치가 알려진 여러 목표를 바라보면 예측 위치와 실제 위치의 쌍을 얻을 수 있으며, 프로그램은 이 쌍을 사용해 현재 환경에 맞는 선형 보정을 구합니다. 한 프레임에만 의존하지 않고 최근의 안정적인 예측값을 모읍니다. 카메라를 움직이거나 사용자의 위치가 바뀌면 이 대응 관계도 달라질 수 있습니다.



### 데이터와 모델



참가자가 화면 좌표를 알고 있는 목표를 바라보는 동안 눈 이미지를 수집했습니다. 이미지 경로와 좌표를 학습용으로 저장하고, 학습한 시선 추정 모델을 실시간 인터페이스에 연결했습니다. 모델은 FGI-Net 논문을 참고했으며, 프로젝트 보고서에 설명한 수정 사항을 반영했습니다. 모델을 읽을 때는 이 수정 사항을 함께 참고하면 됩니다. 발표된 구조의 정확한 재현을 주장하는 구현은 아닙니다.



데이터를 준비할 때는 이미지 변형이 시선 라벨에 미치는 영향을 함께 살펴봐야 합니다. 일반적인 이미지 증강 기법을 그대로 적용하면, 눈 이미지를 뒤집거나 회전하는 과정에서 방향 라벨의 의미가 달라질 수 있습니다. 사용감을 평가할 때도 좌표 오차와 함께 보정, 평활화, 클릭 동작이 잘 맞물리는지 확인해야 합니다. 좌표 오차가 작더라도 이 동작들이 함께 작동해야 편안한 마우스 인터페이스가 됩니다.



### 결과



MAE는 화면 좌표의 평균 절대 오차이며 단위는 픽셀입니다. FPS는 초당 처리한 프레임 수입니다. 원래 보고서에는 검증 MAE 66.77 px, 테스트 MAE 67.71 px, 처리 속도 약 22–24 FPS가 기록되어 있습니다. 데이터는 이미지 단위로 분할했으므로 시간상 가까운 프레임이 서로 다른 분할에 포함됐을 수 있습니다. 이 수치는 수업 프로젝트 내부 결과이며, 독립된 사용자를 대상으로 한 벤치마크가 아닙니다. 이 포트폴리오 사본을 위해 학습과 측정을 다시 수행하지는 않았습니다.



### 구현 살펴보기



구현을 처음 살펴본다면 [main.py](main.py)의 실시간 프레임 처리 루프와 포인터 조작부터 읽는 것이 도움이 됩니다. 이어서 [gaze_utils.py](gaze_utils.py)의 보정과 전처리 단계, [fginet.py](fginet.py)의 신경망을 살펴볼 수 있습니다. 이 순서로 읽으면 모델 내부 구조를 살펴보기 전에 인터페이스가 모델에 요구하는 역할을 이해할 수 있습니다.



프로젝트 내용을 살펴보는 데는 웹캠이 필요하지 않습니다. 실시간 시연을 재현하려면 먼저 호환되는 실행 환경과 모델 체크포인트를 준비하고, 사용자와 화면에 맞춰 보정해야 합니다. 학습된 모델과 유효한 보정값은 저장소나 환경 설치만으로 제공되지 않으므로, 실행 전에 별도로 준비해야 합니다.



### 사용법 튜토리얼

[기존 상세 튜토리얼 전체 보기](README.original.md) · [환경 설정과 단계별 코드 설명](README.original.md#3-tutorial-procedure)

기존에 작성한 환경 설정, 데이터 수집, 학습, 실시간 실행, 보정과 키보드 사용 설명은 위 원문에 그대로 보존되어 있습니다. 아래는 실행 순서를 빠르게 찾기 위한 안내입니다.

1. **환경 준비:** 전체 소스가 있는 [GazeMouse](https://github.com/oldprize47-SH/GazeMouse)를 내려받고 프로젝트 폴더에서 다음 명령을 실행합니다.

   ```sh
   conda env create -f environment.yml
   conda activate Gaze_mouse_fgi
   ```

2. **눈 이미지 수집:** `make_csv_custom.py`에서 저장 폴더와 CSV 경로를 정한 뒤 실행합니다. 화면의 목표를 바라보며 `Space`로 이미지를 수집하고 `Esc`로 종료합니다. 목표별 수집량과 이어서 수집하는 방법은 원문 STEP 1을 참고하세요.
3. **모델 학습:** `train.py`의 데이터와 저장 경로를 맞춘 뒤 학습합니다. 데이터 분할, 체크포인트와 평가 과정은 원문 STEP 2에 설명되어 있습니다. 학습 결과는 실시간 프로그램의 `CKPT` 설정과 일치해야 하며, 기본 파일명은 `model_weights.pth`입니다.
4. **실시간 실행:** 호환되는 가중치를 준비한 뒤 `python main.py`를 실행합니다. 웹캠이 켜지고 실제 마우스 포인터를 제어하므로 종료 키 `Esc`를 먼저 확인해 주세요.
5. **사용자 보정:** `c`로 보정을 시작하고 화면의 안내를 따릅니다. 저장된 `calib.npy`를 불러오려면 `Space`를 누릅니다. 사용자나 카메라 위치가 달라졌다면 다시 보정합니다.
6. **포인터와 클릭:** 보정 후 시선에 따라 포인터가 이동합니다. 원문은 일정 시간 시선을 고정해 포인터를 잠근 뒤 두 눈을 깜박여 클릭하는 순서를 설명합니다. `m`은 창 배치를 바꾸며 `Esc`는 종료합니다. 세부 임계값과 화면 키보드 설명도 원문에 있습니다.

가중치와 개인 보정 데이터는 별도로 준비해야 합니다. 위 안내는 원래 튜토리얼과 현재 소스의 설정·단축키를 대조한 것으로, 이번 문서 복원에서 웹캠 실행이나 모델 재학습을 수행하지는 않았습니다.

### 실행



기록된 실행 환경은 [environment.yml](environment.yml)에 있습니다. 실시간 프로그램에는 호환되는 모델 가중치, 웹캠, 사용자 보정이 필요합니다. 또한 이 프로그램은 마우스 포인터를 제어합니다. 가중치와 개인 보정 데이터는 여기에서 제공하지 않습니다. 저장소를 복제한 뒤 전체 시연을 실행하려면 이 자료를 별도로 준비하는 과정이 필요합니다.



카메라를 열거나 포인터를 제어하지 않고 Python 소스의 구문을 검사했습니다. 원래 보고서와 소스 이력에는 공동 저작 이력이 유지되어 있습니다.



[원본 저장소](https://github.com/oldprize47/DLIP_FinalProject2025_GazeMouse). 원래 이력과 기여 표기를 유지했습니다.



---



<a id="english"></a>

## English

[Usage guide](#usage-tutorial) · [Original tutorial](README.original.md)



**Gaze-Controlled Mouse**



Gaze Tracking Mouse is a webcam-based pointer interface developed for a 2025 image-processing course. It estimates where a user is looking, calibrates the estimate to the screen and moves the mouse pointer. A gaze-hold and blink interaction provides clicking without dedicated eye-tracking hardware.



[Watch the team demonstration](https://www.youtube.com/watch?v=VR9T6X-zanU).



### Project goal



Use a normal webcam to move and click the mouse pointer with eye gaze, without dedicated eye-tracking hardware.



![Project goal: gaze-mouse-course-project](docs/goals/gaze-concept-v3.png)



<sub>AI-generated concept illustration</sub>



### Where it could be used



This could serve as a starting point for hands-free pointer interaction and accessibility-interface research. Looking at a target and using a deliberate blink or gaze hold could provide an alternative input method when operating a conventional mouse is inconvenient. Practical use would need user-specific calibration and evaluation of comfort, accidental clicks and performance under changing camera and lighting conditions.



### At a glance



![Gaze-controlled mouse](docs/flowcharts/gaze.png)



<sub>System overview · <a href="docs/flowcharts/gaze.svg">SVG</a></sub>



### Project configuration and team



The project connects labelled eye-image collection, a learned gaze model and a live interface with calibration and mouse interaction. Sangheon Park carried out data collection and preprocessing, model implementation and training, calibration, pointer and click behaviour, and performance evaluation and integration. 김선우 developed the project's keyboard component and prepared the presentation materials.



The gaze model builds on published FGI-Net research. These responsibilities describe the team's project work; the original model research and supporting libraries retain their own attribution.



### How the interface works



The working sequence starts before the live application: collect labelled eye images, train and evaluate the gaze model, then load a compatible checkpoint. At the start of a session, calibration pairs the model's estimates with known screen targets. The live loop then captures a frame, extracts the eye regions, predicts a position, applies the calibration and smoothing, and updates the pointer. Gaze-hold and blink checks provide the click interaction.



Calibration is repeated for the current user and setup; model training is not repeated for every camera frame. This distinction explains how the data tools, model and live interface fit together.



Calibration is a separate step from training. Looking at several known screen targets gives pairs of predicted and actual positions; the program uses those pairs to fit a linear correction for the current setup. It collects stable recent predictions rather than relying on one frame. Moving the camera or changing the user's position can change that relationship.



### Data and model



Eye images were collected while participants looked at targets with known screen coordinates. Image paths and coordinates were stored for training, and the trained gaze model was connected to the live interface. The model was informed by an FGI-Net paper, with modifications described in the project report; it should be read as an adapted implementation rather than an exact reproduction of the published architecture.



When preparing gaze data, it helps to consider how image transformations affect the labels. Ordinary image augmentation is not automatically valid here: flipping or rotating an eye image can change the meaning of its direction label. For a comfortable mouse interface, low coordinate error also needs to be paired with calibration, smoothing and click behaviour that work well together.



### Results



MAE is the mean absolute error in screen-coordinate pixels; FPS is the number of frames processed per second. The original report recorded a validation MAE of 66.77 px, a test MAE of 67.71 px and approximately 22–24 FPS. The split was made at image level, so nearby frames may have appeared in different splits. These figures are internal results from the course project, not an independent-user benchmark. Training and measurement were not repeated for this portfolio copy.



### Reading the implementation



If you are new to the implementation, [main.py](main.py) is a useful starting point for the live frame loop and pointer interaction. From there, [gaze_utils.py](gaze_utils.py) explains the calibration and preprocessing steps, and [fginet.py](fginet.py) defines the network. This order shows what the interface needs from the model before going into the model's internal structure.



You can explore the project without a webcam. To reproduce the live demo, first prepare a compatible environment and model checkpoint, then calibrate for the user and screen. The trained model and a valid calibration need separate preparation; opening the repository or installing the listed environment does not provide them.



### Usage tutorial

[Read the original detailed tutorial](README.original.md) · [Environment setup and step-by-step code walkthrough](README.original.md#3-tutorial-procedure)

The original environment setup, data collection, training, live interaction, calibration and keyboard instructions remain available in full. This is a quick route through that workflow.

1. **Prepare the environment:** download the complete [GazeMouse](https://github.com/oldprize47-SH/GazeMouse) source and run these commands from the project directory.

   ```sh
   conda env create -f environment.yml
   conda activate Gaze_mouse_fgi
   ```

2. **Collect eye images:** configure the output folder and CSV path in `make_csv_custom.py`, then run it. Look at each displayed target, press `Space` to capture images and `Esc` to exit. Original STEP 1 explains capture counts and resuming collection.
3. **Train the model:** set the data and output paths in `train.py`. Original STEP 2 covers splitting data, checkpoints and evaluation. The resulting weights must match the live application's `CKPT` setting, which defaults to `model_weights.pth`.
4. **Run the interface:** once compatible weights are available, run `python main.py`. This opens the webcam and controls the actual mouse pointer; `Esc` exits.
5. **Calibrate:** press `c` and follow the on-screen targets. Press `Space` to load the saved `calib.npy`. Recalibrate when the user or camera position changes.
6. **Move and click:** after calibration, gaze moves the pointer. The original tutorial describes holding gaze to lock the pointer, then blinking both eyes to click. Press `m` to change window placement or `Esc` to exit. Threshold settings and the screen keyboard are described in the full tutorial.

Model weights and personal calibration data need separate preparation. These instructions were checked against the original tutorial and current source settings and key bindings; no webcam run or retraining was performed during this documentation restoration.

### Running



The recorded environment is in [environment.yml](environment.yml). The live program needs compatible model weights, a webcam and user calibration. It also controls the mouse pointer. The weights and personal calibration data are not supplied here. After cloning, you will need to prepare these before running the full demo.



The Python source was checked for syntax without opening the camera or controlling the pointer. The original report and source history retain the joint authorship.



[Original repository](https://github.com/oldprize47/DLIP_FinalProject2025_GazeMouse). Original history and attribution are retained.

