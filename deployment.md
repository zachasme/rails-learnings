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
IP_ADDRESS=192.168.1.105

# cloud init
cat <<EOF > /tmp/network-config.yaml
network:
  version: 2
  ethernets:
    eth0:
      adresses: [${IP_ADDRESS}]
EOF

# download base (cloud) image
sudo wget -O "${IMAGES}/base/${OS_NAME}.img" \
     "https://cloud-images.ubuntu.com/${OS_NAME}/current/${OS_NAME}-server-cloudimg-amd64-disk-kvm.img"

# create guest image backed by base image
sudo qemu-img create -b "${IMAGES}/base/${OS_NAME}.img" \
                     -F qcow2 -f qcow2 \
                     "${IMAGES}/guests/${GUEST_NAME}.img" \
                     "${DISK}"

# create vm
virt-install --name "${GUEST_NAME}" \
             --network "bridge=${BRIDGE},model=virtio" \
             --memory "${MEMORY}" \
             --vcpus "${CPU}" \
             --import \
             --disk "${IMAGES}/guests/${GUEST_NAME}.img" \
             --cloud-init "root-password-generate=yes,disable=yes network-config=/tmp/network-config.yaml" \
             --osinfo "detect=on" \
             --graphics none \
             --autoconsole none \
             --autostart
```

[ubuntu-server]: https://ubuntu.com/download/server
