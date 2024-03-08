---
title: "Beware of nesting too many Bicep/ARM modules"
categories:
  - Azure
  - TIL
tags:
  - Bicep
---

As I was deploying an application to Azure using Bicep, I noticed the deployment took much longer compared to a similar project. The amount and type of resources deployed are roughly the same.

The main difference was the amount of modules that were used. This led me to set up and execute a quick test.

The first Bicep deployment, called single-module contained the creation of a resource group together with a storage account. The storage account was in a module to scope it to the resource group.

In the multi-module Bicep files, the top layer deployed the resource group and referenced 6 layers of modules until the storage account itself was deployed.

**The result?**

<figure>
    <a href="/assets/images/posts/bicep-nesting-result.png"><img src="/assets/images/posts/bicep-nesting-result.png"></a>
</figure>


Having multiple layers of template-nesting, increased the deployment duration for the resource group with storage account from less than 30 seconds to more than 2 minutes.
Conclusion: even though using modules increases readability and maintainability, balance it out and be aware of the performance impact this could have during deployment, which could impact development productivity once the project gets larger.

For the Bicep code I used in this test, see: [https://github.com/Jeroen-VdB/bicep-module-time-comparison](https://github.com/Jeroen-VdB/bicep-module-time-comparison)