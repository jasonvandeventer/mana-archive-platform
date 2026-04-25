# Mana Archive Platform Roadmap

## Current Platform State

The platform is no longer just a lab experiment. It is now the operational foundation for Mana Archive.

Completed foundation:

- K3s cluster deployed and stable
- Argo CD installed and managing application deployment
- GitOps workflow functional
- Mana Archive deployed from manifests
- Longhorn storage configured
- Persistent application data migrated onto Longhorn
- Longhorn backups validated
- Argo CD Image Updater integrated for application image promotion
- Mana Archive running as the primary real workload

Current platform purpose:

> Run Mana Archive reliably, make releases repeatable, protect application data, and turn the homelab into a credible Platform Engineering portfolio project.

---

## Roadmap Triage Rules

New ideas should be classified before implementation:

- **Blocking operational issue:** fix immediately if it affects availability, data integrity, backups, GitOps deployment, or cluster stability.
- **Same-phase improvement:** implement only if it directly supports the current platform phase.
- **Roadmap item:** capture it under the correct future phase and continue the current work.
- **Distraction:** defer if it is interesting but does not improve reliability, observability, security, developer experience, or career storytelling.

Current platform priority:

1. Keep the current K3s + Argo CD + Longhorn foundation stable.
2. Add observability so failures are visible and explainable.
3. Improve operational runbooks and recovery validation.
4. Add security controls gradually.
5. Package the project as a clear Platform Engineering portfolio story.

---

## Phase 1 — Baseline Platform Foundation

**Status:** Complete

Theme: establish a working GitOps-based Kubernetes platform capable of running Mana Archive with persistent storage.

Completed:

- K3s cluster deployed
- Argo CD installed and working
- GitOps workflow functional
- Application deployed through Kubernetes manifests
- Longhorn storage configured
- Persistent volume attached to Mana Archive
- Data migrated from prior container deployment
- Longhorn backup target configured
- Backup and restore flow validated
- Application versioning/tagging workflow established
- Argo CD Image Updater integrated

Acceptance goal:

> Mana Archive runs on the K3s platform with persistent data, recoverable storage, and Git-managed deployment state.

---

## Phase 2 — Release Automation & GitOps Hygiene

**Status:** Mostly complete / needs documentation polish

Theme: make application releases repeatable and understandable.

Completed or in progress:

- GitHub Actions builds Mana Archive image
- Image pushed to GHCR
- Version tags used for application releases
- Argo CD tracks platform manifests
- Argo CD Image Updater updates application image tags
- Application deployment separated from manual kubectl edits

Remaining work:

- Document normal release flow
- Document beta release flow
- Document manual rollback flow
- Document how Image Updater selects tags
- Document when to manually pin a version
- Capture screenshots of Argo CD application health and sync state

Recommended docs:

- `docs/release-flow.md`
- `docs/rollback.md`
- `docs/argocd-image-updater.md`

Acceptance goal:

> A release can be built, tagged, promoted, deployed, validated, and rolled back using documented steps.

---

## Phase 3 — Observability Foundation

**Status:** Next major platform phase

Theme: make the platform visible. If something fails, you should be able to answer what happened, where it happened, and whether it affected the app.

### Metrics — Prometheus

Core work:

- Deploy Prometheus stack via Helm
- Collect Kubernetes cluster metrics
- Collect node metrics
- Collect pod/container metrics
- Validate metrics from kube-state-metrics and node exporter

Future application metrics:

- Add Mana Archive health endpoint metrics
- Add request count / latency metrics
- Add import/cache timing metrics
- Add basic business metrics if useful later

Acceptance goal:

> You can see cluster, node, pod, and workload health in metrics instead of guessing from kubectl output.

### Visualization — Grafana

Core work:

- Deploy Grafana
- Connect Prometheus datasource
- Import Kubernetes cluster dashboard
- Import node dashboard
- Build a simple Mana Archive workload dashboard

Useful dashboards:

- Cluster overview
- Node resources
- Namespace workload health
- Mana Archive pod health
- Longhorn storage health

Acceptance goal:

> Grafana can answer: “Is the platform healthy?” and “Is Mana Archive healthy?”

### Logging — Loki + Promtail

Core work:

- Deploy Loki
- Deploy Promtail or Grafana Agent/Alloy if chosen later
- Collect pod logs
- Query Mana Archive logs in Grafana
- Query Argo CD-related logs when needed

Initial log queries to document:

- Mana Archive errors
- Import failures
- Scryfall/cache failures
- Restart/crash behavior
- Argo sync/deploy issues

Acceptance goal:

> You can troubleshoot Mana Archive and platform issues from Grafana logs without hopping randomly between commands.

---

## Phase 4 — Reliability & Operations

**Status:** After observability foundation

Theme: prove the platform can recover from normal failure modes.

### Health Checks & Alerts

Core work:

- Configure application readiness/liveness checks if not already sufficient
- Deploy/configure Alertmanager
- Add alerts for:
  - pod crash loops
  - node not ready
  - disk pressure
  - Longhorn volume degraded
  - backup failures
  - high restart count
  - application unavailable

Acceptance goal:

> Important failures become visible without manually checking everything.

### Backup Expansion

Core work:

- Schedule Longhorn backups
- Define retention policy
- Document backup schedule
- Validate restore into a test path or test namespace
- Document restore decision tree

Acceptance goal:

> Backups are not just configured once; they are scheduled, retained, and periodically validated.

### Recovery Testing

Core work:

- Test pod deletion and recovery
- Test application rollout/rollback
- Test Longhorn volume restore
- Test node reboot behavior
- Test degraded storage scenario if practical

Acceptance goal:

> The platform has documented recovery behavior, not just hopeful assumptions.

Recommended docs:

- `docs/backup-validation.md`
- `docs/restore-runbook.md`
- `docs/incident-runbook.md`
- `docs/platform-health-check.md`

---

## Phase 5 — Security & Access Control

**Status:** After reliability basics

Theme: harden the platform without overcomplicating it.

### Image Security

Core work:

- Integrate Trivy scanning into GitHub Actions
- Decide whether scans warn or block initially
- Document severity threshold
- Document exception process for unavoidable findings

Acceptance goal:

> Images are scanned before deployment, and vulnerability handling is intentional.

### Secrets Management

Current baseline:

- Kubernetes Secrets are acceptable for early internal use but are not a mature secrets strategy.

Progression path:

- Keep current Kubernetes Secrets documented
- Evaluate External Secrets Operator
- Evaluate Vault later only if the complexity is justified
- Avoid adding Vault before the platform needs it

Acceptance goal:

> Secret handling is understood, documented, and has a clear maturity path.

### Network Security

Core work:

- Define namespace boundaries
- Add NetworkPolicies where useful
- Restrict unnecessary pod-to-pod communication
- Protect internal admin services

Acceptance goal:

> The platform is not wide open by default.

### Access Control

Possible options:

- Cloudflare Access for externally exposed internal tools
- Authentik or another OIDC provider later
- Separate public app access from admin platform access

Acceptance goal:

> Administrative services are protected before wider external exposure.

---

## Phase 6 — Platform Documentation & Career Packaging

**Status:** Ongoing, but should become a deliberate phase

Theme: turn the work into a portfolio story that recruiters and engineers can understand.

Core documents:

- Architecture overview
- Repository structure explanation
- Release workflow
- Backup and restore documentation
- Observability walkthrough
- Security posture and roadmap
- Known tradeoffs
- “What I would improve next” section

Portfolio narrative:

> I built a K3s-based internal platform that runs a real application, uses GitOps for deployment, Longhorn for persistent storage and backups, Argo CD Image Updater for release automation, and an observability stack for operational visibility.

Screenshots to capture:

- Argo CD healthy application
- Longhorn volume and backup state
- Grafana cluster dashboard
- Grafana Mana Archive dashboard
- GitHub Actions release run
- GHCR image tags
- Mana Archive running through the platform

Acceptance goal:

> The platform is not only functional; it is explainable in an interview or portfolio review.

---

## Phase 7 — Platform as a Product

**Status:** Future maturity phase

Theme: shift from “my app runs on Kubernetes” to “this platform can support more workloads.”

### Developer Experience

Future work:

- Template application deployment
- Standard app manifest pattern
- Standard environment variable/secrets pattern
- Standard ingress pattern
- Standard persistent volume pattern
- Onboarding guide for a new app

Acceptance goal:

> A second app can be deployed using the same platform patterns without reinventing everything.

### Multi-App Support

Future work:

- Deploy another small internal service
- Validate namespace separation
- Validate resource requests/limits
- Validate monitoring per namespace
- Validate backup needs per app

Acceptance goal:

> The platform proves it is generalizable beyond Mana Archive.

### Self-Service Concepts

Future work:

- App template repo
- Minimal new-service checklist
- GitOps app-of-apps pattern if useful
- Documented platform contract for apps

Acceptance goal:

> The platform starts behaving like an internal developer platform, not just a hand-built cluster.

---

## Phase 8 — Advanced Maturity / Optional Future Work

**Status:** Optional / later

These are useful only after the core platform is stable, observable, documented, and secure enough.

Possible future work:

- External Secrets Operator
- Authentik/OIDC integration
- Cert-manager
- Ingress standardization
- Policy enforcement with Kyverno or OPA Gatekeeper
- Progressive delivery with Argo Rollouts
- Staging namespace/environment
- Disaster recovery rehearsal
- Cost/resource usage dashboards
- SLOs for Mana Archive availability
- Automated dependency updates
- Renovate or Dependabot workflow

Acceptance goal:

> Advanced tools are added because they solve real platform problems, not because they look impressive.

---

## Platform / Mana Archive Work Rhythm

Mana Archive is the product workload. The platform is the operating environment. They should feed each other instead of competing.

Recommended rhythm:

1. Mana Archive feature sprint
2. Platform polish or observability sprint
3. Mana Archive feature sprint
4. Documentation/storytelling sprint
5. Platform security or reliability sprint

Near-term example:

1. Finish Mana Archive v2.1.x stabilization
2. Implement Platform Phase 3 observability
3. Build Mana Archive v2.2.x deck management
4. Write platform release/restore docs
5. Build Mana Archive v2.3.x multi-user/storage
6. Add security scanning and access-control polish

---

## Guiding Principles

- Git is the source of truth.
- Avoid manual cluster drift.
- Prefer boring, documented operations over clever automation.
- Protect application data before chasing new features.
- Observability before advanced complexity.
- Security should mature in layers.
- Platform work should support a real workload.
- Documentation is part of the deliverable.
- The platform should be reproducible from scratch.
- Treat the platform as a product, not just infrastructure.

---

## Immediate Next Steps

1. Finish documenting Argo CD Image Updater and the current release flow.
2. Confirm current platform state after the latest Mana Archive release.
3. Begin Observability Phase 1:
   - Prometheus
   - Grafana
   - Loki/Promtail or equivalent
4. Build first platform dashboards:
   - cluster health
   - node health
   - Mana Archive workload health
   - Longhorn storage health
5. Write the first operational runbooks:
   - release flow
   - rollback flow
   - backup validation
   - restore procedure
6. Alternate back to Mana Archive deck-management work after observability foundation is in place.
