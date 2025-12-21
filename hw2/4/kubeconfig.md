
## What Kubeconfig Is

> kubeconfig is **client-side configuration**, not a Kubernetes resource.

It tells Kubernetes clients:
- where the cluster API is
- how to authenticate
- which credentials to use right now

Default location:
```bash
~/.kube/config
```

The cluster does **not** store or manage kubeconfig.

---

## What a Kubeconfig Is Made Of

> A kubeconfig is just **references and credentials**.

It always contains **four sections**.

| Section | Purpose |
|------|--------|
| `clusters` | Where the API server is and how to trust it |
| `users` | How the client authenticates |
| `contexts` | Which cluster + user pair is selected |
| `current-context` | Active selection |

Minimal shape:
```yaml
apiVersion: v1
kind: Config

clusters: []
users: []
contexts: []
current-context: ""
```

---

## Cluster

> A cluster entry answers: **“Where is the API server and can I trust it?”**

A cluster contains **only connection data**.

### What it defines
- API server URL
- TLS verification material

Example:
```yaml
cluster:
  server: https://10.43.0.1:6443
  certificate-authority-data: <base64-ca>
```

### What the CA is for
- Verifies the API server identity
- Prevents man-in-the-middle attacks

> The CA does **not** encrypt traffic.  
> TLS handles encryption.

---

## User

> A user is **not** a Kubernetes user account.

It is an **authentication method**.

A user answers:
> “How do I prove who I am to the API server?”

---

## User Authentication Methods

Exactly **four categories** exist.

### 1. Client Certificates

Used by:
- k3s
- kubeadm
- control-plane admin access

```yaml
user:
  client-certificate-data: <cert>
  client-key-data: <key>
```

The API server:
- verifies the certificate
- extracts username and groups

---

### 2. Bearer Tokens

Used by:
- service accounts
- CI/CD pipelines
- automation

```yaml
user:
  token: <token>
```

> A token is equivalent to a password.

---

### 3. Exec-Based Authentication

> Credentials are generated **at runtime**.

Used by:
- AWS EKS
- GKE
- AKS
- OIDC / SSO systems

```yaml
user:
  exec:
    command: aws
    args:
      - eks
      - get-token
      - --cluster-name
      - prod
```

The client:
- runs the command
- receives a short-lived token
- uses it immediately

---

### 4. Auth-Provider (Legacy)

Deprecated in favor of `exec`.

---

## Context

> A context is **selection**, not power.

A context binds:
- one cluster
- one user
- optional default namespace

Example:
```yaml
context:
  cluster: prod
  user: prod-admin
  namespace: default
```

Contexts:
- do not authenticate
- do not authorize
- only choose

---

## current-context

> This is the **active pointer**.

It tells the client:
> “Use this context right now.”

Switching context:
- changes credentials in use
- does not modify clusters or users

---

## Where Kubeconfig Comes From

Kubeconfig is assembled by **multiple actors**.

| Actor | What they provide |
|----|----------------|
| Cluster installers | CA, admin certs, bootstrap config |
| Cloud providers | Exec-based auth configs |
| Humans | Contexts, merging, selection |
| Automation | Tokens or mounted configs |

---

## Key Rules to Remember

- kubeconfig is **client-only**
- clusters and users are independent
- contexts only bind references
- deleting kubeconfig does **not** affect the cluster

---

## Mental Model

> kubeconfig is a **connection profile**,  
> not an access control system.

RBAC decides **what you can do**.  
kubeconfig decides **how you connect**.
