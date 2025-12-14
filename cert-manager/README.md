# Cert-Manager Installation Guide

## Overview
All resources (CustomResourceDefinitions, cert-manager, cainjector, and webhook components) are included in a single YAML manifest file.

---

## Installation Steps

### 1. Install cert-manager components

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.2/cert-manager.yaml
```

> **Note:** By default, cert-manager will be installed into the `cert-manager` namespace. It is possible to run cert-manager in a different namespace, although you'll need to make modifications to the deployment manifests.

---

### 2. Verify Installation

Check the cert-manager namespace for running pods:

```bash
kubectl get pods --namespace cert-manager
```

**Expected output:**
```
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-5c6866597-zw7kh               1/1     Running   0          2m
cert-manager-cainjector-577f6d9fd7-tr77l   1/1     Running   0          2m
cert-manager-webhook-787858fcdb-nlzsq      1/1     Running   0          2m
```

You should see the following pods in a `Running` state:
- `cert-manager`
- `cert-manager-cainjector`
- `cert-manager-webhook`

> **Note:** The webhook might take a little longer to successfully provision than the others.

---

### 3. Additional Resources

- [Traefik cert-manager User Guide](https://doc.traefik.io/traefik/v3.4/user-guides/cert-manager/)

- [cert-manager ACME with NGINX Ingress](https://cert-manager.io/docs/tutorials/acme/nginx-ingress/#step-5---deploy-cert-manager)