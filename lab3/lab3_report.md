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

### Writing the ConfigMap object

**ConfigMap** is the main object for storing configuration data in Kubernetes. It can be represented as a named set of key—value pairs in which the configuration is stored.

The mechanism for controlling resource consumption in a cluster is the use of **namespaces**. The namespace in Kubernetes provides a way to divide a cluster into separate parts. In this case, the namespace is used to combine all created resources (service and deployment, in which 2 replica-set objects are created to manage pods).

At first, we need to combine all the resources, to do this we need namespace:

```yaml
apiVersion: apps/v1
kind: Namespace
metadata:
  name: common_namespace
```

Now, we can write YAML for ConfigMap object, where we define 2 environment variables from the previous [laboratory work](https://github.com/ViNN280801/2023_2024-intoduction_to_distributed_technologies-k4110-semykin-v-d/blob/main/lab2/lab2_report.md).

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: react-web-app
data:
  # There are pairs of key-value, that specified as "key: value"
  REACT_APP_USERNAME: "Semykin Vladislav Denisovich"
  REACT_APP_COMPANY_NAME: "ITMO. ICT Faculty"
```

YAMLs for deployment and service got from previous laboratory work, but from `react_manifest.yaml` removed these 2 environment variables which added in ConfigMap object.

For convenience we can combine all the manifests to one single manifest.

### Enabling Ingress resources

> **Ingress** is a kind of load balancer placed in front of the service. It transmits requests from clients to the service. The service, in turn, sends them to suitable pod shells based on the label selector.

**Services** are well suited for routing the internal traffic of your cluster (for example, from one microservice to another). **Ingress** objects, in turn, are designed to redirect external requests to your cluster and a suitable microservice. Ingress can direct traffic to different services based on the rules you set. The most common routing of requests is based on their URLs. Among other things, Ingress supports secure connections based on the TLS protocol (formerly known as SSL). If you have many different services and applications on the same domain, they can all have a common TLS certificate and a single Ingress resource can manage these connections.

To enable **Ingress** resources we need to execute the following command:

```bash
minikube addons enable ingress
```

#### Output

```console
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20230407
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20230407
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.8.1
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
```

### Generating a TLS certificates

### Creating an Ingress object as manifest

### Organazation scheme

## Conclusion
