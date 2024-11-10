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

### Prerequisites

1. latest golang installed
2. Install Docker Desktop, Kind - Kubernetes cluster in Docker
3. Install Operator-sdk or Kubebuilder - I used Operator-sdk
4. Should know basics of Kubernetes and its infrastructure.

## Architecture

![Desktop View](/assets/img/k8s/operator.png)

Basic components of Kubernetes Operator pattern

1. <b>Custom Resource (CR):</b> This is a way to define a new type of resource in Kubernetes, tailored to your specific application needs.

2. <b>Custom Resource Definition (CRD):</b> This defines the schema for your custom resource, telling Kubernetes about the new resource type and its properties.

3. <b>Controller Logic or Reconcile loop:</b> This is the brain of the operator. It watches for changes to the custom resources and takes action to ensure the desired state is maintained. This is also called Reconcile loop.

4. <b>API:</b> The Kubernetes API is used to interact with the custom resources and the controller.


## References:
- https://www.youtube.com/watch?v=FHjLL5e7h00
- https://medium.com/operators/operator-pattern-kubernetes-openshift-380ddc6a147c
- https://github.com/rameshagowda/k8s-operator-scaler/tree/main
