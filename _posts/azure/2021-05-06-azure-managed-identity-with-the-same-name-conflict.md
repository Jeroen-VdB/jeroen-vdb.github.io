---
title: "Azure Managed Identity with the same name conflict"
categories:
  - Azure
  - Issues
tags:
  - Azure Managed Identity
---

Today I was automating the Azure landscape of a customer. One of the resources was an API Management Service (will be referred to as APIM from here on) which was configured with a managed identity to access Key Vault certificates.

During the development of the Bicep scripts, we moved the managed identities from the APIM’s resource group to the Key Vault’s resource group.

During the CI/CD deployment, I got the following error:

```json
Conflict: 
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'.",
        "details": [
            {
                "code": "DeploymentFailed",
                "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
                "details": [
                    {
                        "code": "BadRequest",
                        "message": "{\r\n  \"error\": {\r\n    \"code\": \"BadRequest\",\r\n    \"message\": \"Request to resource 'https://control-westeurope.identity.azure.net/subscriptions/xxxx/resourcegroups/xxxx/providers/Microsoft.ApiManagement/service/xxxx/credentials/v2/systemassigned?arpid=xxxx&api-version=2015-08-31-PREVIEW' failed with StatusCode: BadRequest for RequestId: . Exception message: BadRequest: Azure resource '/subscriptions/xxxx/resourcegroups/xxxx/providers/Microsoft.ApiManagement/service/xxxx' does not have access to identity 'xxxx'\",\r\n    \"details\": null,\r\n    \"innerError\": null\r\n  }\r\n}"
                    }
                ]
            }
        ]
    }
}
```

At first, reading the line ‘does not have access to identity’ you would assume it has something to do with access.

This was however not the case, when looking at the top of the error you see the ARM API returned a ‘Conflict’.

Because we created a new managed identity in another resource group with the same name as the manually created one **we had a conflict when adding the new one to APIM with the same name**.

**After removing the old one, we could add the new one without a problem.**

Hope this might help someone!