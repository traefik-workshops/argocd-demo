# Traefik API Gateway with ArgoCD

This repository contains a GitOps-based setup for Traefik API Gateway with observability stack using ArgoCD.

## Repository Structure

```
.
├── apps/                           # ArgoCD Applications
│   ├── api-gateway/               # API Gateway related applications
│   ├── api-management/            # API Management related applications
│   ├── apps/                      # Application deployments
│   ├── observability/             # Observability stack applications
│   │   ├── grafana/              # Grafana deployment
│   │   ├── prometheus/           # Prometheus deployment
│   │   └── tempo/                # Tempo deployment
│   ├── proxy/                     # Proxy configurations
│   └── traefik.yaml              # Main Traefik deployment
└── resources/                     # Kubernetes resources
    ├── api-gateway/              # API Gateway resources
    ├── api-management/           # API Management resources
    ├── apps/                     # Application resources
    └── proxy/                    # Proxy resources

```

## Prerequisites

- Kubernetes cluster
- kubectl
- ArgoCD

## Setup Instructions

1. Create the Traefik namespace and add the license:
   ```bash
   kubectl create namespace traefik
   kubectl create secret generic traefik-hub-license --namespace traefik --from-literal=token=YOUR_LICENSE_TOKEN
   ```

2. Install ArgoCD:
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. Access ArgoCD UI:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
   Then visit: https://localhost:8080

4. Login to ArgoCD (default admin username is 'admin'):
   ```bash
   # Get the initial admin password
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

5. Apply the applications:
   ```bash
   # Apply all applications in the apps directory
   kubectl apply -f apps/
   ```

## Components

### Traefik
- API Gateway with Hub integration
- Dashboard accessible at `http://localhost`
- Metrics, logging, and tracing enabled

### Observability Stack
- **Prometheus**: Metrics collection and storage
- **Tempo**: Distributed tracing backend
- **Grafana**: Visualization platform with pre-configured datasources for Prometheus and Tempo

### API Management
- API Gateway configuration
- API Plans and access control
- API documentation and portal

## Development

To add new components:
1. Add Kubernetes resources under the appropriate directory in `resources/`
2. Create corresponding ArgoCD Application in `apps/`
3. Commit and push changes - ArgoCD will automatically sync the changes
