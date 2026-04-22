# Refactor Kustomize Structure

I have refactored the `msa-config` directory to follow the Kustomize best practices with **base** and **overlays**.

## New Directory Structure

```text
msa-config/
├── k8s/
│   ├── msa-be/
│   │   ├── base/ (Shared configuration)
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   │   └── kustomization.yaml
│   │   └── overlays/ (Environment specific)
│   │       ├── dev/
│   │       ├── stage/
│   │       └── production/
│   ├── msa-bff/ ...
│   └── msa-fe/ ...
└── applications/ (ArgoCD Applications)
    ├── dev/
    ├── stage/
    └── production/
```

## How to Apply Configurations

### 1. Using Kubectl (Direct Apply)
You can apply a specific environment using the `-k` (kustomize) flag:

**Development:**
```bash
kubectl apply -k k8s/msa-be/overlays/dev
kubectl apply -k k8s/msa-bff/overlays/dev
kubectl apply -k k8s/msa-fe/overlays/dev
```

**Staging:**
```bash
kubectl apply -k k8s/msa-be/overlays/stage
kubectl apply -k k8s/msa-bff/overlays/stage
kubectl apply -k k8s/msa-fe/overlays/stage
```

**Production:**
```bash
kubectl apply -k k8s/msa-be/overlays/production
kubectl apply -k k8s/msa-bff/overlays/production
kubectl apply -k k8s/msa-fe/overlays/production
```

### 2. Using ArgoCD
If you are using ArgoCD, you can apply the application manifests which will automatically manage the Kustomize overlays:

```bash
kubectl apply -f applications/dev/
kubectl apply -f applications/stage/
kubectl apply -f applications/production/
```

## Key Changes Made
- Moved all core manifests to `base/` folders.
- Created `kustomization.yaml` in each `base/` to manage resources.
- Created `overlays/` for `dev`, `stage`, and `production`.
- **Dev/Stage**: Added `nameSuffix` (e.g., `-dev`) and `env` labels.
- **Production**: Increased `replicas` to 3 for all deployments using JSON patches.
- Updated ArgoCD application paths to point to the new overlay directories.
