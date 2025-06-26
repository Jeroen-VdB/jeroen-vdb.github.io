---
title: "Azure Storage Account in a Virtual Network: The response for resource had empty or invalid content"
description: "Fix the 'empty or invalid content' error when deploying Azure Storage Accounts with virtual network rules using Bicep. Learn about the defaultAction property requirement."
categories:
  - Azure
  - Issues
tags:
  - Azure VNET
  - Azure Storage Account
  - Bicep
---

Today I was writing a Bicep script to deploy an Azure Storage Account in a virtual network.

In order to configure this in the resource you add the following property:

```
networkAcls: {
   bypass: 'AzureServices'
   virtualNetworkRules: [
      {
         id: storageAccountSubnetId
         action: 'Allow'
      }
   ]
   defaultAction: 'Deny'
}
```

Note that *storageAccountSubnetId* is a parameter in my case.

Next I added a virtual network Bicep script:

```
resource vnet 'Microsoft.Network/virtualNetworks@2020-08-01' = {
   name: '${prefix}-${stage}-vnet'
   location: location
   properties: {
      addressSpace: {
      addressPrefixes: [
            vNetAddressPrefix
         ]
      }
      subnets: [
         {
            name: '${prefix}-${stage}-storage-account-snet'
            properties: {
            addressPrefix: storageAccountSubnetAddressPrefix
            delegations: []
            }
         }
      ]
   }
}
```

Again, I use parameters: *prefix, stage, location, vNetAddressPrefix, storageAccountSubnetAddressPrefix*.

When I tried to deploy both resource from a main Bicep script the terminal in VS Code returned the following message:

> (DeploymentFailed) At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.

Not a lot of details here.

When going to the Azure portal in order to view the deployment logs, I found the following error details:

> The response for resource had empty or invalid content.

The only thing I could think of now was to test everything manually in the portal. When trying to link the virtual network (which was deployed, the Storage Account I added using the portal) in the Storage Account I got a message that the Microsoft.Storage service endpoint has to be enabled. This quickly made me realize I forgot to configure this service in the VNet Bicep script. To fix this I added the following property to the sub net:

```
serviceEndpoints: [
   {
      service: 'Microsoft.Storage'
   }
]
```
And voila, now it’s working.

If you’re interested in the full code, see the following GitHub gist:

{% gist 1cdf6995022b5391969d67f9e906a620 %}

Hopefully this article might help someone with the same situation.

Cheers!