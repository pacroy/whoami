# whoami application

This is a test application for deploying on a Kubernestes cluster with load balancer provider e.g. [Metallb](https://metallb.universe.tf/) and ingress controller e.g. [Traefik](https://traefik.io/).

For more information how to use this repository, check out my blog here:
https://pacroy.medium.com/single-node-kubernetes-on-home-lab-using-microk8s-metallb-and-traefik-7bb1ea38fcc2

## Usage

### Deploy Application

Create namespace, deployment, and service:

```sh
kubectl create ns whoami
kubectl apply -f whoami.yml -n whoami
```

### Exposing Service

a. Using load balancer (requires [metallb](https://metallb.universe.tf/) for bare metal):

```sh
apply -f service-lb.yml -n whoami
```

b. Using simple ingress:

```sh
apply -f ingress.yml -n whoami
```

c. Using [ingress-nginx](https://github.com/kubernetes/ingress-nginx/) (requires [cert-manager](https://cert-manager.io/)):

```sh
apply -f ingress-nginx.yml -n whoami
```

d. Using [Traefik's IngressRoute](https://doc.traefik.io/traefik/user-guides/crd-acme/#traefik-routers) (requires a [certificate resolver](https://doc.traefik.io/traefik/https/acme/#certificate-resolvers)):

```
kubectl apply -f ingressroute.yml -n whoami
```

### Securing Traffic

Forward HTTP traffic to HTTPS using [Traefik's middleware](https://doc.traefik.io/traefik/middlewares/overview/) and use [Traefik's TLSOption](https://doc.traefik.io/traefik/https/tls/#tls-options) to enforce TLS v1.2+ for HTTPS.

Install Traefik with a TLS challenge certificate resolver using [Tarefik Helm chart](https://github.com/traefik/traefik-helm-chart).

```sh
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
kubectl create namespace traefik
helm install traefik traefik/traefik -n traefik --values values.yml
```

Create IngressRoute, Middleware, and TLSOption.

```sh
kubectl apply -f ingressroute-tls.yml -n whoami
kubectl apply -f tlsoption.yml
kubectl apply -f middleware-https.yml
kubectl apply -f ingressroute-http.yml -n whoami
```
