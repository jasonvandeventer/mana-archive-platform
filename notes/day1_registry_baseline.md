# Day 1 Registry Baseline

## Purpose
Move from a local-only image to a real deployable image stored in a registry.

## Registry
- Registry used: GitHub Container Registry (GHCR)

## Image Path
- `ghcr.io/jasonvandeventer/mana-archive:latest`

## Workflow
1. Build image locally on Nobara
2. Tag image for GHCR
3. Push image to GHCR
4. Kubernetes pulls image from GHCR

## Why This Matters
- Prevents building directly on the cluster node
- Separates build environment from runtime environment
- Makes the deployment reproducible
