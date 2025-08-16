<div align="center">
  <img src="images/infinite_radio.png" alt="Infinite Radio Logo"/>
</div>

> **Note**
>
> A huge thank you to the original author, **LaurieWired**, for creating the amazing Infinite Radio project. This repository is a fork that adapts the original vision to run on different hardware and operating systems.
>
> The primary changes in this fork are:
> - **AMD GPU Support:** The core music generation model now runs with AMD's ROCm technology instead of NVIDIA's CUDA.
> - **Windows Compatibility:** The user interface is a native Windows application instead of a macOS application.

# Infinite Radio

Infinite Radio generates endless music that automatically changes based on your current context. It combines the [Magenta RealTime](https://magenta.withgoogle.com/magenta-realtime) music model with contextual genre selection, either from a vision-capable Large Language Model or from the top processes running on your machine.

This version has been adapted to run on **Windows with AMD GPUs** via ROCm in a Docker container.

# Getting Started

## Prerequisites

1.  A **Windows** machine with a modern **AMD GPU** and its latest drivers installed.
2.  **Docker Desktop for Windows** installed and configured to use the **WSL2 backend**.
3.  **Git** installed on your Windows machine.

## Setup Instructions

These instructions will guide you through cloning the repository, building the music server, and running the Windows UI controller.

### 1. Clone the Repository

First, clone this repository to your local machine.

```sh
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```
*(Note: Replace the URL with the actual URL of this repository.)*

### 2. Build and Run the Music Server

The music server runs inside a Docker container that has the machine learning model and all its dependencies.

1.  **Navigate to the MusicContainer directory:**
    ```sh
    cd MusicContainer
    ```
2.  **Build the Docker image.** This might take a while as it downloads the ROCm environment and the ML models.
    ```sh
    docker build -t infinite-radio-rocm .
    ```
3.  **Run the container.** This command starts the server and gives it access to your AMD GPU. Keep this terminal open.
    ```sh
    docker run --rm -it -p 8080:8080 --device=/dev/kfd --device=/dev/dri infinite-radio-rocm
    ```

### 3. Run the Windows UI Controller

The UI controller is a native Windows application that runs from a separate terminal.

1.  **Open a new terminal** (PowerShell or Command Prompt).
2.  **Navigate to the project's root directory** (the one you cloned earlier).
3.  **(Recommended) Create and activate a Python virtual environment:**
    ```sh
    python -m venv venv
    .\venv\Scripts\activate
    ```
4.  **Install the required Python packages:**
    ```sh
    pip install -r requirements.txt
    ```
5.  **Run the application:**
    ```sh
    python windows_app.py
    ```

### 4. Configure and Use the App

1.  An "Infinite Radio" icon will appear in your Windows system tray.
2.  Right-click the icon and go to **Settings > Configure Server...**.
3.  Enter `127.0.0.1:8080` and click Save.
4.  You can now select a **DJ Type** (Process or LLM) and click **Start ... DJ**.
5.  A console window showing the script's output will appear. You can also view this from the "Show Console" menu item.

## Using the LLM DJ

If you choose the LLM DJ, you must also run a local LLM server that the DJ can connect to.

1.  Download a vision model like [InternVL3](https://huggingface.co/OpenGVLab/InternVL3-2B) in [LM Studio](https://lmstudio.ai).
2.  Start the server in LM Studio.
3.  **Important:** The `llm_dj.py` script is hardcoded to connect to `http://localhost:1234/v1`, which is the default for LM Studio. If you need to change the url/port, change **line 135 in `llm_dj.py`** (`lm_studio_url = "http://localhost:1234/v1"`) to your desired URL.

# Building the Windows Executable

For convenience, you can package the Windows UI controller into a single `.exe` file. This removes the need to install Python or dependencies on the end-user's machine.

1.  Navigate to the project's root directory.
2.  Install PyInstaller:
    ```sh
    pip install pyinstaller
    ```
3.  Run the PyInstaller command:
    ```sh
    pyinstaller --onefile --windowed --add-data "icon.png:." windows_app.py
    ```
4.  The final executable will be located in the `dist/` directory (`dist/windows_app.exe`). You can move this file anywhere.

# API Reference

The music server exposes a simple API to control the genre.

## Change Genre

**POST** `/genre`

```bash
curl -X POST http://localhost:8080/genre \
  -H "Content-Type: application/json" \
  -d '{"genre": "jazz"}'
```

## Get Current Genre

**GET** `/current-genre`

```bash
curl http://localhost:8080/current-genre
```
