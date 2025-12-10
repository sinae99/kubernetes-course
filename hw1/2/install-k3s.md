# K3s Air-Gapped




For a full air-gapped installation :
- K3s binary (`k3s`)
- K3s install script (`install.sh`)
- K3s air-gap image (`k3s-airgap-images-amd64.tar.zst`)

All three files must be downloaded on an internet-connected machine and then transferred to the air‑gapped machine.

---

```bash
mkdir -p /internet-machine
```

```bash
mkdir -p /airgapped-machine
```



## 1. Download Files (on Internet Machine)

```bash
curl -L -o k3s "https://github.com/k3s-io/k3s/releases/download/v1.33.3+k3s1/k3s"
```

```bash
curl -L -o k3s-airgap-images-amd64.tar.zst "https://github.com/k3s-io/k3s/releases/download/v1.33.3+k3s1/k3s-airgap-images-amd64.tar.zst"
```

```bash
curl -L -o install.sh https://get.k3s.io
```

+ `chmod` for all scripts


---

## 2. Transfer Files to Air-Gapped Machine


```bash
cp ./*  /root/airgapped-machine/

```


---

## 3. Install K3s Binary Manually

K3s must be placed in `/usr/local/bin/k3s` before running the installer.

```bash
cp /root/airgapped-machine/k3s /usr/local/bin/k3s
chmod +x /usr/local/bin/k3s
k3s --version
```

---

## 4. Prepare Air-Gap Image Directory

K3s imports images from:

```
/var/lib/rancher/k3s/agent/images/
```

Create the directory and copy the tarball:

```bash
mkdir -p /var/lib/rancher/k3s/agent/images/
```

```bash
cp /root/airgapped-machine/k3s-airgap-images-amd64.tar.zst    /var/lib/rancher/k3s/agent/images/
```


---

## 5. Install K3s in Air-Gapped Mode

```bash
cd /root/airgapped-machine
```

```bash
INSTALL_K3S_SKIP_DOWNLOAD=true ./install.sh
```

Expected: no downloads, no GitHub URLs.

---

## 6. Verify K3s Service

```bash
systemctl status k3s
```

---

## 7. Verify Node

```bash
k3s kubectl get nodes -o wide
```

---

## 8. Verify System Pods

```bash
k3s kubectl get pods -A
```

---

## 9. Verify Images Were Imported

### CRI view:

```bash
k3s crictl images
```

### containerd view:

```bash
k3s ctr images ls
```
