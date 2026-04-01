# Day 1 Summary

## What I Accomplished
I rebuilt the project from a clean foundation instead of continuing with the previous mixed environment. I verified the app locally, containerized it on my Nobara workstation, pushed the image to GHCR, and deployed it to my K3s cluster on node1 using a Deployment, Service, and Ingress.

## Most Important Things I Learned
- The application is a Streamlit app, not a Flask app
- The correct entrypoint is `streamlit run app.py`
- The app listens on port `8501`
- The app currently uses a local SQLite database in `data/mana_archive.db`
- Kubernetes is running the same container I tested locally
- Ingress routing depends on the Host header, not just the IP address

## Architecture Built
Nobara -> build container -> push to GHCR -> Kubernetes Deployment -> Service -> Traefik Ingress

## Problems Encountered
- Initially mixed development, build, and runtime work on node1
- Localhost behaved differently than 127.0.0.1 during container testing
- Direct IP access to the ingress returned 404 because the host rule expected `mana.local`

## Why Those Problems Happened
- My earlier setup blurred environment boundaries
- Podman/network resolution behaved differently for localhost vs IPv4 loopback
- Ingress rules match on hostname, not raw IP, unless configured otherwise

## Current State
- App runs locally
- App runs in a container
- Image is stored in GHCR
- App runs in Kubernetes
- Service and Ingress are working
- Local hostname mapping is required for browser testing of `mana.local`

## Next Steps
- Clean up remaining old cluster resources
- Move working files into the project repo cleanly
- Improve project documentation
- Add persistence for the SQLite database
