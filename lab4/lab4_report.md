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

> Original instructions for assigning IP addresses in Calico [link](https://projectcalico.docs.tigera.io/networking/assign-ip-addresses-topology)

- After that, you need to develop a manifest for Calico that, based on the previously specified labels, would assign IP addresses to "submit" based on the pools of IP addresses that you specified in the manifest.

- You need to create a `deployment` with 2 replicas of the container [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) and pass variables to these replicas: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME'.

- Create a service through which you will have access to these "pods". The choice of the type of service is at your discretion.

- Launch port forwarding mode in `minikube` and connect to your containers via a web browser.

- Check the variables `Container name` and `Container IP` on the page in the web browser. Do they change? If so, why?

- Using `kubectl exec`, go to any "pod" and try to ping the "pod" using the `FQDN` name of the neighboring "pod", the results of the pings must be attached to the report.

## Progress of a work
