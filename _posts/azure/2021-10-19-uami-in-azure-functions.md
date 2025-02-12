---
title: "User Assigned Managed Identity in Azure Functions with Azure.Identity"
description: "Exception while executing function: MyFunction ManagedIdentityCredential authentication failed: No MSI found for specified ClientId/ResourceId.    
Status: 400 (Bad Request)"
categories:
  - Azure
  - Issues
tags:
  - Managed Identity
  - Azure Functions
  - Azure.Identity
---

Today was trying to authenticate my Azure Function app with a User Assigned Managed Identity using the newer [Azure.Idenity](https://www.nuget.org/packages/Azure.Identity) NuGet package.

Quickly I ran into the following error:
> Exception while executing function: MyFunction ManagedIdentityCredential authentication failed: No MSI found for specified ClientId/ResourceId.    
Status: 400 (Bad Request)


### Solution

Add **AZURE_CLIENT_ID** to the Application settings with the Client ID of the User Assigned Managed Identity as the value:

![azure-function-uami]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-function-uami.png)