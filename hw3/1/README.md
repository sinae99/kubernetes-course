### question: what is csr in kubernetes
answer:  
csr is a kubernetes object used to request tls certificates.  
it is mainly used for node and client authentication.  

example:  
kubelet creates a csr to authenticate to the api server.  

summary:  
csr = request → approve → signed certificate

---

### question: what is memory.min in cgroups and why is it 0
answer:  
memory.min protects a minimum amount of memory from reclamation.  
kubernetes does not set it, so it stays at zero.  

example:  
a pod has memory requests but memory.min is still 0.  

summary:  
kubernetes enforces memory limits, not guaranteed floors

---

### question: what do kubernetes qos classes do
answer:  
qos defines pod priority during memory pressure.  
it does not reserve or guarantee memory.  

example:  
besteffort pods are killed before guaranteed pods.  

summary:  
qos controls kill order, not resource reservation

---

### question: why can setting memory.min crash a node
answer:  
it blocks memory reclamation under pressure.  
the system and kubelet may starve.  

example:  
pods are protected while kubelet gets oom-killed.  

summary:  
protected memory everywhere leads to node failure

---
