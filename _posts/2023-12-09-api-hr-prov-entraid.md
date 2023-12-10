---
title: API-driven HR Provisioning with Azure EntraID
date: 2023-12-08 12:00 -500
categories: [Architecture,Azure,API,EntraID,HR]
tags: [api, entraid, azure, hr]
author: gowda
---

## Introduction

This blog describes how to dynamically provision users in Entra Id from any HR system. This is api-driven approach as apposed to point-to-point provision using Azure provided connectors.

This can also be enhanced to writecback few fields into the HR system from Entra Id. Examples for these fields can be email Id or a user Id in Entra. This means that there must be an orchestrator software that can help by firstly converting data from hr system into SCIM+JSON format and calling Entra Id with the Microsoft graph Post endpoint. Secondly, it pulls writeback fields from Entra Id and makes a call to hr system to update.

### Prerequisites

1. Minimum P1 license for Entra Id.
2. Familiarity with Enterprise app creation and App registration process.
3. Familiarity with Microsoft Graph APIs.
4. Postman or Bash: To test.

## Architecture

![Desktop View](/assets/img/hr-system/api-driven-arch.png)

![Desktop View](/assets/img/hr-system/seq-diagram.png)

### Steps

References links explain end to end process thoroughly.

## References