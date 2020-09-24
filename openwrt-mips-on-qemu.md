# Using a prebuilt image

The latest OpenWRT version at the time of writing is 19.07.4 available at https://downloads.openwrt.org/releases/19.07.4/.
The [malta](https://downloads.openwrt.org/releases/19.07.4/targets/malta/) target is the one intented for emulation and we'll use that.

## 1. Using initramfs

Here the rootfs is bundled along with the zImage as a single file. In this mode the filesystem resides entirely in memory and any modifications are lost on poweroff.

**Download the bundled zImage**
```
$ wget https://downloads.openwrt.org/releases/19.07.4/targets/malta/be/openwrt-19.07.4-malta-be-vmlinux-initramfs.elf -O vmlinux-initramfs
```

and run
```
$ qemu-system-mips -M malta \
 -kernel vmlinux-initramfs \
 -no-reboot -nographic
```

## 2. Using a separate rootfs

For MIPS, the ImageBuilder does not generate the rootfs. Although the download page does provide a pre-compiled kernel with embedded initramfs (vmlinux-initramfs.elf), however the ImageBuilder does not seem to generate a root fs either embedded in the kernel as initramfs or separate.

**Generate rootfs**
```
$ wget https://downloads.openwrt.org/releases/19.07.4/targets/malta/be/openwrt-imagebuilder-19.07.4-malta-be.Linux-x86_64.tar.xz -O imagebuilder.tar.xz
$ tar -xvf imagebuilder.tar.xz
$ cd openwrt-imagebuilder-19.07.4-malta-be.Linux-x86_64
$ make image
$ mksquashfs build_dir/target-mips_24kc_musl/root-malta/ rootfs.squashfs -comp xz
$ cp rootfs.squashfs ..
$ cd ..
```

**Download plain vmlinux**
```
$ wget https://downloads.openwrt.org/releases/19.07.4/targets/malta/be/openwrt-19.07.4-malta-be-vmlinux.elf -O vmlinux
```

and run
```
$ qemu-system-mips -M malta \
 -kernel vmlinux \
 -drive file=rootfs.squashfs,format=raw \
 -append "root=/dev/sda" \
 -nographic -no-reboot
```

## Other info

For Setting up networking, Customizing OpenWRT and other info please read openwrt-arm-on-qemu.md

# References

- https://openwrt.org/docs/guide-user/additional-software/imagebuilder
- https://openwrt.org/docs/guide-developer/quickstart-build-images
- https://openwrt.org/docs/guide-user/additional-software/beginners-build-guide
- https://openwrt.org/docs/guide-user/virtualization/qemu
- https://wiki.openwrt.org/doc/howto/qemu
- https://blog.ljdelight.com/compiling-openwrt-from-source/
- https://wiki.openwrt.org/doc/howto/obtain.firmware.generate
- https://wiki.openwrt.org/doc/howto/obtain.firmware
- https://wiki.openwrt.org/doc/techref/filesystems
- https://openwrt.org/packages/start
- https://downloads.lede-project.org/releases/17.01.4/targets/armvirt/generic/
- https://github.com/lede-project/source/tree/master/target/linux/armvirt
- https://wiki.openwrt.org/doc/networking/network.interfaces
- https://wiki.openwrt.org/doc/faq/after.installation
- https://wiki.openwrt.org/doc/uci/firewall
- https://openwrt.org/docs/guide-user/firewall/firewall_configuration
- https://www.qemu.org/2018/05/31/nic-parameter/
- https://gist.github.com/extremecoders-re/f2c4433d66c1d0864a157242b6d83f67
- https://wiki.qemu.org/Documentation/Networking
