# Mana Archive Platform Roadmap

## Phase 1 — Baseline Platform (Complete)

* K3s cluster deployed
* ArgoCD installed and working
* GitOps workflow functional
* Application deployed via manifests
* Longhorn storage configured
* Backups validated

---

## Phase 2 — Observability (Next)

### Metrics (Prometheus)

* Deploy Prometheus via Helm
* Scrape:

  * Kubernetes metrics
  * Node metrics
  * Application metrics (future)

### Visualization (Grafana)

* Deploy Grafana
* Import:

  * Kubernetes cluster dashboards
  * Node dashboards
* Connect Prometheus datasource

### Logging (Loki)

* Deploy Loki + Promtail
* Collect:

  * pod logs
  * system logs
* Query logs in Grafana

---

## Phase 3 — Reliability & Operations

### Health & Alerting

* Alertmanager setup
* Alerts for:

  * pod crashes
  * node issues
  * disk pressure

### Backups (Expand)

* Scheduled Longhorn backups
* Backup retention policy
* Restore testing

### Scaling & Resilience

* Test pod restarts
* Test node failure scenarios
* Validate recovery behavior

---

## Phase 4 — Security

### Image Security

* Integrate Trivy into GitHub Actions
* Block vulnerable images

### Secrets Management

* Move from Kubernetes Secrets to:

  * External Secrets OR
  * Vault (future)

### Network Security

* NetworkPolicies
* Restrict pod-to-pod communication

---

## Phase 5 — Platform as a Product

### Developer Experience

* Self-service deployment (future)
* Template apps
* Documentation for onboarding

### Multi-App Support

* Deploy additional services
* Validate platform generalization

### Auth Integration

* Integrate OIDC (Authentik / Cloudflare Access)
* Protect internal services

---

## Guiding Principles

* Everything deployed via GitOps
* No manual cluster drift
* Platform is reproducible from scratch
* Treat platform as a product, not just infrastructure

