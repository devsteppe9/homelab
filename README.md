# Homelab Kubernetes Cluster

A personal Kubernetes homelab managed with GitOps principles using ArgoCD. This repository contains Kubernetes manifests for self-hosted applications and supporting infrastructure.

## 🚀 Overview

### Technology Stack

- **Platform:** Kubernetes
- **GitOps:** ArgoCD
- **Storage:** HostPath persistent volumes
- **Networking:** Mix of LoadBalancer, NodePort, and ClusterIP services

## 📋 Prerequisites

Before deploying this homelab, ensure you have:

- A running Kubernetes cluster (single node or multi-node)
- `kubectl` installed and configured to access your cluster
- Adequate storage for persistent volumes
- **Network Access:**
  - Ability to expose LoadBalancer or NodePort services
  - Optional: Multus CNI for secondary network interfaces

## 🛠️ Initial Setup

### 1. Install ArgoCD

Create the ArgoCD namespace and install the core components:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Apply Service Patch

Patch the ArgoCD server service to change it from ClusterIP to LoadBalancer, exposing the web UI on port 8080:

```bash
kubectl replace -f service-patch.yml -n argocd
```

### 3. Access ArgoCD

Retrieve the initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath={.data.password} | base64 -d
```

**Login credentials:**
- Username: `admin`
- Password: `<output from above command>`

**⚠️ Security Note:** Change the default password immediately after first login.

Access the ArgoCD UI via the LoadBalancer IP on port 8080.

## 📦 Applications

Applications are organized in the `apps/` directory. Each application has its own namespace and configuration files.

**Deploy all applications:**

```bash
# Apply all manifests
kubectl apply -R -f apps/
```

**Deploy individual applications:**

```bash
# Deploy specific application
kubectl apply -f apps/<app-name>/
```

## 🔄 Deployment Order

For a clean deployment, ensure dependencies are deployed first before dependent services.

## 🌐 Accessing Services

| Service | Type | Port | Access |
|---------|------|------|--------|
| ArgoCD UI | LoadBalancer | 8080 | `http://<loadbalancer-ip>:8080` |

Check service endpoints for other applications:

```bash
kubectl get svc -A
```

## 🔍 Troubleshooting

### Check Pod Status

```bash
# Check all pods
kubectl get pods -A

# Check specific namespace
kubectl get pods -n <namespace>
```

### View Logs

```bash
# View logs for a specific pod
kubectl logs -n <namespace> <pod-name> -f

# View logs by label
kubectl logs -n <namespace> -l app=<app-label> -f
```

### Verify Services

```bash
# Check service endpoints
kubectl get svc -A

# Describe a service
kubectl describe svc -n <namespace> <service-name>
```

### Common Issues

1. **Pods not starting:** Check dependencies are running first
2. **Storage issues:** Verify hostPath directories exist and have correct permissions
3. **Connection errors:** Check service DNS and network connectivity

## 🔐 Security Considerations

**Important security improvements to make:**

1. **Change default passwords** in application ConfigMaps
2. **Use Kubernetes Secrets** instead of ConfigMaps for sensitive data
3. **Update storage paths** from `/tmp/` to secure persistent directories
4. **Add resource limits** to prevent resource exhaustion
5. **Configure SSL/TLS** for external-facing services
6. **Review network policies** to restrict inter-pod communication

## 💾 Storage Management

Current storage configuration uses hostPath volumes.

**Recommendations:**
- Move from `/tmp/` to persistent directories (e.g., `/mnt/homelab/`)
- Consider NFS or other network storage for multi-node clusters
- Implement regular backup procedures
- Monitor disk usage regularly

## 🚧 Future Enhancements

- [ ] Add resource requests and limits to all deployments
- [ ] Implement proper Secret management (e.g., Sealed Secrets, External Secrets Operator)
- [ ] Configure Ingress controller for unified access with custom domains
- [ ] Add SSL/TLS certificates (Let's Encrypt with cert-manager)
- [ ] Set up monitoring (Prometheus + Grafana)
- [ ] Implement automated backups
- [ ] Configure network policies for enhanced security
- [ ] Add more applications as needed

## 📝 Maintenance

### Update Application Images

```bash
# Edit deployment files to update image tags
kubectl apply -f apps/<app-name>/

# Or use kubectl set image
kubectl set image deployment/<deployment-name> -n <namespace> \
  <container-name>=<new-image>:<tag>
```

### Backup Databases

```bash
# Example: Backup PostgreSQL database
kubectl exec -n <namespace> <postgres-pod> -- \
  pg_dump -U <user> <database> > backup-$(date +%Y%m%d).sql
```

## 📄 License

This is a personal homelab configuration. Feel free to use and modify for your own purposes.

## 🤝 Contributing

This is a personal project, but suggestions and improvements are welcome via issues or pull requests.