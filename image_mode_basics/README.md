# Image Mode Basics

## Instruqt Track

Access the lab at [red.ht/im-basics]([red.ht/im-basics](https://play.instruqt.com/embed/rhel/tracks/image-mode-basics?token=em_rHX3r7L0P0dLKkGw)) 

## Solution

### Stage 1 : Image Creation

In **Terminal**

```bash
podman build -t ${REGISTRY}/test-bootc .
podman push ${REGISTRY}/test-bootc
```

### Stage 2 : Deployment

In **Terminal**

```bash
podman run --rm --privileged \
        --volume .:/output \
        --volume ./config.json:/config.json \
        --volume /var/lib/containers/storage:/var/lib/containers/storage \
        registry.redhat.io/rhel10/bootc-image-builder:10.0 \
        --type qcow2 \
        --config config.json \
         ${REGISTRY}/test-bootc

cp qcow2/disk.qcow2 /var/lib/libvirt/images/bootc10.0-vm.qcow2

virt-install --name bootc-vm \
--disk /var/lib/libvirt/images/bootc10.0-vm.qcow2 \
--import \
--memory 2048 \
--graphics none \
--osinfo rhel9-unknown \
--noautoconsole \
--noreboot

virsh start bootc-vm
```

### Stage 3 : Image Edition

In **Terminal**

```bash
cat << EOF > Containerfile
FROM registry.redhat.io/rhel10/rhel-bootc:10.0
ADD etc /etc
RUN dnf install -y httpd vim
RUN systemctl enable httpd
EOF

podman build -t ${REGISTRY}/test-bootc .
podman push ${REGISTRY}/test-bootc
```

### Stage 4 : Redeployment

In **VM SSH Session**

```bash
sudo bootc upgrade

echo 'core:1redhat' | sudo chpasswd

sudo reboot
```
