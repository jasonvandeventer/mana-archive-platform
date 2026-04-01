# Mana Archive Platform

## Overview

Mana Archive is a containerized Streamlit application for managing a physical Magic: The Gathering card collection.

This project demonstrates building, packaging, and deploying a stateful application to Kubernetes using a clean, reproducible workflow.

---

## What This Project Demonstrates

* Running and understanding a Python application locally
* Containerizing an application with Podman
* Publishing container images to GitHub Container Registry (GHCR)
* Deploying workloads to a Kubernetes (K3s) cluster
* Exposing services using Kubernetes Service and Ingress (Traefik)
* Implementing persistent storage using PersistentVolumeClaims (PVC)

---

## Architecture

```
Client → Ingress (Traefik) → Service → Pod → Container → Streamlit App
                                      ↓
                              PersistentVolume (SQLite DB)
```

* **Ingress** routes HTTP traffic based on hostname (`mana.local`)
* **Service** provides a stable internal endpoint
* **Pod** runs the containerized application
* **PVC** stores the SQLite database at `/app/data`

---

## Application Details

* Framework: Streamlit
* Entrypoint:

  ```bash
  streamlit run app.py
  ```
* Default port: `8501`
* Database: SQLite (`/app/data/mana_archive.db`)

---

## Containerization

The application is packaged using a Python-based container image:

* Base image: `python:3.11-slim`
* Exposed port: `8501`
* Runtime command:

  ```bash
  streamlit run app.py --server.address=0.0.0.0 --server.port=8501
  ```

---

## Image Registry

Images are stored in GitHub Container Registry:

```
ghcr.io/jasonvandeventer/mana-archive:latest
```

---

## CI/CD

This project uses GitHub Actions to automatically build and push container images to GitHub Container Registry (GHCR) on every push to the main branch.

After a new image is published, Kubernetes can pull the updated image when the Deployment is restarted.

## Kubernetes Deployment

### Resources

* Deployment (`mana-archive`)
* Service (`ClusterIP`)
* Ingress (`Traefik`)
* PersistentVolumeClaim (`mana-archive-data`)

### Key Behavior

* The application is deployed as a single replica
* Traffic is routed through Traefik using host-based routing (`mana.local`)
* SQLite database is stored on a PVC mounted at `/app/data`

---

## Persistence

The application uses a PersistentVolumeClaim to store data:

* Mount path: `/app/data`
* Storage type: `ReadWriteOnce`
* Data survives Pod restarts

### Validation

* Data added through the app
* Pod deleted manually
* Pod recreated by Kubernetes
* Data persisted across restart

---

## Local Access

To access the application locally:

Add to `/etc/hosts`:

```
<node-ip> mana.local
```

Then open:

```
http://mana.local
```

---

## Key Lessons Learned

* Kubernetes Pods are ephemeral; data must be externalized
* Ingress routing depends on the Host header, not just IP address
* Container images should be built outside the cluster and pulled from a registry
* PersistentVolumeClaims do not migrate existing data automatically

---

## Project Status

This project currently supports:

* Single-replica deployment
* SQLite-backed persistence

Not yet implemented:

* Horizontal scaling
* External database
* CI/CD pipeline
* Secret management

---

## Next Steps

* Add environment configuration management
* Evaluate database options for scaling beyond SQLite

