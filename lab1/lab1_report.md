# Laboratory work #1

University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2023/2024

Group: K4110

Author: Semykin Vladislav Denisovich

Lab: Lab1

Date of create: 04.09.2023

Date of finished: XX.XX.2023

# Installing Docker and Minikube, my first manifest

## Description

This is the first laboratory work in which you will be able to test Docker, install Minikube and deploy your first pod-shell.

## Aim

Familiarize with the Minikube and Docker tools, deploy your first pod-shell.

## Progress of a work

### Downloading && Installing

For download [minikube](https://minikube.sigs.k8s.io/) used following command:

```console
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
```

Mean of flag <b>-L</b>: If the server reports that the requested page has moved to a different location (indicated with a Location: header and a 3XX response code), this option will make curl redo the request on the new place.

Mean of flag <b>-O</b>: Write output to a local file named like the remote file we get. The file will be saved in the current working directory.

Command to install minikube for my system (6.4.12-200.fc38.x86_64):

```console
sudo rpm -Uvh minikube-latest.x86_64.rpm
```

Mean of flag <b>-U</b>: Upgrades package

Mean of flag <b>-v</b>: Print verbose information - normally routine progress messages will be displayed.

Mean of flag <b>-h</b>: Print 50 hash marks as the package archive is unpacked.

### Getting started with <b>minikube</b>

After installation we need to deploy an one-node Kubernetes cluster with the following command:

> If appears window that requires root password, just close it or push "Cancel". Because the driver "kvm2" should not be installed with root priviliges.

```bash
minikube start --kubernetes-version=v1.26.8
```

This command starts cluster with the specified Kubernetes version: The Kubernetes version that the minikube VM will use (ex: v1.2.3, 'stable' for v1.27.4, 'latest' for v1.28.0-rc.1). Defaults to 'stable'.

I choose the latest version on my OS.

### Downloading docker image

For example, we have been choose [HashiCorp Vault](https://www.vaultproject.io/). Command to [download](https://hub.docker.com/_/vault/) it:

```bash
sudo docker pull hashicorp/vault:1.13.3
```

As you can see, I chose 1.13.3 version. The colon (:) serves to specify tag of the image.

> Do not forget for start docker service with the following command:
>
> ```bash
> sudo service docker start
> ```
