# Installation
1. Download centos minimal: 
```sh
wget http://mirrors.aliyun.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso
```
2. Check vm support
```shell
egrep '(vmx|svm)' /proc/cpuinfo
```
3. Install kvm and libs, and review installations
```shell
yum install kvm
yum install qemu-kvm python-virtinst libvirt libvirt-python virt-manager libguestfs-tools
rpm -qa | egrep "virt|kvm|qemu"
```
4. Install bridge-utils (Unnecessary)
```shell
yum install bridge-utils
```
5. edit /etc/sysconfig/network-script/ifcfg-br0 [Reference](http://www.linux-kvm.org/page/Networking) and [Reference](https://www.chenyudong.com/archives/libvirt-kvm-bridge-network.html)
```shell
$ cat /etc/sysconfig/network-script/ifcfg-br0
DEVICE=br0
TYPE=Bridge
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=static
IPADDR=192.168.0.11
NETMASK=255.255.255.0
GATEWAY=192.168.0.1
DNS1=218.30.19.40
DNS2=61.134.1.4

$ cat /etc/sysconfig/network-script/ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=no
#BOOTPROTO=xxxx # Comment out BOOTPROTO
BRIDGE=br0
```
6. Create and allocate vm disk
```shell
qemu-img create -f qcow2 -o preallocation=metadata /home/kvm/test.qcow2 30G
```
7. Install vm with vnc server and bridge utils
```shell
virt-install --name=test --ram=8192 --vcpus=1 -f /home/kvm/test.qcow2 --cdrom /home/CentOS-7-x86_64-Minimal-1511.iso --graphics vnc,listen=0.0.0.0,port=5922, --network bridge=br0 --force --autostart
```
8. Start Remmina or other software to connect vnc and finish installation


## Post installation (Guest machine)
1. Config network /etc/sysconfig/network-scripts/ifcfg-eth0
```shell
BOOTPROTO=static
IPADDR=192.168.x.x
NETMASK=255.255.255.0
GATEWAY=192.168.x.x
DNS1=x.x.x.x
DNS2=x.x.x.x
ONBOOT=yes
```
2. Restart network
```shell
systemctl restart network.service
```
3. Install net-tools and openssh-server
```shell
yum install net-tools
yum install openssh-server
```
4. Configure ssh server to avoid being kick out
```shell
ClientAliveInterval 60 # uncomment and change to 60, which means to send alive signal per minute
ClientAliveCountMax 3 # uncomment
```
5. Customize host name
```shell
hostname newhost # rename to newhost
```
6. Configure timezone [Reference](http://blog.csdn.net/kimsoft/article/details/8091734)
```shell
date -R # check current timezone offset, e.g. +08:00
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

### Common commands（以srv_1为例）
1. virsh list --all #列出所有虚拟机
2. virsh setmaxmem srv_1 8192M #设置虚拟机最大内存8G
6. virsh setmem srv_1 8192M #设置虚拟机可使用的内存8G
3. virsh start srv_1 #启动虚拟机
4. virsh shutdown srv_1 #关闭虚拟机
5. virsh edit srv_1 #编辑虚拟机的xml，可直接编辑参数
7. virsh dominfo srv_1 #虚拟机信息
8. # Attach a cdrom to kvm. Find dev def in xml with cdrom segment, something like dev='hda'
```sh
virsh attach-disk domain iso device
virsh attach-disk srv_1 /home/centos.iso hda --type cdrom --mode readonly
```
9. #Snapshot management
```sh
virsh snapshot-create domain #create new snapshot for this domain
virsh snapshot-list domain #list all snapshots of this domain
virsh snapshot-current domain #find out current snapshot of the domain
virsh snapshot-revert domain snapshot #revert domain to the reffered snapshot number
virsh snapshot-delete domain snapshot #delete domain snapshot
```

10. virsh undefine domain #删除虚拟机

11. qemu-img resize vm.img +10G #增加10G给镜像扩充，相应的有-10G减少10G，针对raw或qcow2格式

12. #Change disk format
* qemu-img convert -f raw -O qcow2 windows7_ultimate_x86 windows7_ultimate_x86.qcow2 #change from raw into qcow2 so that we can use snapshot utils
* [something about format changing](https://unixblogger.com/2016/06/13/convert-img-raw-to-qcow2/)
