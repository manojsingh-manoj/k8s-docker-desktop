# Create User and User Context in Docker Desktop

This guide shows how to create a user context in kubernetes runnning in docker desktop. Here we are creating kubernetes ServiceAccount with limited access to a specific namespace, generate a token for it, and configure a `kubeconfig` context for user-like access.

---

## Prerequisites

- Kubernetes cluster (e.g., Docker Desktop, Kind, etc.)
- `kubectl` installed
- Access to your current `~/.kube/config`

---

## Step 1: Create a Namespace & ServiceAccount

```bash
kubectl create namespace my-namespace

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: myuser
  namespace: my-namespace
EOF

## Step 2: Create a Role & RoleBinding


cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: usercontext-reader-example
  namespace: usercontext-example
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
EOF


```bash

cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-read-access
  namespace: my-namespace
subjects:
- kind: ServiceAccount
  name: myuser
  namespace: usercontext-example
roleRef:
  kind: Role
  name: usercontext-reader-example
  apiGroup: rbac.authorization.k8s.io
EOF

## Step 2: : Create role and rolebinding and generate a Token for the ServiceAccount

```bash
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml

kubectl -n my-namespace create token myuser

## Step 2: : Create role and rolebinding and generate a Token for the ServiceAccount

```bash
kubectl config set-credentials myuser \
  --token=<paste-your-token-here generated from previous step>

Setup your context with the user you created previously, myuser-context name can be anything. 

kubectl config set-context myuser-context \
  --cluster=docker-desktop \
  --user=myuser \
  --namespace= usercontext-example

kubectl config use-context myuser-context

## CleanUp
kubectl config get-contexts
kubectl config delete-context myuser-context
kubectl config get-users
kubectl config delete-user myuser
