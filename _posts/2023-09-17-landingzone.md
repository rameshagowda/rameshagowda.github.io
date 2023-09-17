---
title: Deploying Azure Landing zones
date: 2023-09-17 12:00 -500
categories: [Architecture,Azure,Landingzone]
tags: [landing-zone, azure]
author: gowda
---

## Evaluating Deployment Options
### Use an Accelerator
    This is the recommended approach. It deployes the conceptual architecure, which provides governance, security, and management from the start.
    You could use azure portal, Bicep or terraform to deploy.
### Customize
    Customize the environment to meet very specfic rquirements. It starts small and aim to align with the conceptual architecture.

Lets deploy few scenarios using ALZ Accelerator

## Scenario 1:
    Requirements:
        - Scalable architecture
        - No IaC
        - Only public facing workloads

### Architecture
    - Deploy Enterprise-scale foundation using portal
    - Dedicated Management subscription
    - Online Application subscription
    - No identity, connectivity (network) or Corp management groups or subscription
    - Deploy Azure Policy

    ![Desktop View](/assets/img/landingzone/scenario1.png)

### Solution
    - login to portal and create 2 subscriptions. Here we are just creating subscriptions ahead of time and including them into landing zone. I call them Management and Online subscriptions.
    - 

    




