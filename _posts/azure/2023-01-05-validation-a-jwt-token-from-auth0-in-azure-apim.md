---
title: "Validating a JWT token from Auth0 in Azure APIM"
categories:
  - Azure
  - TIL
tags:
  - OAuth2
  - Unit Testing
  - APIM
toc: true
toc_max_header: 1
---

Because I really like the documentation of Auth0 I wanted to set up a PoC to validate a JWT token from them in Azure APIM. I didn't see any specific articles for this combination, so I wanted to share my solution.

# Pre-requisites
In my example, I opted for a Client Credentials Flow because I have a machine-to-machine scenario in my current project.

- Know what the [OAuth 2.0 Authorization Framework (auth0.com)](https://auth0.com/docs/authenticate/protocols/oauth) is
- Understand the [Client Credentials Flow (auth0.com)](https://auth0.com/docs/get-started/authentication-and-authorization-flow/client-credentials-flow)
- You are familiar with [Azure API Management (- Overview and key concepts - Microsoft Learn)](https://learn.microsoft.com/en-us/azure/api-management/api-management-key-concepts)

# Container diagram
This diagram can give an overview of the example I've set up and which OAuth config relates to which system:

![azure-apim-auth0-container-diagram]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auth0-container-diagram.png)

# Creating an APIM gateway

[Install API Management gateway (Microsoft Azure)](https://portal.azure.com/#create/Microsoft.ApiManagement)

For the scope of this example, I created a simple consumption tier APIM gateway as shown in this screenshot:

![azure-apim-auht0-create-consumption]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-create-consumption.png)

I left all the other tabs on the default settings.

When the gateway is created, I copied the Gateway URL from the Overview page to use it in the next step.

![azure-apim-auht0-copy-gateway-url]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-copy-gateway-url.png)

I created an API using the `Manually define an HTTP API` option and in that API added a test operation.

For `All operations` I added a mock-response policy to return a 200:

```xml
<policies>
    <inbound>
        <base />
        <mock-response status-code="200" content-type="application/json" />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

# Setting up Auth0
As I did not have an Auth0 account, I created one and set up a Tenant. From here on, we only need to visit 2 pages:

![azure-apim-auht0-nav]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-nav.png)

## Creating an API in Auth0
When creating an API in Auth0 **I used the Gateway URL as the Identifier (without https)**. This way we can use a [context variable](https://learn.microsoft.com/en-us/azure/api-management/api-management-policy-expressions#ContextVariables) in the APIM policy.

Under permissions, **I added a test scope** with the name `all:api`

![azure-apim-auht0-api]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-api.png)

The API config in Auth0 can now be used in our APIM policy to validate if the provided client token has permission to call that API.

## Creating an Application in Auth0
We now have an API to validate our incoming token, now we need an application so our client can prove it’s authenticated.

Give the application a name, I called my application `Postman` because I will use Postman to test my API, and select `Machine to Machine Applications`.

When the application is created, I went to the APIs tab and toggled the Authorized button for the `Azure APIM` API. I also selecte the `all:api` permission:

![azure-apim-auht0-app]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-app.png)

# Adding the Validate JWT policy
For more info about the policy, see: [Azure API Management access restriction policies — Validate JWT | Microsoft Learn](https://learn.microsoft.com/en-us/azure/api-management/api-management-access-restriction-policies#ValidateJWT)

The information that we need from Auth0 is:

- [OIDC discovery URL](https://auth0.com/docs/get-started/applications/configure-applications-with-oidc-discovery), for Auth0 this is: `https://TENANT-NAME.REGION.auth0.com/.well-known/openid-configuration`
- [audience](https://community.auth0.com/t/what-is-the-audience/71414): in our case we used the hostname of our APIM gateway so we can use a context variable in our policy, otherwise use the identifier of the Auth0 API
- [scope](https://auth0.com/docs/get-started/apis/scopes), in our example: `all:api` , you could then use specific scopes for different API operations

The policy also requires a configuration to specify where the token will be located, the standard location is the `Authorization` header. I also included a configuration to return a 401 and for sports, a Bearer scheme and expiration time are required.

The full policy XML now looks as follows:
```xml
<policies>
    <inbound>
        <base />
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized" require-expiration-time="true" require-scheme="Bearer">
            <openid-config url="https://dev-someid.eu.auth0.com/.well-known/openid-configuration" />
            <audiences>
                <audience>@(context.Request.OriginalUrl.Host)</audience>
            </audiences>
            <issuers>
                <issuer>https://dev-someid.eu.auth0.com/</issuer>
            </issuers>
            <required-claims>
                <claim name="scope" match="any" separator=" ">
                    <value>all:api</value>
                </claim>
            </required-claims>
        </validate-jwt>
        <mock-response status-code="200" content-type="application/json" />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

# Testing our API in Postman
I created a new Postman collection with a request in it to our API test endpoint. By default, Azure APIM enables the requirement of an APIM subscription key. You can turn this off in the portal. I didn't and just added it to the headers, but thanks to the validate-jwt policy, I still receive a 401:

![azure-apim-auht0-postman]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-postman.png)

To add the required information to Postman (which would be the client application in a real scenario), we need the following info from Auth0:

- Access Token URL
- Client ID
- Client Secret
- Scope
- Audience

The Auth0 application details page has a useful quickstart tab where we can find the first 3:

![azure-apim-auht0-quickstart]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-quickstart.png)

The scope is the one we defined in our Auth0 API and gave our Auth0 application permission to, namely `all:api` .  
The audience is the identifier of our Auth0 API.

In my Postman collection, I added these settings under the Authorization tab:

![azure-apim-auht0-postman-authz]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-postman-authz.png)
![azure-apim-auht0-postman-new-token]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-postman-new-token.png)

Now I can click `Get New Access Token` , this will generate a new token. When it succeeded, I clicked `Use Token` to add it as a header for all requests in this collection. You can do this again when your token expired.

We can now make a successful call:
![azure-apim-auht0-postman-call]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-postman-call.png)
![azure-apim-auht0-postman-inherit-auth]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-apim-auht0-postman-inherit-auth.png)

Thank you for reading, I hope I was able to help!
