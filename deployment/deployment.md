# Deployment

## 1. Set up the physical server

> Ubuntu Server, KVM

- Install [Ubuntu Server][ubuntu-server] on your machine
- Install KVM
- Configure bridge network using Netplan

```yaml
network:
  version: 2
  renderer: networkd

  ethernets:
    eno1:
      dhcp4: false

  bridges:
    br0:
      interfaces: [ eno1 ]
      dhcp4: false
      addresses: [192.168.1.60/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1]
      parameters:
        stp: true
        forward-delay: 4
```

## Adding a service

> https://earlruby.org/2023/02/quickly-create-guest-vms-using-virsh-cloud-image-files-and-cloud-init/

```sh
TIMESTAMP = $(date +%s)
IMAGES="/var/lib/libvirt/images"
BRIDGE=br0
OS=ubuntu22.04
OS_NAME=jammy
GUEST_NAME=mohnny
CPU=1
MEMORY=2048
DISK=20G
IP_ADDRESS="192.168.1.77"
GATEWAY="192.168.1.1"
SUBNET="/24"
SSH_KEY=""

# cloud init
cat <<EOF > /tmp/network-config.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0:
      addresses: [${IP_ADDRESS}${SUBNET}]
      gateway4: 192.168.1.1
      nameservers:
        search: [ localdomain ]
        addresses:
          - "192.168.1.1"
EOF

# download base (cloud) image
sudo wget --timestamping -O "${IMAGES}/base/${OS_NAME}-${TIMESTAMP}.img" \
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
             --cloud-init "disable=on,root-ssh-key=${SSH_KEY},network-config=/tmp/network-config.yaml" \
             --osinfo "${OS}" \
             --graphics none \
             --autoconsole none \
             --autostart
```

[ubuntu-server]: https://ubuntu.com/download/server
