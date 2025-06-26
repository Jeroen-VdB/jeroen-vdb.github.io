---
title: "Comparing Azure Functions and Logic Apps Performance"
description: "Performance comparison showing that Stateless Logic Apps (Standard) match Azure Functions speed, while Stateful Logic Apps add approximately one second of latency to integration flows."
categories:
  - Azure
  - TIL
tags:
  - Azure Functions
  - Logic Apps
---

# TL;DR
Stateless Logic Apps (Standard) are just as fast as Azure Functions. Stateful Logic Apps (Standard) and Logic apps (Consumptions) add a significant delay, about a second, to your integration flow.

---

For a new integration project in Azure, we were, like always, deciding between the use of Azure Functions or Logic Apps for our integrations.

In this project there are 2 different integration types:

1. Synchronous where we need to expose a restful API that retrieves the data from a WCF Service.
1. Asynchronous where we have Event Hub on the one side and an on-premises FileShare on the other.

For integration type nr. 2 we had chosen the use of Logic Apps in combination with an On-Premises Data Gateway. Performance for these integrations is not of great importance.

For integration type nr. 1 we were still indecisive. Because we already use Logic Apps for the other integration type, it would be handy to also use them for the API integrations. But, the performance of these calls is important in this case.

Therefore, I did some performance comparisons between the different types of Logic Apps and Azure Functions. I used https://mock.code/200 as a fake back-end call. In order to test the calls, I used Postman and tried every type 10 times. Here are the results:

**Directly calling mock.code**

- Median duration: 300 ms
- Min duration: 112 ms
- Max duration: 423 ms

**Logic Apps (Consumption)**

- Median duration: 800 ms
- Min duration: 544 ms
- Max duration: 1226 ms

**Logic Apps (Standard — Stateful)**

- Median duration: 1000 ms
- Min duration: 582 ms
- Max duration: 2180 ms

**Logic Apps (Standard — Stateless)**

- Median duration: 150 ms
- Min duration: 144 ms
- Max duration: 528 ms

**Azure Functions (C# script)**

- Median duration: 140 ms
- Min duration: 134 ms
- Max duration: 484 ms

**Azure Functions (C# class library)**

- Median duration: 140 ms
- Min duration: 126 ms
- Max duration: 412 ms

As you can see, the difference between Azure Functions and the Stateless Logic Apps is almost negligible. While the stateful and consumption Logic Apps can add a delay of more than 1 second.

Conclusion: We will use the stateless Logic Apps for our integration type nr. 1 because we can easily consume a WCF service using a [Logic App Custom Connector](https://docs.microsoft.com/en-us/connectors/custom-connectors/create-register-logic-apps-soap-connector) and we are able to use just 1 App Service Plan for both types of integrations.