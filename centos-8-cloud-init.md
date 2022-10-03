# CentOS Stream 8 Cloud Init Setup

```

apt-get update
apt-get install libguestfs-tools
export EDITOR=vi

wget <STREAM-IMAGE-URL> # https://cloud.centos.org/centos/8-stream/x86_64/images/

export image_name='CentOS-Stream-GenericCloud-8-20220913.0.x86_64.qcow2'

virt-edit -a ${image_name} /etc/cloud/cloud.cfg -e 's/disable_root: [Tt]rue/disable_root: False/'
virt-edit -a ${image_name} /etc/cloud/cloud.cfg -e 's/disable_root: 1/disable_root: 0/' 
virt-edit -a ${image_name} /etc/cloud/cloud.cfg -e 's/lock_passwd: [Tt]rue/lock_passwd: False/'
virt-edit -a ${image_name} /etc/cloud/cloud.cfg -e 's/lock_passwd: 1/lock_passwd: 0/' 
virt-edit -a ${image_name} /etc/cloud/cloud.cfg -e 's/ssh_pwauth:   0/ssh_pwauth:   1/';

virt-edit -a CentOS-Stream-GenericCloud-8-20220913.0.x86_64.qcow2 /etc/cloud/cloud.cfg
# add
packages:
 - qemu-guest-agent

qm create 9000 --memory 2048 --net0 virtio,bridge=vmbr0
qm importdisk 9000 CentOS-Stream-GenericCloud-8-20220913.0.x86_64.qcow2 local-lvm
qm set 9000 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-9000-disk-0
qm set 9000 --ide2 local-lvm:cloudinit
qm set 9000 --boot c --bootdisk scsi0
qm set 9000 --serial0 socket --vga serial0
qm template 9000
qm clone 9000 101 --name centos8-01

```
