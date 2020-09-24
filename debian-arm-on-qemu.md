## Installing Debian arm on qemu

**Variants**

You can choose any of these variants
```
armel
armhf
```

the urls have this format
```
http://cdimage.debian.org/cdimage/release/current/{variant}/iso-cd/
http://ftp.debian.org/debian/dists/buster/main/installer-{variant}/current/images/netboot/
http://ftp.debian.org/debian/dists/stretch/main/installer-{variant}/current/images/netboot/
```


**Download initrd & vmlinux**

Download from
Buster (current): http://ftp.debian.org/debian/dists/buster/main/installer-armhf/current/images/netboot/
Stretch: http://ftp.debian.org/debian/dists/stretch/main/installer-armhf/current/images/netboot/

```
$ wget http://ftp.debian.org/debian/dists/buster/main/installer-armhf/current/images/netboot/initrd.gz -O initrd.gz
$ wget http://ftp.debian.org/debian/dists/buster/main/installer-armhf/current/images/netboot/vmlinuz -O vmlinuz
```


**Download Debian iso**

Important! The initrd already acts as an installer so this step is optional

Download mipsel netinst from
Buster (current): http://cdimage.debian.org/cdimage/release/current/armhf/iso-cd/
Stretch: http://cdimage.debian.org/cdimage/archive/9.13.0/armhf/iso-cd/

```
$ wget https://cdimage.debian.org/debian-cd/current/armhf/iso-cd/debian-10.5.0-armhf-netinst.iso -O debian-armhf-netinst.iso
```


**Create a new hard disk**

```
$ qemu-img create -f qcow2 hda.qcow 10G
```


**Install Debian**

```
$ qemu-system-arm -M virt \
 -cdrom debian-armhf-netinst.iso \
 -hda hda.qcow \
 -append "root=/dev/sda1" \
 -kernel vmlinuz \
 -initrd initrd.gz \
 -m 2048 \
 -smp 2 \
 -boot d \
 -nographic \
 -no-reboot
```


**Boot from hda**

Important note: `initrd-boot.img` and `vmlinuz-armmp-lpae` is the file copied from `hda.qcow` and NOT the original one downloaded from ftp.debian.org.
If you use the original file the Debian installer will return!.

Copy initrd.img
```
sudo apt-get install libguestfs-tools
sudo guestmount -a hda.qcow -i /mnt

sudo cp /mnt/boot/vmlinuz-4.19.0-10-armmp-lpae vmlinuz-armmp-lpae
sudo chown "$USER":"$USER" vmlinuz-armmp-lpae

sudo cp /mnt/boot/initrd.img-4.19.0-10-armmp-lpae initrd-boot.img
sudo chown "$USER":"$USER" initrd-boot.img

sudo umount /mnt
```

Run VM

```
$ qemu-system-arm -M virt \
 -hda hda.qcow \
 -append "root=/dev/sda1" \
 -kernel vmlinuz-armmp-lpae \
 -initrd initrd-boot.img \
 -m 2048 \
 -smp 2 \
 -nographic \
 -no-reboot
```


**References**

- https://github.com/kholia/mips-hacking
- https://linux-tips.com/t/booting-from-an-iso-image-using-qemu/136
- https://wiki.debian.org/QEMU
- http://ask.xmodulo.com/mount-qcow2-disk-image-linux.html
- https://gist.github.com/extremecoders-re/3ddddce9416fc8b293198cd13891b68c
