# Laboratory work #2

## Deployment of a web service in Minikube, access to the web interface of the service. Monitoring of the service

University: [ITMO University](https://itmo.ru/ru/)<p>
Faculty: [FICT](https://fict.itmo.ru)<p>
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)<p>
Year: 2023/2024<p>
Group: K4110<p>
Author: Semykin Vladislav Denisovich<p>
Lab: Lab2<p>
Date of create: 18.10.2023<p>
Date of finished: XX.XX.2023<p>

## Description

In this lab, you will get acquainted with the deployment of a full-fledged web service with multiple replicas.

## Aim

Familiarize yourself with the types of container deployment "controllers", familiarize yourself with network services and deploy your web application.

## Tasks

- You need to create a deployment with 2 replicas of the <b>ifilyaninitmo/itdt-contained-frontend:master</b> container and pass variables to these replicas: <b>REACT_APP_USERNAME</b>, <b>REACT_APP_COMPANY_NAME</b>.
- Create a service through which you will have access to these "pods". The choice of the type of service is at your discretion.
- Launch port forwarding mode in minikube and connect to your containers via a web browser.
- Check the <b>REACT_APP_USERNAME</b>, <b>ROOT_APP_COMPANY_NAME</b> and Container name variables on the web browser page. Do they change? If so, why?
- Check the container logs, attach the logs to the report.

## Progress of a work

### Creating deployment

Firstly, we need to add container <b>ifilyaninitmo/itdt-contained-frontend:master</b> to our Docker with the following command:

```bash
docker pull ifilyaninitmo/itdt-contained-frontend:master
```

> Do not forget to start docker service and sing in if it's necessary:
>
> ```bash
> sudo systemctl start docker
> sudo docker login
> ```

#### Output

```console
<loveit@fedora 2023_2024-intoduction_to_distributed_technologies-k4110-semykin-v-d>$ sudo docker pull ifilyaninitmo/itdt-contained-frontend:master
master: Pulling from ifilyaninitmo/itdt-contained-frontend
213ec9aee27d: Pull complete
7eb06cd79147: Pull complete
765a58e53190: Pull complete
beee7f5da459: Pull complete
ef9be46a2fcd: Pull complete
4f4fb700ef54: Pull complete
de687e97b091: Pull complete
c26a1b0cdf5b: Pull complete
76b9ce80a97c: Pull complete
Digest: sha256:08756f1022aea55538e740562aa980b56be6241d2166e6d8d6521386e0876dbe
Status: Downloaded newer image for ifilyaninitmo/itdt-contained-frontend:master
docker.io/ifilyaninitmo/itdt-contained-frontend:master
```


