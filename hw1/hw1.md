#### Create the folder that will become our container filesystem


> ```bash

> mkdir rootfs
> echo "hello container" > rootfs/hello.txt



> ```




#### add busybox as a tool for next steps when im in the new ns

> ```bash

> sudo cp /bin/busybox rootfs/
> sudo ln -s busybox rootfs/sh



> ```


i called link to busybox `sh` to get a shell from it for exploring the env inside my future ns
