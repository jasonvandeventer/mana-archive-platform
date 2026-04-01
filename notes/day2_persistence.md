# Day 2 – Persistence (PVC)

## Purpose
Add persistent storage to the application so data survives Pod restarts.

## Problem
- The app uses SQLite stored at `/app/data/mana_archive.db`
- Without persistence, data lives in the container filesystem
- If the Pod is recreated, all data is lost

## Solution
- Create a PersistentVolumeClaim (PVC)
- Mount it into the container at `/app/data`

## PVC Configuration
- Name: mana-archive-data
- Access mode: ReadWriteOnce
- Storage requested: 1Gi

## Deployment Changes
Added:

- volumeMount:
  - mountPath: /app/data

- volume:
  - linked to PVC

## Important Behavior
- Mounting the PVC replaces the container’s `/app/data` directory
- Existing in-container data is NOT automatically migrated
- A new database is created on the mounted volume

## Validation Steps
1. Started app → database created on PVC
2. Added test data through UI
3. Deleted Pod manually
4. Kubernetes recreated Pod
5. Data still present after restart

## Result
Persistence is working correctly.

## Key Insight
- Container filesystem = ephemeral
- PersistentVolume = durable storage
- Application must write to the mounted path to persist data

## Limitations
- SQLite is not suitable for multi-replica scaling
- Current setup supports a single Pod only

## Next Considerations
- Backups (not implemented)
- Database migration strategy (future)
- Potential move to a managed database if scaling is required
