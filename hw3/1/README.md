### ***what is csr in kubernetes***
  
csr is a `k8s object` used to `request tls certificates`.  
it is mainly used for node and client authentication.  

 
kubelet creates a csr to `authenticate to the api` server.  

  
csr = request → approve → signed certificate

---

### ***what is memory.min in cgroups and why is it 0***

memory.min `protects` a minimum amount of memory from reclamation.  
kubernetes `does not` set it, so it stays at zero.  


a pod has memory requests but memory.min is still 0.  

kubernetes `enforces memory limits`, not guaranteed floors

---

### ***what do kubernetes qos classes do***

`qos` defines `pod priority during memory pressure`.  
it does not reserve or guarantee memory.  

 
besteffort pods are killed before guaranteed pods.  


qos controls `kill order`, not resource reservation

---

### ***why can setting memory.min crash a node***

it blocks memory reclamation under pressure.  
the system and kubelet `may starve`.  

 
pods are protected while kubelet gets oom-killed.  


`protected memory everywhere leads to node failure`

---
