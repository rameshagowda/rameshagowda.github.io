---
title: Kubernetes Operator pattern using Operator-sdk | Kubebuilder
date: 2024-11-10 12:00 -500
categories: [Architecture, Kubernetes, API]
tags: [operator-sdk, kubernetes, api, go]
author: gowda
---

## Introduction

Kubernetes Operator pattern is the way to extend your Kubernetes for creating additional features using custom resources which are not available in Kubernetes cluster.

In simple term,
Kubernetes Operator = CR + CRD + Controller logic + Api

When do I use Kubernetes Operator? Use it for Day-2 production operations which you is missing in default kubernetes.
Some of the examples include - Increasing the Pods during business hours and decreasing after business hours automatically. - Alerting an Production support Operator via Teams, Slack if a status of Pod changes. - Taking periodic backup of database or files - Package your solution as an installer for 3rd parties / customers

NOTE: This example shows the PoC but it requires to create an image of the solution and push to container registry before use it in production cluster.
<https://github.com/rameshagowda/k8s-operator-scaler>

### Prerequisites

1. latest golang installed
2. Install Docker Desktop, Kind - Kubernetes cluster in Docker
3. Install Operator-sdk or Kubebuilder - I used Operator-sdk
4. Should know basics of Kubernetes and its infrastructure.

## Architecture

![Desktop View](/assets/img/k8s/operator.png)

Basic components of Kubernetes Operator pattern

1. Custom Resource (CR): This is a way to define a new type of resource in Kubernetes, tailored to your specific application needs.

2. Custom Resource Definition (CRD): This defines the schema for your custom resource, telling Kubernetes about the new resource type and its properties.

3. Controller Logic or Reconcile loop: This is the brain of the operator. It watches for changes to the custom resources and takes action to ensure the desired state is maintained. This is also called Reconcile loop.

4. API: The Kubernetes API is used to interact with the custom resources and the controller.

## Implementation

    1. Create a project folder in VS Code
    2. Create operator boiler-plate template using operator-sdk.
        domain is the api group where all your custom crd, cr, controller logic reside
        repo is the github repo

        ``` shell
        operator-sdk init --domain rcgowda-operator.io --owner "ramesha c gowda" --repo github.com/rameshagowda/k8s-operator-scaler
        ```
    3. Create an API for your custom resource. kind is your custom resource name. Say yes to create resource and controller.
        ``` shell
        operator-sdk create api --kind Scaler --group api --version v1alpha1
        ```
    4. Compare the code in git repo provided to copy the logic. You should be looking at API and Controller folder.
    5. Once your logic is added, you can use make and kubectl commands to create CR and CRD, build and test it.
    Make sure to create a cluster using kind
        ``` shell
        make manifests
        kind create cluster
        kubectl apply -f config/crd/bases/api.<your yml file>
        kubectl get crd, cr
        make run
        ```
    NOTE: Follow the youtube link to debug and test the operator.

## References:

- <https://www.youtube.com/watch?v=FHjLL5e7h00>
- <https://medium.com/operators/operator-pattern-kubernetes-openshift-380ddc6a147c>
