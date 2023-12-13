# Deployment

## 1. Set up the physical server

> Ubuntu Server, KVM

- Install [Ubuntu Server][ubuntu-server] on your machine
- Configure bridge network using Netplan
- Install KVM

```yaml
network:
  version: 2
  renderer: networkd

  bridges:
    br0:
      dhcp4: yes
      interfaces: [enp1s0]
```

## Adding a service

> https://earlruby.org/2023/02/quickly-create-guest-vms-using-virsh-cloud-image-files-and-cloud-init/

```sh
IMAGES="/var/lib/libvirt/images"
BRIDGE=br0
OS_NAME=jammy
GUEST_NAME=mohnny
CPU=1
MEMORY=2048
DISK=20G
IP=192.168.1.105

# download base
sudo wget -O "${IMAGES}/base/${OS_NAME}.img" \
     "https://cloud-images.ubuntu.com/${OS_NAME}/current/${OS_NAME}-server-cloudimg-amd64-disk-kvm.img"

# sudo qemu-img convert -f qcow2 -O qcow2 \
#              "${IMAGES}/base/${OS_NAME}.img" \
#              "${IMAGES}/${GUEST_NAME}.qcow2"

# create guest image
sudo qemu-img create -b "${IMAGES}/base/${OS_NAME}.img" \
                     -F qcow2 -f qcow2 \
                     "${IMAGES}/guests/${GUEST_NAME}.img" \
                     "${DISK}"

# create vm
virt-install --name "${GUEST_NAME}" \
             --memory "${MEMORY}" \
             --vcpus "${CPU}" \
             --import \
             --cloud-init "root-password-generate=yes,disable=yes" \
             --osinfo "detect=on" \
             --disk "${IMAGES}/guests/${GUEST_NAME}.img" \
             --network "bridge=${BRIDGE},model=virtio" \
             --graphics none \
             --autoconsole none \
             --autostart
```
# LlcMYvzFg6YLQHht
[ubuntu-server]: https://ubuntu.com/download/server
