# Kubernetes Kubeconfig — Technical Overview

## What is kubeconfig
`kubeconfig` is a **client-side YAML configuration file** used by `kubectl` and other Kubernetes clients to authenticate to and communicate with one or more Kubernetes clusters.  
It is **not stored in the cluster** and is never persisted in `etcd`.

Default location:
```bash
~/.kube/config
```

---

## Core Structure
A kubeconfig file is composed of four primary sections:

- **clusters**: Defines Kubernetes API endpoints and trust material (CA)
- **users**: Defines authentication methods (not Kubernetes users)
- **contexts**: Binds a user to a cluster and optional default namespace
- **current-context**: Selects which context is active

Minimal structure:
```yaml
apiVersion: v1
kind: Config

clusters: []
users: []
contexts: []
current-context: ""
```

---

## Cluster Definition
A `cluster` entry specifies:
- API server address
- TLS trust configuration

Example:
```yaml
cluster:
  server: https://10.43.0.1:6443
  certificate-authority-data: <base64-ca>
```

The CA is used to **verify the API server’s identity**, not to encrypt traffic.

---

## User Authentication Methods
A `user` entry defines **how the client authenticates** to the API server. Kubernetes does not manage users internally.

Common methods:
- **Client certificates** (k3s, kubeadm)
- **Bearer tokens** (service accounts, CI/CD)
- **Exec-based auth** (EKS, GKE, AKS, OIDC/SSO)
- **Auth-provider** (legacy, deprecated)

Example (exec-based):
```yaml
user:
  exec:
    command: aws
    args: ["eks", "get-token", "--cluster-name", "prod"]
```

---

## Context
A `context` is a **client-side selector** that maps:
- one cluster
- one user
- optional default namespace

It does not grant permissions.

Example:
```yaml
context:
  cluster: prod
  user: prod-admin
  namespace: default
```

---

## Ownership and Lifecycle
- **Cluster installers** (k3s, kubeadm) generate initial kubeconfigs
- **Cloud providers** generate exec-based kubeconfigs
- **Admins and automation tools** extend and manage contexts and users

Deleting a kubeconfig only removes **client access**; it does not affect the cluster.

---

## Key Principles
- kubeconfig is **client-only**
- Authentication is external; authorization is handled via RBAC
- Contexts select credentials, they do not contain them
- One kubeconfig can manage many clusters and users
