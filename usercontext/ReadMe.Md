# Kubernetes Namespace Access with ServiceAccount & Custom Context

This guide shows how to create a Kubernetes ServiceAccount with limited access to a specific namespace, generate a token for it, and configure a `kubeconfig` context for user-like access.

---

## Prerequisites

- Kubernetes cluster (e.g., Docker Desktop, Kind, etc.)
- `kubectl` installed
- Access to your current `~/.kube/config`

---

## ✅ Step 1: Create a Namespace & ServiceAccount

```bash
kubectl create namespace my-namespace

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myuser
  namespace: my-namespace
EOF
