# crictl - ctr 
what is happening inside the container runtime layer? ----->  **crictl** and **ctr**.  

Both interact with containerd, but not in the same way, and learning the difference between them clarifies how Kubernetes actually runs workloads.

---

## crictl – The Kubernetes Runtime View

The kubelet never talks directly to containerd.  
Instead, it communicates through the **Container Runtime Interface (CRI)**.  
Anything the kubelet knows about pods, containers, or images comes from this interface.

`crictl` is a debugging tool that speaks the same CRI language as kubelet.  
Because of that, `crictl` shows the world exactly as Kubernetes sees it:

- pods represented as CRI sandboxes  
- containers inside those pods  
- images available to kubelet  
- logs and configuration for containers  

When a pod fails to start or a container won’t run, `crictl` is the tool that exposes what kubelet is experiencing.  
It does not show low‑level containerd details—only the Kubernetes‑level view defined by CRI.

### Example usage:
```bash
k3s crictl pods
k3s crictl ps
k3s crictl images
k3s crictl inspect <CONTAINER-ID>
k3s crictl logs <CONTAINER-ID>
```

---

## ctr – The Containerd Internal View

While `crictl` shows Kubernetes’ perspective, `ctr` shows containerd’s internal state.  
It does **not** understand pods or Kubernetes objects.  
It exposes namespaces, raw container tasks, image metadata, snapshots, and everything containerd manages internally.

In air‑gapped installations, K3s relies heavily on containerd importing images from the local bundle.  
Understanding what containerd loaded, how images are stored, or what tasks are running is done through `ctr`.

`ctr` is the tool you use when you want to look underneath kubelet and inspect the runtime’s raw operations.

### Example usage:
```bash
k3s ctr namespaces ls
k3s ctr images ls
k3s ctr containers ls
k3s ctr images info <IMAGE>
k3s ctr tasks ls
```

---

## How They Fit Together

Kubelet gives orders.  
CRI translates them.  
Containerd executes them.

This is the chain:

```
kubelet → CRI → containerd → containers
```

`crictl` plugs into the CRI layer, showing what kubelet sees.  
`ctr` plugs directly into containerd, showing what the runtime sees.  

As you experiment with Kubernetes, these two perspectives let you understand both the high‑level orchestration and the low‑level mechanics powering your containers.

---

## When To Use Which Tool

Use **crictl** when you want to understand pod behavior, container statuses, logs, or images from Kubernetes’ point of view.

Use **ctr** when you want to inspect containerd internals, imported images, namespaces, or runtime tasks—especially useful in air‑gapped setups where containerd imports images from local bundles instead of pulling from a registry.

Both tools complement each other, each revealing a different layer of how workloads are actually running on your node.

