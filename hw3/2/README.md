### question: why move apps to kubernetes instead of docker
answer:  
docker runs containers, kubernetes manages them at scale.  
it provides self-healing, scaling, and updates.  

example:  
a crashed container is automatically restarted.  

summary:  
kubernetes adds orchestration on top of containers

---

### question: what are the disadvantages of containers
answer:  
containers share the host kernel and have weaker isolation than vms.  
they add operational and networking complexity.  

example:  
a kernel bug affects all containers.  

summary:  
containers trade isolation for efficiency

---

### question: what is the difference between node and k3s in /etc/rancher
answer:  
node is a kubernetes api object.  
k3s is the kubernetes implementation on the host.  

example:  
node appears in kubectl get nodes, k3s appears on disk.  

summary:  
node is logical, k3s is physical

---

### question: hostport vs port in kubernetes
answer:  
containerport is metadata only.  
hostport exposes the pod directly on the node.  

example:  
hostport binds port 80 on the node to the pod.  

summary:  
port informs, hostport exposes
