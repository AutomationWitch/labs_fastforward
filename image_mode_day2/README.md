# Image Mode Day 2

## Instruqt Track

Access the lab at https://www.redhat.com/en/day-2-operations-with-image-mode-for-red-hat-enterprise-linux

## Solution

### Stage 1 : Introduction

In **Terminal**

```bash
cat << EOF > Containerfile
FROM registry.redhat.io/rhel10/rhel-bootc:10.0

ADD etc /etc

RUN dnf install -y httpd vim
RUN systemctl enable httpd

EOF

podman build -t $REGISTRY/test-bootc:el10 .
podman push $REGISTRY/test-bootc:el10

sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo bootc switch $REGISTRY/test-bootc:el10
sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo reboot
```
