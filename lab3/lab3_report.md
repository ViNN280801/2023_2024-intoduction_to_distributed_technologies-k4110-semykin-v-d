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
  name: common-namespace
```

Now, we can write YAML for ConfigMap object, where we define 2 environment variables from the previous [laboratory work](https://github.com/ViNN280801/2023_2024-intoduction_to_distributed_technologies-k4110-semykin-v-d/blob/main/lab2/lab2_report.md).

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
  namespace: common-namespace
data:
  # There are pairs of key-value, that specified as "key: value"
  REACT_APP_USERNAME: "Semykin Vladislav Denisovich"
  REACT_APP_COMPANY_NAME: "ITMO. ICT Faculty"
```

YAMLs for deployment and service got from previous laboratory work, but from `react_manifest.yaml` removed these 2 environment variables which added in ConfigMap object.

For convenience we can combine all the manifests to one single manifest by using `---`, for example:

```yaml
# Step 1: Creating namespace to combine all necessary Kubernetes objects
apiVersion: apps/v1
kind: Namespace
metadata:
  name: common-namespace

# Step 2: Creating ConfigMap object with 2 ENV vars
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: config
  namespace: common-namespace
data:
  # There are pairs of key-value, that specified as "key: value"
  REACT_APP_USERNAME: "Semykin Vladislav Denisovich"
  REACT_APP_COMPANY_NAME: "ITMO. ICT Faculty"
```

### ResourceQuota

**ResourceQuota** - constraints supply of the process time and memory not only for containers, and for namespaces. The _prefferable_ approach is to use **ResourceQuotas** to limimt count of pods, which can be executing in there. Let's write manifest for it:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: react-web-app
  namespace: common-namespace
spec:
  hard:
    pods: "2" # Here we define the limit on count of the pods
```

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

### Generating a TLS certificate

Self-signed TLS certificates are suitable for personal use or for applications that are used internally within an organization.\
To generate TLC certificate with my signature, I used [this resourse](https://www.linode.com/docs/guides/create-a-self-signed-tls-certificate/), that describes this process in all details.

**Generate the Certificate**:

```bash
openssl req -new -newkey rsa:4096 -x509 -sha256 -days 363 -nodes -out mycertificate363.crt -keyout mykey.key
```

- `newkey rsa:4096`: Create a 4096 bit RSA key for use with the certificate. RSA 2048 is the default on more recent versions of OpenSSL but to be sure of the key size, you should specify it during creation.
- `x509`: Create a self-signed certificate.
- `sha256`: Generate the certificate request using 265-bit SHA (Secure Hash Algorithm).\
- `days`: Determines the length of time in days that the certificate is being issued for. For a self-signed certificate, this value can be increased as necessary.\
- `nodes`: Create a certificate that does not require a passphrase. If this option is excluded, you will be required to enter the passphrase in the console each time the application using it is restarted.

#### Output

```console
......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.....+.......+......+.....+.+...+...+.....+...+................+......+..+.......+..............+......+.+........+......+..........+...+......+............+..+.+..+.......+..+...+.+..............+.+...........+...+......+............+...+....+......+..............+...+...+.......+...+..+...+......+.+..............+....+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...............+.....................+......+..+.......+...........+...+....+...+...+......+.....+......+...+............+..................+.........+...........................+................+..+.+......+...+.....+......+.+..................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.......+...+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+......+.+...............+.....+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.......+...+....+........+...........................+...+............+.......+.........+..+...+..........+...........+......+............+......+.+......+..+.......+.....+....+.........+...........+......+.............+.................+...+......+.+...+...+...+..+....+......+..+..................+....+...+..+.+.....+.......+......+.........+..+..........+...............+.........+..+............+......+.........+...+..........+.........+......+.....+.........+........................+..........+.....+....+...+..+..................+.........................+.....+.......+............+........+.+......+...+...+............+.....+......+.+........+......................+.....+.+.....+...+....+.................+....+......+...............+...+..+....+.................+.......+..+.+.....+...+...............+...................+...........+.....................+....+.....+................+......+.....+....+......+...+..+.........+..................+.+.........+...+.....+...+.+..............+.+.........+......+...........+.......+...+.....+.........+....+.........+......+......+....................+................+...+......+........+...+.............+............+..+..........+......+..+...+.+........+.......+.........+......+........+...+..........+........+.+...+..+.........+.........+...............+.....................+....+...+..+.+.....+............+............+......+.+...+..+....+..+.......+...............+.....+...+..................+......+...+................+.........+..+.+....................+..........+.........+...........+.......+...+.....+..........+...+......+......+.....+.......+...+.....+.........+.........+.......+.................................+..+....+...+..+....+......+.....+.........+............+.......+...+..............+.+..+...+.........+......+............................+...........+.+..+.+............+..+.......+.....+.........+....+..+......+....+.....+......+...+.......+.....+...+.....................+.+..+.......+...+...........+....+...+............+...............+.....+.+..+...+.+......+.................+.+.................+..................+.+......+........+....+......+...........................+...............+..+.+.....+......+.............+.....+............+......+.......+...+.........+........+......+....+........+....+...+..+.+........+.......+.....+....+...+..+..........+.....+......+...+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:Ru
State or Province Name (full name) []:SpB
Locality Name (eg, city) [Default City]:Saint-Petersburg
Organization Name (eg, company) [Default Company Ltd]:ITMO
Organizational Unit Name (eg, section) []:ICT
Common Name (eg, your name or your server's hostname) []:vladislavsemykin
Email Address []:mymail@mail.ru
```

This command creates a self-signed certificate (mycertificate363.crt) that is valid for 363 days.

**Impelementation certificate in minikube**

Creating the Kubernetes object - **Secret** with type **tls**. This specific type of Secret is commonly used for storing TLS (Transport Layer Security) certificates, which are used for securing network communication within a Kubernetes cluster.

```bash
kubectl create secret tls vladislavsemykin-tls --key=mykey.key --cert=mycertificate363.crt
```

`vladislavsemykin-tls`: Name that is giving to the Secret object.
`--key=mykey.key`: Flag that specifies the private key file that is part of your TLS certificate.
`--cert=mycertificate363.crt`: This flag specifies the TLS certificate file. The mycertificate363.crt file contains the public certificate that corresponds to the private key.

#### Output

```console
secret/vladislavsemykin-tls created
```

### Creating an Ingress object as manifest

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: react-web-app
  namespace: common-namespace
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: HTTP
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
  labels:
    name: react-web-app
spec:
  rules:
    - host: vladislavsemykin # Domain hostname
      http:
        paths:
          - pathType: Prefix
            path: /
            backend:
              service:
                name: react-web-app
                port:
                  number: 3000
  tls:
    - hosts:
        - vladislavsemykin # Domain hostname
      secretName: vladislavsemykin-tls
```

Applying manifest `start_web_app.yaml` with the command:

```bash
kubectl apply -f start_web_app.yaml
```

### Organazation scheme

## Conclusion
