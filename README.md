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

d. Using [Traefik](https://traefik.io/)'s IngressRoute:

```
kubectl apply -f ingressroute.yml -n whoami
```

### Securing Traffic

Forward HTTP traffic to HTTPS using Traefik's middleware and use TLS v1.2+ for HTTPS.

```sh
kubectl apply ingressroute-tls.yml -n whoami
kubectl apply -f tlsoption.yml
kubectl apply -f middleware-https.yml
kubectl apply -f ingressroute-http.yml -n whoami
```
