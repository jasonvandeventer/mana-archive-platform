# Day 1 App Baseline

## Purpose
Establish how the application runs outside of containers and Kubernetes.

## Entrypoint
- File: `app.py`
- Correct run command: `streamlit run app.py`

## App Type
- Framework: Streamlit
- This is not a Flask app with `app.run()`
- Streamlit provides the web server and UI runtime

## Startup Behavior
- Loads environment variables with `python-dotenv`
- Calls `init_db()` at startup
- Initializes/verifies SQLite tables on launch

## Dependencies
- streamlit>=1.32.0
- sqlalchemy>=2.0.0
- sqlalchemy? no typo check from requirements if needed
- aiohttp>=3.9.0
- requests>=2.31.0
- pandas>=2.2.0
- python-dotenv>=1.0.0

## Runtime Details
- Local URL: `http://localhost:8501`
- Network URL: `http://10.42.1.20:8501`
- Streamlit default port: `8501`

## Data
- Current database path: `data/mana_archive.db`
- Database type: SQLite
- Data is currently local to the app directory

## Result
- App runs successfully on Nobara
- App is reachable in browser
