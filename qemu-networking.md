## Setting up Qemu with a tap interface

There are two parts to networking within QEMU:

- The virtual network device that is provided to the guest (e.g. a PCI network card).
- The network backend that interacts with the emulated NIC (e.g. puts packets onto the host's network).


#### Example: User mode network

User mode networking allows the guest to connect back to the outside world through TCP, UDP etc. ICMP Ping is not allowed. Also connections from host to guest are not allowed unless using port forwarding.

```
$ qemu-system-i386 -cdrom Core-current.iso -boot d -netdev user,id=mynet0,hostfwd=tcp::8080-:80 -device e1000,netdev=mynet0
```

##### `-netdev user,id=mynet0,hostfwd=tcp::8080-:80`
Create the user mode network backend having id `mynet0`.
Redirect incoming tcp connections on host port 8080 to guest port 80. The syntax is `hostfwd=[tcp|udp]:[hostaddr]:hostport-[guestaddr]:guestport`

##### `-device e1000,netdev=mynet0`
Create a NIC (model e1000) and connect to `mynet0` backend created by the previous parameter


#### Example: Tap network

TAP network overcomes all of the limitations of user mode networking, but requires a tap to be setup before running qemu. Also qemu must be run with root privileges.

```
$ sudo qemu-system-i386 -cdrom Core-current.iso -boot d -netdev tap,id=mynet0,ifname=tap0,script=no,downscript=no -device e1000,netdev=mynet0,mac=52:55:00:d1:55:01
```

##### `-netdev tap,id=mynet0,ifname=tap0,script=no,downscript=no`
Create a tap network backend with id `mynet0`. This will connect to a tap interface `tap0` which must be already setup. Do not use any network configuration scripts.

##### `-device e1000,netdev=mynet0,mac=52:55:00:d1:55:01`
Create a NIC (model e1000) and connect to `mynet0` backend created by the previous parameter. Also specify a mac address for the NIC.


## Setup

- Create a bridge
	```
	brctl addbr br0
	```
    
- Clear IP of `eth0`
	```
	ip addr flush dev eth0
	```

- Add `eth0` to bridge
	```
    brctl addif br0 eth0
    ```
    
- Create tap interface
	```
    tunctl -t tap0 -u `whoami`
    ```

- Add `tap0` to bridge
	```
    brctl addif br0 tap0
    ```
    
- Make sure everything is up
	```
    ifconfig eth0 up
    ifconfig tap0 up
    ifconfig br0 up
    ```

- Check if properly bridged
	```
    brctl show
    ```

- Assign ip to `br0`
	```
	dhclient -v br0
    ```


## Cleanup


- Remove tap interface `tap0` from bridge `br0`
  ```
  brctl delif br0 tap0
  ```

- Delete `tap0`
  ```
  tunctl -d tap0
  ```

- Remove eth0 from bridge
  ```
  brctl delif br0 eth0
  ```
  
- Bring bridge down
  ```
  ifconfig br0 down
  ```

- Remove bridge
  ```
  brctl delbr br0
  ```
  
- Bring `eth0` up  
  ```
  ifconfig eth0 up
  ```

- Check if an IP is assigned to `eth0`, if not request one
  ```
  dhclient -v eth0
  ```

### dhclient - Auto configuration using a DHCP server

- Release IP
  ```
  dhclient -v -r <interface>
  ```

- Request IP
  ```
  dhclient -v <interface>
  ```
  
## References
 
 - https://www.qemu.org/2018/05/31/nic-parameter/
 - http://www.linux-kvm.org/page/Networking
 - http://www.naturalborncoder.com/virtualization/2014/10/14/understanding-bridges/
 - http://www.naturalborncoder.com/virtualization/2014/10/17/understanding-tun-tap-interfaces/
 - https://wiki.qemu.org/Documentation/Networking
 - https://en.wikibooks.org/wiki/QEMU/Networking
 - http://blog.elastocloud.org/2015/07/qemukvm-bridged-network-with-tap.html
 - http://brezular.com/2011/06/19/bridging-qemu-image-to-the-real-network-using-tap-interface/
 - https://gist.github.com/extremecoders-re/e8fd8a67a515fee0c873dcafc81d811c
