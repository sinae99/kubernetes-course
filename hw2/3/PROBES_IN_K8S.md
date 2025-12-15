# Probes (start+readi+live)

## What Probes Are Made Of

> A probe is **configuration**, not a component.

A probe always consists of **two parts**.

### 1. Methods (How Kubernetes checks)

Exactly **three mechanisms** exist:

| Method | What Kubernetes does |
|------|----------------------|
| `httpGet` | Sends HTTP request to container |
| `tcpSocket` | Attempts TCP connection |
| `exec` | Runs a command inside container |

Result is always `binary`:
- success
- failure

---

### 2. Timing (How patient Kubernetes is)

| Field | Meaning |
|----|--------|
| `initialDelaySeconds` | wait before first check |
| `periodSeconds` | interval between checks |
| `timeoutSeconds` | max wait per check |
| `successThreshold` | successes required |
| `failureThreshold` | failures before action |

> Probes have **no logic**.  
> Only timing and thresholds.

---

## Where Probes Live

> Probes live **inside the Pod spec**, attached to a **container**.

- Defined in Pod YAML
- Stored in the API server
- Read by **kubelet**
- Executed on the **node**

They are:
- not Pods
- not containers
- not processes

> Probes exist only as **instructions for kubelet**.

---

## Who Runs and Interprets Probes

> **kubelet** does everything.

| Step | Responsible |
|----|------------|
| Reads probe config | kubelet |
| Executes probe | kubelet |
| Interprets result | kubelet |
| Takes action | kubelet |

Scheduler and controllers are **not involved**.

---

## What Probes Talk To

> Probes talk **only to the container**.

They observe:
- container process
- container network
- container filesystem

They do **not** interact with:
- Services
- Ingress
- API server
- Other Pods

---

## The Three Probes

> Kubernetes supports **exactly three probes**.

| Probe | Question |
|----|---------|
| `startupProbe` | Has the app finished starting? |
| `readinessProbe` | Can this Pod receive traffic? |
| `livenessProbe` | Should this container be restarted? |

Same structure.  
Different reactions.

---

## startupProbe

> **Used only during startup.**

### What it is for
- Slow boots
- Heavy initialization
- Migrations
- Warm-up

### What failure means
- App is still starting
- Kubernetes waits

### What happens if it keeps failing
- Container is restarted

### Key rule
> While `startupProbe` is failing,  
> `livenessProbe` is disabled.

---

## readinessProbe

> **Controls traffic flow.**

### What it is for
- Deciding if Pod should receive requests

### What failure means
- Pod is removed from Service endpoints

### What does NOT happen
- Container is not restarted

### Typical usage
- Dependency not ready
- Temporary overload
- Graceful shutdown

---

## livenessProbe

> **Controls restarts.**

### What it is for
- Detecting stuck or broken applications

### What failure means
- Container is killed
- Container is restarted

### Typical usage
- Deadlocks
- Infinite loops
- Unrecoverable internal states

---

## Usage Summary

| Probe | Affects traffic | Restarts container | Startup only |
|----|----------------|-------------------|-------------|
| startupProbe | ❌ | ✅ (eventually) | ✅ |
| readinessProbe | ✅ | ❌ | ❌ |
| livenessProbe | ❌ | ✅ | ❌ |

---

## Final Mental Model

> **startupProbe** → give me time  
> **readinessProbe** → don’t send traffic  
> **livenessProbe** → restart me

---
