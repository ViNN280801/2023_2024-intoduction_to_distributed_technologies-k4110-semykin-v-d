# Laboratory work #3

## Certificates and "secrets" in Minikube, secure data storage.

University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4110c\
Author: Semykin Vladislav Denisovich\
Lab: Lab2\
Date of create: 18.10.2023\
Date of finished: XX.XX.2023\

## Description

In this laboratory work, you will get acquainted with certificates and "secrets" in Minikube, the rules for safe data storage in Minikube.

## Aim

Get acquainted with certificates and "secrets" in Minikube, the rules of safe data storage in Minikube.

## Tasks

- You need to create a `ConfigMap` with the variables: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME'.

- You need to create a `ReplicaSet` with 2 replicas of the container [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) and using the previously created `ConfigMap` pass the variables `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME'.

- Enable `minikube addons enable ingress` and generate a TLS certificate, import the certificate into minikube.

- Create an ingress in minikube, where the previously imported certificate is specified, the FQDN that you will use to log in and the name of the service that you created earlier.

> If you are doing this work on Windows/macOS to access ingress you need to use the `minikube tunnel` command to the created ingress.
> If you are doing this work on Windows/macOS to access ingress, you need to add the ip address and your FQDN to the hosts.

- In the `hosts`, enter the FQDN and IP address of your ingress and try to go to the browser by the FQDN name.

- Log in to the web application using your FQDN using HTTPS and check for the certificate.

> Usually in the browser it is a small lock next to the FQDN of the site, click on it and take a screenshot with the information.

## Progress of a work

...

## Conclusion
