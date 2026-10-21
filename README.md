# 🏋️ AI Real-Time GYM Coach

An AI-powered workout assistant that uses real-time computer vision to track exercise movements, count repetitions, and provide intelligent coaching through a web-based interface.

Built with **Python, Streamlit, MediaPipe, OpenCV, WebRTC, and Groq**.

## ✨ Features

- **Real-Time Pose Detection:** Uses MediaPipe Pose Landmarker to detect and track body landmarks from a live webcam feed.
- **Automatic Rep Counting:** Tracks repetitions using exercise-specific movement detection logic.
- **Multiple Exercises:** Supports:
  - Squats
  - Push-ups
  - Dumbbell Biceps Curls
  - Shoulder Press
  - Lunges
- **Live Workout Feedback:** Processes body landmarks to analyze exercise movements and display workout metrics.
- **AI-Powered Coaching:** Integrates the Groq API to provide AI-generated workout guidance.
- **Interactive Web Interface:** Uses Streamlit to provide a simple interface for starting and monitoring workouts.
- **Persistent Workout Data:** Uses SQLite for local data persistence, where configured.
- **Webcam Streaming:** Uses WebRTC to stream camera frames to the application for real-time processing.

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Python | Core application logic |
| Streamlit | Web application interface |
| streamlit-webrtc | Real-time webcam streaming |
| MediaPipe Tasks | Human pose landmark detection |
| OpenCV | Image processing and frame manipulation |
| NumPy | Numerical operations |
| PyAV (`av`) | Video frame handling |
| Groq API | AI-powered coaching |
| SQLite | Local data persistence |
| Pandas | Data handling and analysis |

## 🏗️ How It Works

The application follows a real-time video-processing pipeline.

1. **Start a workout:** The user selects an exercise and starts the workout session.
2. **Capture video:** The browser requests webcam access and streams video frames through WebRTC.
3. **Process frames:** OpenCV prepares incoming frames for pose detection.
4. **Detect body landmarks:** MediaPipe Pose Landmarker identifies key body points, such as the shoulders, elbows, hips, knees, and ankles.
5. **Analyze movements:** Exercise-specific detectors process the landmarks to recognize movement phases and track repetitions.
6. **Display workout metrics:** The application updates the interface with the available exercise and workout information.
7. **Generate coaching:** The Groq integration supports AI-generated coaching responses.

### Architecture Overview

```text
User / Webcam
     |
     v
Streamlit Web Interface
     |
     v
WebRTC Video Streaming
     |
     v
OpenCV Frame Processing
     |
     v
MediaPipe Pose Landmarker
     |
     v
Body Landmark Detection
     |
     v
Exercise-Specific Detectors
     |
     v
Rep Counting & Workout Metrics
     |
     +----------------------+
     |                      |
     v                      v
Streamlit Dashboard    SQLite Persistence
     |
     v
Groq-Powered AI Coaching
```

*The diagram represents the main processing flow; the exact order of UI updates, persistence, and coaching calls depends on the implementation.*

## 📂 Project Structure

The main application is organized around the Streamlit entry point and the vision-processing modules.

```text
gymAi_app/
└── mainApp/
    ├── app.py
    ├── services/
    │   ├── vision/
    │   │   └── exercise_video_processor.py
    │   └── coaching/
    │       └── llm.py
    ├── ml_models/
    │   └── pose_landmarker_full.task
    ├── requirements.txt
    └── .streamlit/
        └── secrets.toml   # Local secrets; do not commit
```

Additional modules, exercise detectors, database files, and assets may exist in the repository.

## 🚀 Getting Started

### Prerequisites

- Python installed on your system
- Git
- A webcam
- A Groq API key for AI coaching
- A modern browser with webcam permissions enabled

### 1. Clone the repository

```bash
git clone https://github.com/RohanSingh404/gymAi_app.git
cd gymAi_app/mainApp
```

### 2. Create a virtual environment

**Windows PowerShell:**

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

If PowerShell blocks script execution, activate the environment using Command Prompt instead:

```cmd
venv\Scripts\activate.bat
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure the Groq API key

Create the local secrets directory and file if they do not already exist:

```powershell
New-Item -ItemType Directory -Force .streamlit
notepad .streamlit/secrets.toml
```

Add your key to `secrets.toml`:

```toml
GROQ_API_KEY = "your_groq_api_key"
```

Obtain an API key from [Groq Console](https://console.groq.com/).

**Security:** Never commit `secrets.toml`, API keys, or other credentials to GitHub. Add `.streamlit/secrets.toml` to `.gitignore`.

### 5. Verify the pose model

Ensure the required MediaPipe model file exists:

```text
ml_models/pose_landmarker_full.task
```

The application needs access to this model to initialize pose detection. If it is missing, provide the model file expected by the application.

### 6. Run the application

From the `mainApp` directory, execute:

```bash
streamlit run app.py
```

Open the local URL printed in the terminal, usually:

```text
http://localhost:8501
```

Grant camera permission when prompted, select an exercise, and start a workout.

## 🌐 Deployment

The application can be deployed using Streamlit Community Cloud.

1. Push the project to a GitHub repository.
2. Open [Streamlit Community Cloud](https://share.streamlit.io/).
3. Create a new app and select the repository and branch.
4. Set the application entry point to `mainApp/app.py` if deploying from the repository root.
5. Add `GROQ_API_KEY` under the app's **Settings → Secrets**.
6. Deploy and check the application logs if startup or video processing fails.

Make sure the deployed environment installs the dependencies from the correct `requirements.txt` and includes the required pose model file.

### WebRTC Connectivity

The application currently uses Google's public STUN server:

```python
rtc_configuration = {
    "iceServers": [
        {"urls": ["stun:stun.l.google.com:19302"]}
    ]
}
```

STUN-only connectivity does not work in every network environment. Some firewalls and network configurations prevent direct peer-to-peer WebRTC connections, which can cause a black camera feed or a connection that never finishes loading. A successful browser camera permission prompt alone does not guarantee that WebRTC has connected.

## 🔧 Troubleshooting

| Problem | What to check |
|---|---|
| Camera permission denied | Allow camera access in your browser settings. |
| Black video feed | Check WebRTC connection state, browser console errors, and app logs. |
| Workout never starts | Confirm that the video processor initializes and the pose model is present. |
| MediaPipe GPU warning | CPU execution is supported; this warning alone does not mean pose detection has failed. |
| Groq API error | Verify `GROQ_API_KEY`, API access, and the model ID configured in the coaching service. |
| Missing Python packages | Reinstall dependencies from `requirements.txt` in the correct environment. |
| Works locally but not on Streamlit Cloud | Check cloud logs, dependency versions, model-file availability, and WebRTC network restrictions. |

## 🔒 Security and Privacy

- Keep API keys in Streamlit Secrets or environment variables.
- Never upload credentials or secret configuration files to a public repository.
- Request webcam permission only when needed.
- Review how workout metrics and session data are stored before using the application with other users.

## 🚧 Future Improvements

Potential improvements include:

- More exercise types and configurable workout routines.
- Better feedback on exercise form and movement quality.
- Workout history and progress visualizations.
- Personalized workout plans and coaching.
- Improved handling of WebRTC connection failures.
- More robust session management and error reporting.

## 👨‍💻 Author

**Rohan Singh**

- GitHub: [RohanSingh404](https://github.com/RohanSingh404)
- Project Repository: [gymAi_app](https://github.com/RohanSingh404/gymAi_app)

## 📄 License

No license is specified here. Add a `LICENSE` file to the repository if you want to explicitly define the terms under which others may use, modify, or distribute the project.
