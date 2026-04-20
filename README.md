# Mana Archive Platform

This repository contains the **GitOps source of truth** for the Mana Archive platform.

It defines and manages all Kubernetes resources using ArgoCD.

---

## Purpose

This repo represents the platform layer of the system:

- Kubernetes manifests
- ArgoCD application definitions
- Observability stack
- Storage configuration

It is intentionally separated from the application code.

App repo:  
https://github.com/jasonvandeventer/mana-archive

---

## Architecture Overview

The platform is built using:

- K3s (lightweight Kubernetes)
- ArgoCD (GitOps controller)
- Longhorn (distributed storage)
- Prometheus + Grafana + Loki (observability)

---

## GitOps Model

This repository uses an **app-of-apps** pattern:

platform-root (ArgoCD)
└── child applications
    ├── mana-archive
    ├── longhorn
    ├── observability stack
    └── other services

### Key idea

> Git is the source of truth.  
> ArgoCD continuously reconciles cluster state to match this repo.

---

## Repository Structure

k8s/
├── argocd/
│   ├── root-app.yaml
│   └── apps/
│       ├── mana-archive.yaml
│       ├── longhorn.yaml
│       └── whoami.yaml
├── apps/
│   └── mana-archive/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── pvc.yaml
│       └── namespace.yaml
└── observability/
    ├── namespace.yaml
    └── prometheus-stack/

---

## What this repo manages

### Application
- Mana Archive deployment
- Service and networking
- Persistent volume claims

### Storage
- Longhorn-backed volumes
- Backup/restore validation

### Observability
- Prometheus
- Grafana
- Loki

---

## Deployment Flow

1. Changes are committed to this repository
2. ArgoCD detects the change
3. ArgoCD applies manifests to the cluster
4. Cluster state is reconciled automatically

No manual `kubectl apply` is required after initial setup.

---

## Key Design Decisions

### 1. Separation of concerns
- App repo contains only application logic
- Platform repo contains infrastructure

### 2. GitOps over imperative control
- No manual cluster drift
- Everything is versioned and reproducible

### 3. Persistent storage abstraction
- Application is decoupled from storage implementation
- Longhorn provides resilience and backup capability

### 4. Observability built-in
- Metrics, logs, and dashboards are part of the platform
- Not an afterthought

---

## Operational Notes

- ArgoCD is configured with automated sync and self-heal
- Any manual change in the cluster will be reverted
- All changes must go through Git

---

## Why this project matters

This repository demonstrates:

- real-world GitOps workflows
- Kubernetes platform design
- infrastructure as code beyond Terraform
- operational thinking (backups, observability, reliability)

---

## Future Improvements

- Helm-based deployments for reusable components
- External Secrets / Vault integration
- Multi-environment support (dev/staging/prod)
- CI pipeline for manifest validation and security scanning

