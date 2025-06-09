---
title: API Architectural Styles - SOAP, REST, GraphQL, gRPC, WebSocket and Webhooks
date: 2025-06-05 12:00 -500
categories: [API, Architecture, Automation, Testing]
tags: [api, testing, postman]
author: gowda
---

## Introduction

APIs are the backbone of modern software design and integration, enabling seamless communication between disparate systems. SOAP and REST have long dominated the enterprise for their structured and accessible approaches. Modern alternatives like GraphQL, gRPC, WebSocket, and Webhooks offer greater flexibility, enhanced performance, and real-time capabilities. Each architectural style brings unique strengths, allowing software architects and engineers to choose the best fit for specific use cases and operational demands.

These API styles sample endpoints can be tested by importing into Postman Collections. <https://github.com/rameshagowda/api-styles>

API Test strategy is important to consider for automation which is provided in the end.

## 1. SOAP

SOAP is an XML-based protocol with a robust, standardized contract defined via WSDL, which ensures secure and reliable communication in enterprise environments. It is ideal for systems that require strict message formatting, built-in error handling, and formal transaction support.

ASMX (in the example) refers to the file extension used for legacy SOAP-based web services in Microsoft's ASP.NET framework. These ".asmx" files serve as endpoints that automatically handle SOAP messaging—packaging, transmitting, and receiving XML messages according to the SOAP protocol.

### How to make SOAP request using Postman?

1. Use this SOAP endpoint to test - https://www.w3schools.com/xml/tempconvert.asmx
2. Select POST from the request method dropdown list.
3. In "Headers" tab,

- Add Content-Type to "application/soap+xml; charset=utf-8"
- SOAPAction to "#POST"

4. In the Body tab, select raw and choose XML from the dropdown list.This is how sample request and response looks like for a SOAP endpoint.
   ![Desktop View](/assets/img/apis/soap.png)

#### References

<https://learning.postman.com/docs/sending-requests/soap/making-soap-requests/>
<https://blog.postman.com/making-http-soap-requests-with-postman/>

## 2. REST

REST uses standard HTTP methods to manipulate resources, typically exchanging data in JSON or XML. Its stateless, lightweight nature makes it highly scalable and easy to integrate with web and mobile applications.

      - GET example

        URL: https://jsonplaceholder.typicode.com/posts

        Method: GET

        Returns: A list of sample posts in JSON format.


      - POST example

        URL: https://jsonplaceholder.typicode.com/posts

        Method: POST

        Headers: Content-Type: application/json

        Body (raw JSON): {
              "title": "New Post Title",
              "body": "This is the content of the post.",
              "userId": 1
              }

```bash
    curl --location 'https://jsonplaceholder.typicode.com/posts'
```

```bash
    curl --location 'https://jsonplaceholder.typicode.com/posts' \
    --header 'Content-Type: application/json' \
    --data '{
      "title": "YET ANOTHER Post Title",
      "body": "This is the content of the post.",
      "userId": 1
    }
    '
```

## 3. GraphQL

Unlike REST, which uses multiple endpoints to access different data sets, GraphQL streamlines querying by accessing all data through a single endpoint. It lets clients specify exactly which fields they need, eliminating over-fetching and under-fetching inherent in traditional REST designs. With its strongly typed schema and flexible query language, GraphQL enables more efficient, self-documenting, and evolvable APIs.

GraphQL requests can perform three types of operations:

      - Query - Retrieves data from the server. Queries specify the required data fields and can include arguments for more precise data retrieval.

      - Mutation - Manipulates data on the server, including creating, updating, or deleting records. Mutations specify the fields to be returned after the operation and use arguments to detail the manipulation.

      - Subscription - Gets real-time data updates from the server. Subscriptions enable clients to listen to specific data fields and receive updates automatically over a persistent connection.

![Desktop View](/assets/img/apis/graphQL.png)

### References

- <https://graphql.org/learn/>
- <https://rickandmortyapi.com/documentation>
- <https://www.youtube.com/watch?v=xMCnDesBggM&list=PL4cUxeGkcC9gUxtblNUahcsg0WLxmrK_y&index=1>
- <https://learning.postman.com/docs/sending-requests/graphql/graphql-overview/>

## 4. gRPC

### References

## 5. WebSocket

WebSockets establish a persistent connection between client and server, enabling realtime, bi-directional data exchange without repeated HTTP handshakes.

After an initial HTTP upgrade, they use a dedicated protocol over TCP, reducing overhead and latency for interactive applications.

They are widely used for chat, gaming, live updates, and other scenarios where continuous, rapid data transmission is essential.

Socket.io (NodeJS) and SignalR (MS DotNet) leverage WebSockets for realtime, reliable and low latency communications. They can fallback to other transport protocols like SSE (Server Sent events) or long polling if websocket connection is not supported by client or network environment.

### References

Difference between Http Polling, SSE (Server Sent Events), WebSockets and Webhooks.

- <https://www.youtube.com/watch?v=JQoPuXAf92U>
- <https://www.youtube.com/watch?v=6RvlKYgRFYQ>

WebSockets design and testing

- <https://www.youtube.com/watch?v=X_DdIXrmWOo>
- <https://www.youtube.com/watch?v=ImzYxO3Lsvc>
- <https://www.youtube.com/watch?v=aSPHr6dbMmo>

## 6. Webhooks

### References

Difference between Http Polling, SSE (Server-Sent Events), WebSockets and Webhooks.

- <https://www.youtube.com/watch?v=JQoPuXAf92U>
- <https://www.youtube.com/watch?v=6RvlKYgRFYQ>

## Mix API styles in the same Product?

      Yes, of course, we can use all of them together to execute an API strategy. Each API styles are best suited for certain use cases. For example, REST APIs are user friednly and can be used to expose to external users. gRPC is best suited for internal client server use cases like in microservices. So we can provide REST endpoints to external users, but internal transactions, calculations, communications can be handled with gRPC

## API styles in one image

![Desktop View](/assets/img/apis/compare.png)

## API Protocols

## API test automation strategy

- Unit Test
- Integration Test
  - Using Mocks
  - Using variables to exchange info between API calls
- Contract test and Schema validation
- End to end API test
  - Functional Test
  - Load and Performance Test
  - SQL Injection security check
  - Regression Testing

### References for API tests using Postman

- <https://www.youtube.com/watch?v=oXW-C2bM0wE>
- <https://www.postman.com/postman/test-examples-in-postman/documentation/zuy6un3/intro-to-writing-tests-with-examples>
- <https://community.postman.com/categories>
- <https://www.youtube.com/watch?v=1qU6fIDht-s&list=PL6tu16kXT9PqcGCrFRw7mBFdqwG7x7Kcz&index=6>
- <https://www.youtube.com/watch?v=Ix6z1kBweuk>
- <https://learning.postman.com/docs/tests-and-scripts/tests-and-scripts/>
