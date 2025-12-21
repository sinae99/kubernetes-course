### ***why move apps to kubernetes instead of docker***
 
docker runs containers, kubernetes `manages them at scale`.  
it provides `self-healing`, `scaling`, and `updates`.  

 
a crashed container is `automatically restarted`.  

  
kubernetes adds orchestration on `top` of containers

---

### ***what are the disadvantages of containers***
 
containers `share the host kernel` and have `weaker isolation` than vms.  
they add operational and networking complexity.  

  
a kernel bug affects all containers.  


containers trade isolation for efficiency

---

### ***what is the difference between node and k3s in /etc/rancher***

node is a kubernetes api object.  
k3s is the kubernetes implementation on the host.  

 
`node appears in kubectl get nodes`, `k3s appears on disk`.  


node is logical, k3s is physical

---

### ***hostport vs port in kubernetes***

containerport is `metadata` only.  
hostport `exposes` the pod `directly` `on the node`.  

 
hostport `binds port 80 on the node to the pod`.  


port informs, hostport exposes
