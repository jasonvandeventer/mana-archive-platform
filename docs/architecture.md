# Architecture

## Overview

This application is a containerized Streamlit app deployed to a K3s Kubernetes cluster. It is exposed via an Ingress controller (Traefik) and uses a PersistentVolumeClaim (PVC) to store application data.

The system is intentionally minimal and designed to demonstrate core Kubernetes concepts:

* workload deployment
* service networking
* ingress routing
* persistent storage

---

## High-Level Request Flow

Client → Ingress (Traefik) → Service → Pod → Container → Application
↓
PersistentVolume (SQLite DB)

---

## Component Breakdown

### 1. Client

The user accesses the application via a browser using:

```
http://mana.local
```

This hostname is resolved locally via `/etc/hosts`.

---

### 2. Ingress (Traefik)

* Acts as the HTTP entry point into the cluster
* Matches requests based on the **Host header**
* Configured with rule:

  * host: `mana.local`

Behavior:

* If Host header matches → forwards request to Service
* If it does not match → returns 404

---

### 3. Service (ClusterIP)

* Provides a stable internal endpoint for the application
* Decouples networking from Pod lifecycle

Configuration:

* Port: `80`
* TargetPort: `8501`

Behavior:

* Receives traffic from Ingress
* Routes it to the correct Pod using label selectors

---

### 4. Pod

* The execution unit in Kubernetes
* Created and managed by the Deployment
* Runs a single container instance of the app

Properties:

* Ephemeral (can be destroyed and recreated)
* Does not persist data on its own

---

### 5. Container

* Built from a Python base image
* Contains:

  * Streamlit app
  * dependencies from `requirements.txt`
* Starts with:

```
streamlit run app.py --server.address=0.0.0.0 --server.port=8501
```

Behavior:

* Serves the application on port `8501`
* Writes data to `/app/data`

---

### 6. PersistentVolumeClaim (PVC)

* Provides persistent storage to the Pod
* Mounted inside the container at:

```
/app/data
```

Purpose:

* Stores SQLite database (`mana_archive.db`)
* Ensures data survives Pod restarts

Important behavior:

* Replaces the container’s `/app/data` directory
* Does NOT automatically migrate existing data

---

## Deployment Model

### Deployment

* Manages Pod lifecycle
* Ensures desired state (1 replica)

Behavior:

* If Pod dies → automatically recreated
* New Pod mounts the same PVC

---

## Data Flow

1. User sends request to `mana.local`
2. Ingress receives request and matches host rule
3. Ingress forwards to Service
4. Service routes to Pod
5. Pod forwards request to container
6. Streamlit app processes request
7. App reads/writes data from `/app/data`
8. Data persists via PVC

---

## Key Design Decisions

### Use of Streamlit

* Simplifies UI and backend into a single process
* Avoids need for separate API + frontend

### Single Replica Deployment

* SQLite does not support concurrent multi-writer scaling
* Keeps architecture simple and correct for current scope

### PVC for Storage

* Required because container filesystem is ephemeral
* Enables stateful behavior in Kubernetes

### Host-Based Ingress

* Demonstrates real-world routing behavior
* Highlights importance of Host header matching

---

## Known Limitations

* SQLite does not scale across multiple replicas
* No backup strategy implemented
* No authentication or access control
* No CI/CD pipeline
* Manual host resolution required (`/etc/hosts`)

---

## Future Improvements

* Replace SQLite with external database (PostgreSQL)
* Add Secrets for configuration management
* Introduce CI/CD for image build and deployment
* Implement automated DNS instead of manual host mapping
* Add monitoring and logging aggregation

---

## Core Insight

The system separates concerns across layers:

* Application logic → container
* Execution → Pod
* Networking → Service + Ingress
* Storage → PersistentVolume

This separation allows:

* independent scaling
* reproducibility
* resilience to failure

Without persistent storage, the system would lose all data on Pod restart. The PVC enables correct stateful behavior within Kubernetes.

