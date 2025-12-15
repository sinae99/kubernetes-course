# Secret vs ConfigMap


## Technical Differences

---

## Storage in etcd

| Aspect | ConfigMap | Secret |
|-----|----------|--------|
| Stored in etcd | ✅ | ✅ |
| Data format | Plain text | Base64-encoded |
| Encrypted at rest (default) | ❌ | ❌ |
| Supports encryption config | ❌ | ✅ |

> Without etcd encryption enabled, **both are readable by cluster admins**.

---

## API Server Handling

> Both go through the **same Kubernetes API path**, but with different object types.

| Detail | ConfigMap | Secret |
|-----|----------|--------|
| API resource | `configmaps` | `secrets` |
| Validation | Minimal | Slightly stricter |
| Size limit | ~1MB | ~1MB |
| Binary data | ❌ | ✅ (`data` field) |

> **Secret supports binary payloads**  
> ConfigMap is text-oriented only.

---

## Volume Mount Behavior

> When mounted as volumes, the mechanics are identical.

| Feature | ConfigMap | Secret |
|-----|----------|--------|
| Volume type | `configMap` | `secret` |
| Files created | One per key | One per key |
| File permissions | `0644` | `0644` (can be tightened) |
| Ownership | root:root | root:root |

> The filesystem does **not care** which one it is.


---

## Caching & Performance

> Both are cached by kubelet.

| Detail | ConfigMap | Secret |
|-----|----------|--------|
| Cached on node | ✅ | ✅ |
| Stored in memory | ❌ | ⚠️ (tmpfs for Secrets) |
| Disk write | Yes | Usually no |

> **Key difference**  
> Secrets are commonly mounted using **tmpfs**  
> → never written to disk on the node.

---

## RBAC & Access Control

| Area | ConfigMap | Secret |
|----|----------|--------|
| RBAC verbs | Same | Same |
| Default policies | Relaxed | Often restricted |
| Least-privilege expectation | Medium | High |

> Kubernetes assumes **Secrets need stricter control**, even if mechanics are similar.

---

## Type System (Secrets Only)

> Secrets have **built-in typing**.

| Secret Type | Purpose |
|------------|--------|
| `Opaque` | Generic |
| `kubernetes.io/tls` | TLS certs |
| `kubernetes.io/dockerconfigjson` | Registry auth |
| `kubernetes.io/service-account-token` | SA tokens |

> ConfigMap has **no types**.

---

## Security Reality

> Kubernetes does **not** magically secure Secrets.

- Secrets are:
  - Base64-encoded
  - API-readable
  - Admin-accessible
- Real security requires:
  - etcd encryption
  - RBAC discipline
  - External secret managers

---

## Hard Technical Summary

> Same delivery mechanism  
> Same Pod interface  
> Same kubelet logic  

### The **real technical differences** are:

- Secret supports **binary data**
- Secret supports **tmpfs mounting**
- Secret supports **types**
- Secret integrates with **etcd encryption**
- Secret is **treated as higher-risk** by the control plane

---


## Theorical Differences

> **Both Secret and ConfigMap exist to inject data into Pods — nothing more, nothing less.**

They solve **the same technical problem**,  
but they solve it for **different types of data**.

---

## What They Are Doing

> From a running Pod’s perspective, **there is almost no difference**.

| Capability | ConfigMap | Secret |
|---------|----------|--------|
| Kubernetes object | ✅ | ✅ |
| Key–value storage | ✅ | ✅ |
| Inject as env vars | ✅ | ✅ |
| Mount as files | ✅ | ✅ |
| Namespaced | ✅ | ✅ |
| Referenced in Pod spec | ✅ | ✅ |

---

## ConfigMap — What It Does

> **ConfigMap exists to store application configuration that is safe to be visible.**

### What kind of data lives here

| Type | Examples |
|----|---------|
| App config | `APP_ENV=prod` |
| URLs | `API_BASE_URL=https://api.internal` |
| Feature flags | `FEATURE_X=true` |
| Tuning values | timeouts, limits |
| Logging | log level, format |

### How Kubernetes treats it

- Stored as **plain text**
- Easily readable:
  ```bash
  kubectl get configmap -o yaml
  ```
- Designed to be:
  - Committed to Git
  - Reviewed
  - Shared between teams

> **ConfigMap = configuration that changes behavior, not identity**

---

## Secret — What It Does

> **Secret exists to store data that must not be exposed casually.**

### What kind of data lives here

| Type | Examples |
|----|---------|
| Credentials | DB username/password |
| Tokens | API keys |
| Auth material | TLS certs, private keys |
| Access secrets | OAuth secrets |

### How Kubernetes treats it

- Stored as **base64-encoded**
- Protected via:
  - RBAC
  - Optional etcd encryption
- Less visible by default
- Meant to reduce:
  - Accidental leaks
  - Logging exposure
  - Casual inspection


---

## Why They Are Separated

> Kubernetes **intentionally split them** to enforce human behavior, not technical behavior.

| Reason | Explanation |
|----|------------|
| Safety | Prevent secrets from ending up in Git |
| Access control | Tighter RBAC for sensitive data |
| Intent | Make developers think before storing data |
| Auditing | Clear separation of risk |

> **Same mechanism, different intent**

---

## Side-by-Side Responsibility

| Question | ConfigMap | Secret |
|-------|----------|--------|
| Is this safe if leaked? | Yes | No |
| Can it be public-ish? | Yes | No |
| Should it be in Git? | Yes | Usually no |
| Is this identity or access? | No | Yes |
| Is this behavior/config? | Yes | No |

---

## Mental Model (Sticky)

> **ConfigMap = how the app behaves**  
> **Secret = who the app is**

---
