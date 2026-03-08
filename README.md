# ⎈ Helm CRUD App

A production-ready Helm chart blueprint for deploying full-stack applications on Kubernetes — covering deployments, services, configuration management, secrets, and RBAC out of the box.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Why This Project](#why-this-project)
- [Who Is This For](#who-is-this-for)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Install the Chart](#install-the-chart)
  - [Upgrade the Chart](#upgrade-the-chart)
  - [Uninstall the Chart](#uninstall-the-chart)
- [RBAC — Read-Only Access](#rbac--read-only-access)
- [Configuration](#configuration)
- [Useful Helm Commands](#useful-helm-commands)
- [Extending This Blueprint](#extending-this-blueprint)
- [Contributing](#contributing)

---

## Overview

This repository provides a **Helm chart** to deploy a full-stack CRUD application onto a Kubernetes cluster. By packaging Kubernetes manifests as a Helm chart, it enables:

- Templated, reusable Kubernetes configurations
- Easy parameterization via `values.yaml`
- Simple install, upgrade, and rollback lifecycle management
- RBAC support with a dedicated read-only role manifest

---

## Why This Project

Managing raw Kubernetes YAML files works — but it doesn't scale. As applications grow, you end up copy-pasting manifests, manually tracking environment differences, and struggling with versioning.

This project solves that by introducing **Helm** as the deployment layer. It serves as a **minimal but complete blueprint** that demonstrates how to:

- Package a multi-tier app (frontend + backend + database) into a single, installable Helm chart
- Separate concerns cleanly: deployment logic lives in templates, environment config lives in `values.yaml`, and sensitive data lives in Secrets
- Apply Kubernetes best practices from day one — including RBAC, ConfigMaps, and proper service exposure
- Manage the full application lifecycle (install → upgrade → rollback → teardown) using a single tool

Think of this as the **"Hello World" of Helm** — simple enough to understand end-to-end, but structured the way real production charts are built.

---

## Who Is This For

This project is a practical reference for:

- **Developers** learning how to containerize and deploy apps on Kubernetes using Helm
- **DevOps engineers** looking for a clean starting point to scaffold new application charts
- **Teams** migrating from raw `kubectl apply` workflows to a repeatable, versioned Helm-based deployment pipeline
- **Students & practitioners** studying Kubernetes patterns like ConfigMaps, Secrets, Services, and RBAC in a real project context

---

## Prerequisites

Make sure the following are installed and configured:

- [kubectl](https://kubernetes.io/docs/tasks/tools/) — connected to a running cluster
- [Helm v3](https://helm.sh/docs/intro/install/)
- A running Kubernetes cluster (e.g. [Minikube](https://minikube.sigs.k8s.io/), [Kind](https://kind.sigs.k8s.io/), EKS, GKE, AKS)

---

## Project Structure

```
helmcrud/
├── helmcrud/                  # Helm chart directory
│   ├── Chart.yaml             # Chart metadata (name, version, description)
│   ├── values.yaml            # Default configuration values
│   └── templates/             # Kubernetes manifest templates
│       ├── deployment.yaml    # App deployment
│       ├── service.yaml       # Service definition
│       ├── configmap.yaml     # App configuration
│       └── secret.yaml        # Sensitive credentials
├── read-only.yaml             # RBAC: ClusterRole + binding for read-only access
└── README.md
```

---

## Getting Started

### Install the Chart

```bash
helm install helmcrud ./helmcrud
```

To override default values at install time:

```bash
helm install helmcrud ./helmcrud \
  --set mysql.password=mysecretpassword \
  --set replicaCount=2
```

Or supply a custom values file:

```bash
helm install helmcrud ./helmcrud -f my-values.yaml
```

Verify the deployment:

```bash
kubectl get pods
kubectl get services
```

---

### Upgrade the Chart

After making changes to templates or values:

```bash
helm upgrade helmcrud ./helmcrud
```

---

### Uninstall the Chart

```bash
helm uninstall helmcrud
```

---

## RBAC — Read-Only Access

The `read-only.yaml` file defines a **ClusterRole** and **ClusterRoleBinding** to grant read-only (view) access to cluster resources. This is useful for CI pipelines, monitoring tools, or team members who need visibility without write permissions.

Apply it with:

```bash
kubectl apply -f read-only.yaml
```

To remove it:

```bash
kubectl delete -f read-only.yaml
```

---

## Configuration

The following values can be configured in `helmcrud/values.yaml` or overridden at install time:

| Key                   | Default        | Description                              |
|-----------------------|----------------|------------------------------------------|
| `replicaCount`        | `1`            | Number of app pod replicas               |
| `image.repository`    | `your-image`   | Docker image for the app                 |
| `image.tag`           | `latest`       | Image tag                                |
| `service.type`        | `ClusterIP`    | Kubernetes service type                  |
| `service.port`        | `80`           | Service port                             |
| `mysql.host`          | `mysql`        | MySQL service hostname                   |
| `mysql.database`      | `cruddb`       | Database name                            |
| `mysql.user`          | `root`         | MySQL user                               |
| `mysql.password`      | `changeme`     | MySQL password (override in production)  |

> ⚠️ **Never commit real credentials.** Use Kubernetes Secrets or a secrets manager in production.

---

## Useful Helm Commands

```bash
# Preview rendered templates without installing
helm template helmcrud ./helmcrud

# Lint the chart for errors
helm lint ./helmcrud

# Check release history
helm history helmcrud

# Rollback to a previous release
helm rollback helmcrud 1

# List all installed releases
helm list
```

---

## Extending This Blueprint

This chart is intentionally kept minimal so it's easy to understand and adapt. Here are some natural next steps for building on top of it:

| Enhancement | How |
|---|---|
| **Ingress support** | Add an `ingress.yaml` template and toggle it via `values.yaml` |
| **Horizontal Pod Autoscaling** | Add an `hpa.yaml` template with CPU/memory thresholds |
| **Persistent storage** | Add a `PersistentVolumeClaim` for the MySQL deployment |
| **Health checks** | Add `livenessProbe` and `readinessProbe` to the deployment template |
| **Multi-environment deploys** | Create `values-dev.yaml`, `values-staging.yaml`, `values-prod.yaml` |
| **CI/CD integration** | Run `helm upgrade --install` in a GitHub Actions or Jenkins pipeline |
| **External secrets** | Integrate with [External Secrets Operator](https://external-secrets.io/) or HashiCorp Vault |

---

## Contributing

Contributions are welcome!

1. Fork the repository
2. Create a new branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add your feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

---

> Built with ⎈ Helm — the package manager for Kubernetes.
