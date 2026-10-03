# Project Executable Files — How to Run

All commands below assume your terminal's working directory is `Project/`.

## 1. Create and activate a local virtual environment

```bash
cd Project
python -m venv venv

# Windows (PowerShell / Git Bash)
venv\Scripts\activate       # PowerShell
source venv/Scripts/activate  # Git Bash

# macOS/Linux
source venv/bin/activate
```

> This project was built and tested against **Python 3.11.9**. If your default `python` resolves to a very new version (e.g. 3.13/3.14) and `pip install` fails building `pydantic-core` or `pillow` from source, install with a 3.11 interpreter instead (e.g. `py -3.11 -m venv venv` on Windows).

## 2. Install dependencies

```bash
pip install -r requirements.txt
```

## 3. Configure environment variables

Copy the example file and fill in your real key:

```bash
cp .env.example .env
```

Edit `.env`:

```
GOOGLE_API_KEY=your_actual_gemini_api_key
SECRET_KEY=some_random_secret_string
```

Without a real `GOOGLE_API_KEY`, the app still runs — every planner will transparently use its static fallback recommendations and the UI will show a "fallback" banner.

## 4. Run the server

```bash
uvicorn main:app --reload --port 8000
```

Then open **http://127.0.0.1:8000** in your browser.

To stop the server, press **Ctrl+C** in the terminal.

## 5. Run the test suite

```bash
pytest tests/ -v
```

All tests mock the Gemini call, so they pass with no API key configured.
