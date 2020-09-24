# Install QEMU


## Ubuntu 18

**Normal install**

```
$ sudo apt install qemu qemu-utils qemu-system libguestfs-tools bridge-utils virt-manager libvirt-daemon-system libvirt-clients
```

**Most update install**

Add OpenSuse repo
```
$ echo 'deb http://download.opensuse.org/repositories/home:/noscript/bionic/ /' | sudo tee /etc/apt/sources.list.d/home:noscript.list
$ curl -fsSL https://download.opensuse.org/repositories/home:noscript/bionic/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home:noscript.gpg > /dev/null
$ sudo apt update
```

And install!
```
$ sudo apt install qemu qemu-utils qemu-system libguestfs-tools bridge-utils virt-manager libvirt-daemon-system libvirt-clients
```
  
## References
 
 - https://mathiashueber.com/manually-update-qemu-on-ubuntu-18-04/
 - https://www.reddit.com/r/VFIO/comments/hkezeq/how_to_set_up_qemu_500_on_ubuntu_1804/
 - https://software.opensuse.org/download/package?package=qemu&project=home%3Anoscript
