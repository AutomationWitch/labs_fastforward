# Image Mode Day 2

## Instruqt Track

Access the lab at [red.ht/im-day2](https://www.redhat.com/en/day-2-operations-with-image-mode-for-red-hat-enterprise-linux)

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

podman build -t ${REGISTRY}/test-bootc:el10 .
podman push ${REGISTRY}/test-bootc:el10

sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo bootc switch ${REGISTRY}/test-bootc:el10
sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo reboot
```

### Stage 2 : Tags

In **Terminal**

```bash
cat << EOF > Containerfile
FROM registry.redhat.io/rhel10/rhel-bootc:10.0
ADD etc /etc
RUN dnf install -y httpd vim
RUN systemctl enable httpd
RUN echo "New application coming soon!" > /var/www/html/index.html
EOF

podman build -t ${REGISTRY}/test-bootc:v2 .
podman image tag ${REGISTRY}/test-bootc:v2 ${REGISTRY}/test-bootc:dev
podman push ${REGISTRY}/test-bootc:v2
podman push ${REGISTRY}/test-bootc:dev

sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo bootc switch ${REGISTRY}/test-bootc:v2
sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo reboot
```

### Stage 3 : Rollback

In **Terminal**

```bash
sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo bootc rollback
sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo reboot
```

### Stage 4 : Local state vs Image state

In **Terminal**

```bash
podman build -t ${REGISTRY}/test-bootc:v3 -f Containerfile.index
podman push ${REGISTRY}/test-bootc:v3

sshpass -p redhat ssh  -o StrictHostKeyChecking=no core@192.168.122.78 sudo bootc switch ${REGISTRY}/test-bootc:v3
```
