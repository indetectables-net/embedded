# Reverse notes


## 1. Reverse tools

**Basic tools**
```bash
apt update && apt upgrade -y
apt install build-essential cmake git gdb python python-dev python-pip python3 python3-dev python3-pip net-tools
```

**Binwalk**
```bash
# install
git clone https://github.com/ReFirmLabs/binwalk
cd binwalk
sudo python3 setup.py install


# binwalk automatic dependencies install
sudo ./deps.sh

# binwalk manual dependencies install
sudo apt-get install mtd-utils gzip bzip2 tar arj lhasa p7zip p7zip-full cabextract squashfs-tools sleuthkit default-jdk lzop srecord

# Install sasquatch to extract non-standard SquashFS images
sudo apt-get install zlib1g-dev liblzma-dev liblzo2-dev
git clone https://github.com/devttys0/sasquatch
cd sasquatch && ./build.sh

# Install jefferson to extract JFFS2 file systems
sudo pip install cstruct
git clone https://github.com/sviehb/jefferson
cd jefferson && sudo python3 setup.py install

# Install ubi_reader to extract UBIFS file systems
sudo apt-get install liblzo2-dev python-lzo
git clone https://github.com/jrspruitt/ubi_reader
cd ubi_reader && sudo python3 setup.py install


# extract
binwalk fw.bin -e --preserve-symlinks
```

**Firmware Mod Kit**
```bash
# install
apt install git build-essential zlib1g-dev liblzma-dev python-magic bsdmainutils
git clone https://github.com/rampageX/firmware-mod-kit fmk-tool

# extract
fmk-tool/extract-firmware.sh fw.bin

# build new firmware from extracted data
fmk-tool/build-firmware.sh fmk/
```

**Install Radare**
```bash
git clone https://github.com/radareorg/radare2.git && cd radare2
sys/install.sh
```

## 2. Useful tips

**Share files**

1. Compress the file we want to share
```bash
tar -czvf rootfs.tar.gz rootfs/
```

2. Share file with http
```bash
# with python
python3 -m http.server 8000

# with php
php -S 0.0.0.0:8000

# with node
npx http-server ./ --port 8000
```

See ip of real machine
```bash
# hostname tool
hostname -I

# ip tool
ip a
```

Download from vm
```bash
wget http://192.168.126.130:8000/rootfs.tar.gz
tar -zxvf rootfs.tar.gz
```

3. Share file with scp
```bash
scp -P ssh-port rootfs.tar.gz root@localhost:/tmp
```


**Run fs from other systems**

Cheat to run elf/scripts from other rootfs

```bash
$ cd target-fs-folder
$ chroot . bin/sh
```


**Foward ports**

```bash
qemu-system-mipsel -M malta \
 -hda hda.qcow \
 -append "root=/dev/sda1 nokaslr" \
 -kernel vmlinux-malta \
 -initrd initrd-boot.img \
 -m 512 \
 -nographic \
 -no-reboot \
 -net nic -net user,hostfwd=tcp::10022-:22,hostfwd=tcp::10080-:80
```


**Enable SSH root login**

```bash
nano /etc/ssh/sshd_config

# add this to file
# PermitRootLogin yes

/etc/init.d/ssh restart
```


# References

- https://cjhackerz.net/posts/arm-emulated-environment-iotsec-qemu/
- https://shadow-file.blogspot.com/2015/01/dynamically-analyzing-wifi-routers-upnp.html
