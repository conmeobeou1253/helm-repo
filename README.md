# Infrastructure Helm Charts

This directory contains Helm charts for all infrastructure components:

## 📦 Charts

### 1. ArgoCD
- **Path**: `argocd/`
- **Description**: GitOps continuous delivery tool
- **Namespace**: `argocd`
- **Features**:
  - Single replica (cost-optimized)
  - ClusterIP service
  - Redis standalone (no HA)

### 2. Monitoring
- **Path**: `monitoring/`
- **Description**: Kube-Prometheus-Stack (Prometheus + Grafana + Alertmanager)
- **Namespace**: `monitoring`
- **Features**:
  - Prometheus with 512Mi RAM
  - Grafana with admin/admin123
  - No persistent storage (ephemeral)
  - Single replicas for all components

### 3. Loki
- **Path**: `loki/`
- **Description**: Centralized logging stack
- **Namespace**: `monitoring`
- **Features**:
  - Loki for log aggregation
  - Promtail for log collection
  - Integrates with Grafana
  - No persistent storage

### 4. Ingress-Nginx
- **Path**: `ingress-nginx/`
- **Description**: Nginx Ingress Controller
- **Namespace**: `ingress-nginx`
- **Features**:
  - Single LoadBalancer ($16/month)
  - Admission webhooks disabled (saves resources)
  - Metrics enabled for Prometheus

### 5. Metrics-Server
- **Path**: `metrics-server/`
- **Description**: Kubernetes metrics server for HPA
- **Namespace**: `kube-system`
- **Features**:
  - Enables `kubectl top` commands
  - Required for Horizontal Pod Autoscaling

## 🚀 Deployment Methods

### Method 1: Direct Helm Install (Manual)
```bash
# Update dependencies
cd helm-repo/monitoring
helm dependency update

# Install chart
helm install monitoring . -n monitoring --create-namespace

# Or upgrade existing
helm upgrade --install monitoring . -n monitoring
```

### Method 2: Via ArgoCD (GitOps - Recommended)
```bash
# Apply ArgoCD Application manifests
kubectl apply -f app-repo/argocd-apps/infrastructure.yaml

# Watch deployment
kubectl get applications -n argocd
```

### Method 3: Via Terraform (Infrastructure as Code)
See `learn-terraform-provision-eks-cluster/` directory for Terraform-based deployment.

## 📊 Cost Summary

| Component | Cost | Notes |
|-----------|------|-------|
| Monitoring Stack | $0 | Runs on EKS nodes |
| Loki | $0 | Runs on EKS nodes |
| Metrics Server | $0 | Runs on EKS nodes |
| Ingress-Nginx | **$16/month** | AWS LoadBalancer |
| ArgoCD | $0 | Runs on EKS nodes |
| **Total** | **$16/month** | Excluding EKS cluster cost |

## 🔧 Customization

Each chart has `values.yaml` that can be customized:

```bash
# Custom values
helm install monitoring ./monitoring \
  -n monitoring \
  -f custom-values.yaml
```

Or override via ArgoCD:
```yaml
spec:
  source:
    path: helm-repo/monitoring
    helm:
      values: |
        kube-prometheus-stack:
          grafana:
            adminPassword: myCustomPassword
```

## 📝 Update Dependencies

When Chart.yaml changes, update dependencies:

```bash
cd helm-repo/<chart-name>
helm dependency update
```

## 🎯 Production Considerations

For production deployments, consider:
- Enable persistent storage for Prometheus & Loki
- Increase replica counts for HA
- Add resource limits
- Configure TLS/SSL certificates
- Set up alerting rules
- Enable backup/restore with Velero

## 📚 References

- [Kube-Prometheus-Stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)
- [Loki Stack](https://grafana.com/docs/loki/latest/setup/install/helm/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [Ingress-Nginx](https://kubernetes.github.io/ingress-nginx/)
- [Metrics Server](https://github.com/kubernetes-sigs/metrics-server)
