# Reverse notes


## 1. Reverse tools

**Basic tools**
```
$ apt update && apt upgrade -y
$ apt install build-essential cmake git gdb python python-dev python-pip python3 python3-dev python3-pip net-tools
```

**Install Firmware Mod Kit**
```
$ sudo apt-get install git build-essential zlib1g-dev liblzma-dev python-magic bsdmainutils
$ git clone https://github.com/rampageX/firmware-mod-kit fmk-tool

# extract
fmk-tool/extract-firmware.sh fw.bin

# build new firmware from extracted data
fmk-tool/build-firmware.sh fmk/
```

**Install Radare**
```
$ git clone https://github.com/radareorg/radare2.git && cd radare2
$ sys/install.sh
```

## 2. Useful tips

**Share files**

1. Compress the file we want to share
```
$ tar -czvf rootfs.tar.gz rootfs/
```

2. Share file with http
```
# with python
$ python3 -m http.server 80

# with php
$ php -S 0.0.0.0:8000

# with node
$ npx http-server ./
```

See ip of real machine
```
# hostname tool
$ hostname -I

# ip tool
$ ip a
```

Download from vm
```
$ wget http://192.168.126.130:8000/rootfs.tar.gz
$ tar -zxvf rootfs.tar.gz
```

3. Share file with scp
```
$ scp -P ssh-port rootfs.tar.gz root@localhost:/tmp
```


**Run fs from other systems**

Cheat to run elf/scripts from other rootfs

```
$ cd target-fs-folder
$ chroot . bin/sh
```


**Foward ports**

```
$ qemu-system-mipsel -M malta \
 -hda hda.qcow \
 -append "root=/dev/sda1 nokaslr" \
 -kernel vmlinux-malta \
 -initrd initrd-boot.img \
 -m 512 \
 -nographic \
 -no-reboot \
 -net nic -net user,\
 hostfwd=tcp::10022-:22,\
 hostfwd=tcp::10080-:80
```


# References

- https://cjhackerz.net/posts/arm-emulated-environment-iotsec-qemu/
