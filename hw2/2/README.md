# Secret vs ConfigMap


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
