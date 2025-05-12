# Debug-Container

[![Docker Repository on Quay](https://quay.io/repository/divazone_tw/debug-container/status "Docker Repository on Quay")](https://quay.io/repository/divazone_tw/debug-container)

This container serves as the administrator's interactive shell environment. It includes a range of diagnostic utilities—like ping, traceroute, and mtr—as well as manual pages that are commonly used for troubleshooting issues on the host system.

- Networking-related commands:
  - [x] iproute
  - [x] net-tools
  - [x] mtr
  - [x] dig
  - [x] ping
  - [x] ethtool
  - [x] nmap-ncat
- Generic commands:
  - [x] vim
  - [x] git
  - [x] htop

## Download
```
docker pull ghcr.io/divazone/debug-container:master
```

## How to use `debug-container` on specific hosts?

1. Bridge Mode (Container on OS):
```bash
docker run -it --rm --name debug-container ghcr.io/divazone/debug-container:master
```

2. Host Mode (Container within OS):
```bash
docker run -it --rm --name debug --privileged \
       --ipc=host --net=host --pid=host -e HOST=/host \
       -e NAME=debug-container -e IMAGE=divazone/debug-container \
       -v /run:/run -v /var/log:/var/log \
       -v /etc/localtime:/etc/localtime -v /:/host \
       ghcr.io/divazone/debug-container:master
```

3. Container Mode (Bridge another container)
```
docker run -it --rm --name debug-contaienr --net container:<container_name> ghcr.io/divazone/debug-container:master
```

## How to use `debug-container` on Native Kubernetes/Amazon Elastic Kubernetes Service?

1. Namespace Level Debugging: Running one Pod in namespace and `any node`
```bash
kubectl run -n default debug-container --restart=Never --rm -i --tty --image ghcr.io/divazone/debug-container:master -- /bin/bash
```

2. Namespace Level Debugging: Running one Pod in namespace and `specific node`
```bash
# Show all of nodes
kubectl get nodes
NAME                                          STATUS   ROLES    AGE   VERSION
ip-192-168-1-101.us-west-2.compute.internal   Ready    <none>   3h    v1.29.0
ip-192-168-1-102.us-west-2.compute.internal   Ready    <none>   3h    v1.29.0
ip-192-168-1-103.us-west-2.compute.internal   Ready    <none>   3h    v1.29.0

# Run the command
kubectl run -n default debug-container --restart=Never --rm -i --tty --overrides='{ "apiVersion": "v1", "spec": {"nodeName":"ip-192-168-1-103.us-west-2.compute.internal"}}' --image ghcr.io/divazone/debug-container:master -- /bin/bash
```

3. Node Level Debugging: Running one Pod on `specific node`
```bash
kubectl run -n default debug-container --image ghcr.io/divazone/debug-container:master \
  --restart=Never -it --attach --rm \
  --overrides='{ "apiVersion": "v1", "spec": { "nodeSelector":{"kubernetes.io/hostname":"ip-192-168-1-103.us-west-2.compute.internal"}, "hostNetwork": true}}' -- /bin/bash

# or
$ kubectl debug node/ip-192-168-1-103.us-west-2.compute.internal -it --image=ghcr.io/divazone/debug-container:master -- /bin/bash
Creating debugging pod node-debugger-aks-ip-192-168-1-103.us-west-2.compute.internal with container debugger on node ip-192-168-1-103.us-west-2.compute.internal.
If you don't see a command prompt, try pressing enter.

[root@ip-192-168-1-101 /]# chroot /host /bin/bash
[root@host /]# cat /etc/os-release | head -n 2
```

## Author
* **Uei-Rung King** <kingweirong@gmail.com>
