# Day 1 Kubernetes Baseline

## Purpose
Deploy the working container to Kubernetes using the smallest clean architecture:
Ingress -> Service -> Pod -> Container -> App

## Cluster Context
- Workstation: Nobara
- Cluster node: node1
- kubectl is run from Nobara against node1

## Deployment
- Resource name: `mana-archive`
- Replicas: `1`
- Container image: `ghcr.io/jasonvandeventer/mana-archive:latest`
- Container port: `8501`

## Service
- Resource name: `mana-archive`
- Type: `ClusterIP`
- Service port: `80`
- Target port: `8501`

## Ingress
- Resource name: `mana-archive`
- Ingress controller: Traefik
- Host rule: `mana.local`
- Backend service: `mana-archive`
- Backend port: `80`

## What Each Resource Does
- Deployment: tells Kubernetes to run and manage the Pod
- Pod: runs the container image
- Service: gives the Pod a stable internal endpoint
- Ingress: routes HTTP requests to the Service based on the host header

## Validation
- Deployment created successfully
- Pod reached Running state
- Service created successfully
- Ingress created successfully

## Important Behavior Observed
- Browsing to `http://10.42.1.50` returned `404`
- This was expected because the Ingress rule matched host `mana.local`, not the raw IP
- `curl -H "Host: mana.local" http://10.42.1.50` returned the correct Streamlit HTML
- This proved the Ingress rule was working correctly

## Local Name Resolution
- Added `mana.local` to `/etc/hosts` on Nobara
- This allows the browser to send the correct Host header for local testing

## Mental Model
- Ingress matches on host
- Service forwards traffic to the target port on the Pod
- Pod runs the same container that was tested locally
