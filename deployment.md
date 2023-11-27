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

```
wget -O jammy.img \
     https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64-disk-kvm.img

sudo qemu-img convert -f qcow2 -O qcow2 \
              /var/lib/libvirt/images/base/jammy.img \
              /var/lib/libvirt/images/GUEST_NAME.qcow2
sudo qemu-img resize /var/lib/libvirt/images/GUEST_NAME.qcow2 40G

virt-install --name GUEST_NAME \
             --vcpus 1 --memory 2048 --os-variant ubuntu22.04 \
             --network bridge=br0,model=virtio --graphics none \
             --events on_reboot=restart \
             --import --disk /var/lib/libvirt/images/base/jammy.img \
             --noautoconsole --cloud-init user-data=/u/kvm/guests-config/user-data-#{guest_name},network-config=/u/kvm/guests-config/network-config-#{guest_name} \
             --autostart
```

[ubuntu-server]: https://ubuntu.com/download/server