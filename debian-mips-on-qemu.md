## Installing Debian mips on qemu

**Variants**

You can choose any of these variants
```
mips
mipsel
mips64el
```

the urls have this format
```
http://cdimage.debian.org/cdimage/release/current/{variant}/iso-cd/
http://ftp.debian.org/debian/dists/buster/main/installer-{variant}/current/images/malta/netboot/
http://ftp.debian.org/debian/dists/stretch/main/installer-{variant}/current/images/malta/netboot/
```


**Download initrd & vmlinux**

Download from
Buster (current): http://ftp.debian.org/debian/dists/buster/main/installer-mipsel/current/images/malta/netboot/
Stretch: http://ftp.debian.org/debian/dists/stretch/main/installer-mipsel/current/images/malta/netboot/

```
$ wget http://ftp.debian.org/debian/dists/buster/main/installer-mipsel/current/images/malta/netboot/initrd.gz -O initrd.gz
$ wget http://ftp.debian.org/debian/dists/buster/main/installer-mipsel/current/images/malta/netboot/vmlinux-4.19.0-10-4kc-malta -O vmlinux-malta
```


**Download Debian iso**

Important! The initrd already acts as an installer so this step is optional

Download mipsel netinst from
Buster (current): http://cdimage.debian.org/cdimage/release/current/mipsel/iso-cd/
Stretch: http://cdimage.debian.org/cdimage/archive/9.13.0/mipsel/iso-cd/

```
$ wget https://cdimage.debian.org/debian-cd/current/mipsel/iso-cd/debian-10.5.0-mipsel-netinst.iso -O debian-mipsel-netinst.iso
```


**Create a new hard disk**

```
$ qemu-img create -f qcow2 hda.qcow 10G
```


**Install Debian**

```
$ qemu-system-mipsel -M malta \
 -cdrom debian-mipsel-netinst.iso \
 -hda hda.qcow \
 -append "root=/dev/sda1 nokaslr" \
 -kernel vmlinux-malta \
 -initrd initrd.gz \
 -m 512 \
 -boot d \
 -nographic \
 -no-reboot
```


**Boot from hda**

Important note: `initrd-boot.img` is the file copied from `hda.qcow` and NOT the original one downloaded from ftp.debian.org.
If you use the original file the Debian installer will return!.

Copy initrd.img
```
sudo apt-get install libguestfs-tools
sudo guestmount -a hda.qcow -i /mnt

sudo cp /mnt/boot/initrd.img-4.19.0-10-4kc-malta initrd-boot.img
sudo chown "$USER":"$USER" initrd-boot.img

sudo umount /mnt
```

Run VM

```
$ qemu-system-mipsel -M malta \
 -hda hda.qcow \
 -append "root=/dev/sda1 nokaslr" \
 -kernel vmlinux-malta \
 -initrd initrd-boot.img \
 -m 512 \
 -nographic \
 -no-reboot
```


**References**

- https://github.com/kholia/mips-hacking
- https://linux-tips.com/t/booting-from-an-iso-image-using-qemu/136
- https://wiki.debian.org/QEMU
- http://ask.xmodulo.com/mount-qcow2-disk-image-linux.html
- https://gist.github.com/extremecoders-re/3ddddce9416fc8b293198cd13891b68c
