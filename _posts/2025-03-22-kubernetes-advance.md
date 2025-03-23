---
title: Kubernetes Ingress routing with TLS and network polices on KinD cluster
date: 2025-03-22 12:00 -500
categories: [Kubernetes, Cilium, CNI, Ingress, TLS]
tags: [cni, ingress, routing, networkpolicies]
author: gowda
---

## Introduction

Outline steps to implement some of the Kubernetes advanced concepts.

1. Create a local Kubernetes in Docker (KinD) cluster with multiple nodes. Kind cluster gives a similar experience as a production Kubernetes cluster on your local desktop.
2. Setup a simple ingress routing using Nginx ingress controller.
3. Introduce TLS encryption to ingress routing.
4. Compare the Network policies between Cilium CNI and Kubernetes CNI.
5. Discuss some of the advantages using Cilium as CNI in networking, observability and service mesh.

## Create Kubernetes KinD cluster locally

- Install KinD from here - <https://kind.sigs.k8s.io/docs/user/quick-start/>
- Create a cluster with 3 nodes - 1 Control node, 2 Worker nodes
- Allow control node to make requests to ingress controller over ports for both http (80) and https (443)

```yaml
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
name: dev
nodes:
  - role: control-plane
    kubeadmConfigPatches:
      - |
        kind: InitConfiguration
        nodeRegistration:
          kubeletExtraArgs:
            node-labels: "ingress-ready=true"
    extraPortMappings:
      - containerPort: 80
        hostPort: 80
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        protocol: TCP
  - role: worker
  - role: worker
```

- Now we have KinD cluster with Ingress enabled.
  ![Desktop View](/assets/img/k8s/kind.png)

## Ingress Routing with Nginx Controller

- Apply Nginx controller

        - kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

- Ingress controller is up and running
  ![Desktop View](/assets/img/k8s/ing.png)

## Application deployment and a service

- Create a deployment.

        - kubectl create deployment my-test-app --image=nginx

- Create a service for the deployment.

        - kubectl expose deployment my-test-app --name=my-test-app-service --type=ClusterIP --port=80 --target-port=80

### Ingress Resource

- Create ingress resource to define routing rules.

        - kubectl create ing my-ingress --rule="myapp.local/my-test-app=my-test-app-service:80" --annotation nginx.ingress.kubernetes.io/rewrite-target=/$2

- This is how Ingress rule looks like after execting the above command.
  ![Desktop View](/assets/img/k8s/ingrule.png)

### Local DNS mapping and Test the APP

        - Update the file /etc/hosts to create a record: 127.0.0.1 myapp.local

        - test it: curl http://example.local -v

- Access it from the browser:
  ![Desktop View](/assets/img/k8s/myapp.png)

## TLS enabled Ingress Resource

### Self Signed certificates

Real production workloads will always be encrypted with Certificate Authority (CA) signed TLS certificates. Here lets create self-signed certificates using using OpenSSL tool. This certificate will enable TLS encryption with ingress routing.

- Cretae a TLS Key.

        - openssl genrsa -out tls.key 2048

- Create TLS certificate - Note the domain name in CN which is matching with my local domain.

        - openssl req -x509 -new -nodes -key ingress-tls.key -subj "/CN=myapp.local” -days 10 -out tls.crt

- Optional - View certificate details

        - openssl x509 -in tls.crt -noout -text

### Kubernetes Secret

A Kubernetes secret for TLS is required. Lets create one.

        - kubectl create secret tls my-secret --cert=tls.crt --key=ingress-tls.key

![Desktop View](/assets/img/k8s/secret.png)

### Enable TLS in Ingress Reource

- Edit the ingress resource to enable TLS or delete and recreate the ingress resource.
- Delete the previous ingress resource rule

        - kubectl delete ing my-ingress

- Create ingress resource rule with tls enabled

        - kubectl create ing my-ingress --rule="myapp.local/my-test-app=my-test-app-service:80,tls=my-secret" --annotation nginx.ingress.kubernetes.io/rewrite-target=/$2

- Test the application with https.

        - curl https://example.local --insecure -v

- Access it from browser by accepting the insecure warning.
  ![Desktop View](/assets/img/k8s/myapp-tls.png)
  ![Desktop View](/assets/img/k8s/myapp-tlsb.png)
