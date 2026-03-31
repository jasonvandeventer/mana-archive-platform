# Day 1 Container Baseline

## Purpose
Prove the application runs correctly inside a container.

## Build Tool
- Container tool used: Podman

## Dockerfile Behavior
- Base image: Python slim image
- Working directory: `/app`
- Copies `requirements.txt`
- Installs Python dependencies
- Copies application source
- Exposes port `8501`
- Starts app with:
  `streamlit run app.py --server.address=0.0.0.0 --server.port=8501`

## Why `0.0.0.0` Matters
- Required so the app is reachable outside the container
- Binding only to localhost inside the container would make the port mapping appear broken

## Image
- Local image tag used: `localhost/mana-archive:local`

## Validation
- Image built successfully
- Container started successfully
- App reachable from host at:
  - `http://127.0.0.1:8501`
  - `http://10.42.1.20:8501`

## Networking Observation
- `127.0.0.1:8501` worked
- `localhost:8501` failed
- Likely cause: localhost resolving to IPv6 (`::1`) while the published port was reachable over IPv4
- Conclusion: container networking worked correctly; localhost behavior was a local resolution issue, not an app issue
