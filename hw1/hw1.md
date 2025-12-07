### Create the folder (`rootfs`) that will become our container "/" :


```bash
 mkdir rootfs
 echo "hello container" > rootfs/hello.txt
 ```




### add `busybox` as a tool for next steps when Im inside the new namespace :

```bash
sudo cp /bin/busybox rootfs/
sudo ln -s busybox rootfs/sh
```


( i called sym link to busybox, `sh`, to get a shell from it for exploring the env inside my future ns )


`sh` inside rootfs will be a shell implemented by BusyBox.


rootfs contains :

```text
rootfs/
├── busybox
├── sh -> busybox
└── hello.txt
```

right now im just ====> **a normal process with no isolation yet**




### Create a mount namespace :

```bash
sudo unshare --mount --fork bash
```

What happens now:

----> The kernel created a new mount namespace for a child process

----> That child process is this new bash

----> Its filesystem root `/` is still the host filesystem


right now im just ====> **a process inside a new mount namespace**


#### Stop mount propagation into the host :

```bash
mount --make-rprivate /
```

Why?

- The new mount namespace inherits the host’s “shared” propagation
- We must make it private so mounts inside the namespace stay inside



### Enter the rootfs using chroot

chroot syntax:

`chroot NEW-ROOT CMD`

```bash
sudo chroot rootfs /sh
```


at this momment,

kernel do this:

----> Changes this process’s root directory from `host /` → `rootfs/`

----> Executes `/sh` inside that new root

----> `/sh` is a symlink → busybox → which starts a shell (ash)

Now inside the container shell:

```text
/ # ls
busybox   hello.txt   sh
```

right now im just ====> **a process inside a mount namespace with new filesystem**


### this is my first real container 







