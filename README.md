# Homelab Infrastructure

This repository contains the infrastructure as code for my homelab environment, managed using Flux CD for GitOps-driven deployments to Kubernetes.

## Overview

This homelab setup runs on Proxmox with Kubernetes as the container orchestration platform. The infrastructure is defined using a combination of:

- Terraform/Terramate for provisioning infrastructure
- Kubernetes manifests for workload definitions
- Flux CD for continuous deployment from Git
- Kustomize for environment-specific configurations

## Repository Structure

```
homelab/
├── infrastructure/                 # Infrastructure provisioning code
│   ├── terraform/                  # Terraform modules and configurations
│   │   ├── proxmox/                # Proxmox VM definitions
│   │   └── terramate/              # Terramate stacks
│   └── kubernetes/                 # Kubernetes cluster bootstrap
├── cluster/                        # Kubernetes resources managed by Flux
│   ├── base/                       # Base configurations
│   ├── core/                       # Core system components
│   │   ├── namespaces/             # Namespace definitions
│   │   ├── networking/             # Ingress, cert-manager, etc.
│   │   ├── monitoring/             # Prometheus, Grafana, etc.
│   │   └── storage/                # Storage classes, volumes, etc.
│   ├── apps/                       # Application deployments
│   │   ├── development/            # Development environment specific apps
│   │   └── production/             # Production environment specific apps
│   └── config/                     # Configuration for applications
├── docs/                           # Documentation
└── scripts/                        # Utility scripts
```

## Prerequisites

- Proxmox hypervisor
- Kubernetes cluster (k3s or other distribution)
- kubectl
- Flux CLI
- Terraform
- Terramate
- Git

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/homelab.git
cd homelab
```

### 2. Bootstrap the Kubernetes Cluster with Flux

```bash
# Export your GitHub personal access token
export GITHUB_TOKEN=<your-token>

# Bootstrap Flux
flux bootstrap github \
  --owner=<your-github-username> \
  --repository=homelab \
  --branch=main \
  --path=cluster \
  --personal
```

### 3. Provision Infrastructure with Terraform

```bash
cd infrastructure/terraform
terramate run -- terraform init
terramate run -- terraform apply
```

## Core Components

This homelab setup includes the following core components:

- **Networking**: Ingress controllers, cert-manager for SSL certificates
- **Monitoring**: Prometheus and Grafana for metrics and visualization
- **Storage**: Local storage classes and persistent volumes
- **Databases**: PostgreSQL and ClickHouse for data storage
- **GitOps**: Flux for continuous delivery from Git

## Infrastructure Management

### Proxmox VMs

VMs are provisioned on Proxmox using Terraform. The configuration can be found in `infrastructure/terraform/proxmox/`.

### Kubernetes Resources

All Kubernetes resources are defined in the `cluster/` directory and are applied using Flux CD according to the GitOps methodology.

## Monitoring and Observability

The monitoring stack consists of:

- Prometheus for metrics collection
- Grafana for visualization
- Alertmanager for alerting

Access Grafana at: `https://grafana.yourdomain.local`

## Backup and Disaster Recovery

Regular backups are configured for:

- Proxmox VMs
- Persistent volumes
- Database data

Backup configurations can be found in `cluster/core/backup/`.

## Flux Sync Configuration

Flux is configured to sync the repository on a regular schedule and to automatically apply changes when new commits are pushed to the repository.

The Flux configuration is defined in `cluster/base/flux-system/`.

## Development Workflow

1. Make changes to the repository
2. Commit and push to the main branch
3. Flux automatically detects changes and applies them to the cluster
4. Monitor the sync process with `flux get kustomizations --watch`

## Troubleshooting

Common issues and their solutions:

- **Flux sync failures**: Check `flux logs`
- **Pod scheduling issues**: Check node resources with `kubectl describe node`
- **Storage issues**: Verify PV/PVC status with `kubectl get pv,pvc`

For more detailed troubleshooting, refer to the documentation in the `docs/` directory.

## Contributing

1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature`
3. Make your changes
4. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
