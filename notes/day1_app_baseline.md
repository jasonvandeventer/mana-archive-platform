# Day 1 App Baseline

## Entrypoint
- File: app.py
- Command: streamlit run app.py

## Runtime
- Framework: Streamlit
- Startup behavior:
  - Loads environment variables via dotenv
  - Initializes SQLite DB via init_db()

## Dependencies
- streamlit>=1.32.0
- sqlalchemy>=2.0.0
- pandas>=2.2.0
- requests>=2.31.0
- aiohttp>=3.9.0
- python-dotenv>=1.0.0

## Network
- Local URL: http://localhost:8501
- Network URL: http://10.42.1.20:8501
- Port: 8501

## Data
- SQLite DB path:
  /home/jason/lab/mana-archive/data/mana_archive.db

## Result
- App starts successfully: yes
- Reachable in browser: yes

## Key Observations
- App is NOT Flask; it is Streamlit
- Streamlit manages the web server
- No explicit port config in code (default 8501)

## Container Baseline
- Tool used: Podman
- Image built successfully: yes
- Container started successfully: yes
- App reachable at host IP: yes
- App reachable at localhost: No, but only because localhost is set for IPv6
- Observation: Streamlit in container is reachable on published port 8501

## Networking Observation
- 127.0.0.1 works
- localhost fails (IPv6 ::1 resolution)
- Host IP works (10.42.1.20:8501)
- Conclusion: container is correctly exposed on IPv4
