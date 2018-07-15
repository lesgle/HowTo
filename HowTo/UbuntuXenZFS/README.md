# Ubuntu 17.04 with XEN 4.8 and ZFS

Update packages.

```text
apt update && apt upgrade
```

Install XEN hypervisor.

```text
apt-get install xen-hypervisor-amd64
```

Modify GRUB and reboot.

```text
dpkg-divert --divert /etc/grub.d/08_linux_xen --rename /etc/grub.d/20_linux_xen
update-grub
```

Check that Domain-0 is up and running.

```text
xl list
```

Check how it started and runs. First one is a good place to check if VMX advanced features are supported.

```text
xl dmesg
xl top
```

Configure bridged network. List of interfaces can be checked by running "ip a". Without "bridge_ports" isolated virtual network for domains can be created.

```text
nano /etc/network/interfaces
auto eth0
iface eth0 inet manual
auto xenbr0
iface xenbr0 inet static
address IP_ADDRESS
netmask NETMASK
gateway GW
bridge_ports eth0
```

Install and configure ZFS. It will be installed in Dom0 with read/write caching, compression and by obvious reasons with ZFS memory use limit.

```text
apt install zfsutils
```

Create 2x2 mirrored pool, where X is your drive names (can be checked by "fdisk -l").

```text
zpool create POOL_NAME mirror /dev/sdX /dev/sdX mirror /dev/sdX /dev/sdX
```

Check pool status.

```text
zpool status
```

Get pool IO performance information.

```text
zpool iostat -v
```

Add write cache drive.

```text
zpool add POOL_NAME log /dev/sdX
```

Add read cache drive.

```text
zpool add POOL_NAME cache /dev/sdX
```

Create ZFS file system.

```text
zfs create POOL_NAME/FS_NAME
```

Set quota. Outcome can be checked by "df -h".

```text
zfs set quota=100GB POOL_NAME/FS_NAME
```

Quota set example, where pool1 is name of the pool and "test" is ZFS file system.

```text
zfs set quota=100GB pool1/test
```

Set ZFS compression.

```text
zfs set compression=lz4 POOL_NAME/FS_NAME
```

Get ZFS compression ratio.

```text
zfs get compressratio
```

Check ZFS arc limits and set min/max values. Example sets minimum 512MB and maximum 4GB. Reboot is required.

```text
cat /proc/spl/kstat/zfs/arcstats |grep c_
nano /etc/modprobe.d/zfs.conf
options zfs zfs_arc_min=536870912
options zfs zfs_arc_max=4294967296
reboot
cat /proc/spl/kstat/zfs/arcstats |grep c_
```

Create basic HVM.
Create disk image (15GB) on ZFS volume. Put there an ISO with OS installation media.

```text
dd if=/dev/zero of=test.raw bs=1G count=15
```

Create domain configuration file.

```text
nano /etc/xen/test.cfg
builder = "hvm"
name = "test"
memory = "1024"
vcpus = 1
vif = ['bridge=xenbr0']
disk = ['vdev=hda,format=raw,target=/pool1/test/test.raw','file:/pool1/test/linuxmint-18.2-cinnamon-64bit.iso,hdc:cdrom,r']
vnc = 1
vnclisten = "ip of Dom0"
vncpasswd = "password"
boot="dc"
```

Create PV instance with xen-tools.

```text
apt install xen-tools
xen-create-image --hostname=test2 --dist=zesty --dir=/pool1/test
xl create /etc/xen/test2.cfg
```

Enter to test2 console.

```text
xl console test2
```

Some useful commands and configurations.
Check virtualization CPU support (flags like ept, vmx etc).

```text
cat /proc/cpuinfo
```

Disabling IPv6 if required.

```text
nano /etc/sysctl.d/99-sysctl.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
sysctl -p
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```

Some disk commands.

```text
parted /dev/sdX print unit MB print free
fdisk -l
```

Create 20GB file (image files for PV, PVH - *.raw, *.img etc).

```text
dd if=/dev/zero of=filename.raw bs=1G count=20
```