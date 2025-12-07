#### Create the folder (rootfs )that will become our container "/" :


```bash
 mkdir rootfs
 echo "hello container" > rootfs/hello.txt
 ```




#### add `busybox` as a tool for next steps when Im inside the new namespace :

```bash
sudo cp /bin/busybox rootfs/
sudo ln -s busybox rootfs/sh
```


( i called sym link to busybox, `sh`, to get a shell from it for exploring the env inside my future ns )
