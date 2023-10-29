# Laboratory work #4

## Communication networks in Minikube, CNI and CoreDNS

University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4110c\
Author: Semykin Vladislav Denisovich\
Lab: Lab4\
Date of create: 27.10.2023\
Date of finished: XX.XX.2023

## Description

This is the last laboratory work in which you will get acquainted with communication networks in Minikube. The peculiarity of Kubernetes is that it simultaneously has `underlay` and `overlay` networks, and management can be organized by various CNIs.

## Aim

To get acquainted with CNI Calico and the `IPAS Plugin` function, to study the features of CNI and Core DNS.

## Tasks

- When starting minikube, install the `CNI=calico` plugin and the `Multi-Node Clusters` operation mode at the same time, as part of this laboratory work, you need to deploy 2 nodes.

> Original instructions for installing Calico in Minikube [link](https://projectcalico.docs.tigera.io/getting-started/kubernetes/minikube)
> Original instructions for enabling 2 nodes in Minikube [link](https://minikube.sigs.k8s.io/docs/tutorials/multi_node /)

- Check the work of the CNI Calico plugin and the number of nodes, attach the results of the check to the report.

- To test the work of Calico, we will try one of the functions called `IPAM Plugin'.

- To check the `IPAM` mode, it is necessary to specify the `label` for previously launched nodes based on the rack or geographical location (of your choice).

> [Original instructions for assigning IP addresses in Calico](https://projectcalico.docs.tigera.io/networking/assign-ip-addresses-topology)

- After that, you need to develop a manifest for Calico that, based on the previously specified labels, would assign IP addresses to "submit" based on the pools of IP addresses that you specified in the manifest.

- You need to create a `deployment` with 2 replicas of the container [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) and pass variables to these replicas: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME'.

- Create a service through which you will have access to these "pods". The choice of the type of service is at your discretion.

- Launch port forwarding mode in `minikube` and connect to your containers via a web browser.

- Check the variables `Container name` and `Container IP` on the page in the web browser. Do they change? If so, why?

- Using `kubectl exec`, go to any "pod" and try to ping the "pod" using the `FQDN` name of the neighboring "pod", the results of the pings must be attached to the report.

## Progress of a work

### Starting 2 nodes of `minikube` with the `Calico` plugin

> Do not forget to start the `docker` service and login
>
> ```bash
> sudo systemctl start docker
> sudo docker login
> ```

```bash
minikube start --driver=docker --nodes 2 -p multinode-app --network-plugin=cni --cni=calico
```

```console
<loveit@fedora ~>$ minikube start --driver=docker --nodes=2 --network-plugin=cni --cni=calico
😄  [multinode-app] minikube v1.31.2 on Fedora 38
✨  Using the docker driver based on user configuration
❗  With --network-plugin=cni, you will need to provide your own CNI. See --cni flag as a user-friendly alternative
📌  Using Docker driver with root privileges
👍  Starting control plane node multinode-app in cluster multinode-app
🚜  Pulling base image ...
❗  minikube was unable to download gcr.io/k8s-minikube/kicbase:v0.0.40, but successfully downloaded docker.io/kicbase/stable:v0.0.40 as a fallback image
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.27.4 on Docker 24.0.4 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring Calico (Container Networking Interface) ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner

👍  Starting worker node multinode-app-m02 in cluster multinode-app
🚜  Pulling base image ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🌐  Found network options:
    ▪ NO_PROXY=192.168.58.2
❗  This container is having trouble accessing https://registry.k8s.io
💡  To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
🐳  Preparing Kubernetes v1.27.4 on Docker 24.0.4 ...
    ▪ env NO_PROXY=192.168.58.2
🔎  Verifying Kubernetes components...
🏄  Done! kubectl is now configured to use "multinode-app" cluster and "default" namespace by default
```

Let's check our created nodes and pods:

```bash
kubectl get nodes && kubectl get pods --selector k8s-app=calico-node
```

```console
NAME                STATUS   ROLES    AGE   VERSION
multinode-app       Ready    <none>   53s   v1.27.4
multinode-app-m02   Ready    <none>   29s   v1.27.4
NAMESPACE     NAME                READY   STATUS    RESTARTS      AGE
kube-system   calico-node-8h2vt   1/1     Running   1 (91s ago)   15m
kube-system   calico-node-rttmf   1/1     Running   1 (39s ago)   14m
```

Here we can see 2 nodes and 2 pods from the output of previous commands. Let notice, that the **each pod started on each node**.

### IPAM Plugin

Firstly, we need to add labels to our minikube nodes, I'll do it with [`kube-shell`]() and `kubectl edit` command:

```bash
kubectl edit node multinode-app
kubectl edit node multinode-app-m02
```

Command `kubectl edit` would open your default text editor with the manifest of specified Kubernetes resource. If smth goes wrong (you made a mistakes or smth else), kubectl reports about it and opens the certain manifest for make a corrections, otherwise `kubectl` updates resource like command `kubectl apply -f <manifest_name.yaml>`.

<img src="imgs/1.png">

<img src="imgs/2.png">

Let check our labels:

```bash
kubectl get nodes -l nodeName=fstNode
kubectl get nodes -l nodeName=sndName
```

> Yes, I did their names intentionally.

<img src="imgs/3.png">

Now, we need to delete default IP pool and create 2 different IP pools for each node. [Instructions there](https://docs.tigera.io/calico/latest/networking/ipam/assign-ip-addresses-topology).

```bash
kubectl get ippool
```

```console
NAME                  AGE
default-ipv4-ippool   31m
```

```yaml
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: multinode-app
spec:
  cidr: 192.168.0.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: nodeName == "fstNode"

---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: multinode-app-m02
spec:
  cidr: 192.168.1.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: nodeName == "sndName"
```

Deletion of the default IP pool and applying manifest to create 2 custom IP pools:

> How to install [`calicoctl`](https://docs.tigera.io/calico/latest/operations/calicoctl/install#install-calicoctl-as-a-binary-on-a-single-host)
>
> ```bash
> curl -L https://github.com/projectcalico/calico/releases/download/v3.26.3/calicoctl-linux-amd64 -o kubectl-calico
> chmod +x ./calicoctl
> mv calicoctl <YOUR_PATH_ENV_VAR>
> ```
>
> To view your PATH variable values you can just do this:
>
> ```bash
> $PATH
> ```

````

```bash
kubectl delete ippool default-ipv4-ippool && calicoctl apply -f create_ippools.yaml
````

```console
ippool.crd.projectcalico.org "default-ipv4-ippool" deleted
Successfully applied 2 'IPPool' resource(s)
```

```bash
calicoctl get ippool
```

```console
NAME                CIDR             SELECTOR                
multinode-app       192.168.0.0/24   nodeName == "fstNode"   
multinode-app-m02   192.168.1.0/24   nodeName == "sndName"
```
