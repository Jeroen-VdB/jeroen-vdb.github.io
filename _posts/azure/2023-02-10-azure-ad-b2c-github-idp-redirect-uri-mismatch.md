---
title: "Azure AD B2C with GitHub as Identity Provider returning 'Error: redirect_uri_mismatch'"
description: "Solve the redirect_uri_mismatch error when using GitHub as an identity provider in Azure AD B2C by ensuring the Authorization callback URL is in all lowercase."
categories:
  - Azure
  - Issue
tags:
  - AD B2C
---

# TL;DR
The **Authorization callback URL** in a GitHub OAuth App is case-sensitive.

<figure class="half">
    <a href="/assets/images/posts/azure-ad-b2c-github-idp-with-cases.png"><img src="/assets/images/posts/azure-ad-b2c-github-idp-with-cases.png"></a>
    <a href="/assets/images/posts/azure-ad-b2c-github-idp-without-cases.png"><img src="/assets/images/posts/azure-ad-b2c-github-idp-without-cases.png"></a>
    <figcaption>Issue → Solution</figcaption>
</figure>

---

Today I tested Azure AD B2C with GitHub as an Identity Provider. Microsoft has some excellent steps on how to do this. I followed these articles:

1. [Tutorial — Create an Azure Active Directory B2C tenant - Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant)
2. [Tutorial: Register a web application in Azure Active Directory B2C — Azure AD B2C - Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)
3. [Tutorial — Create user flows and custom policies — Azure Active Directory B2C - Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-user-flows?pivots=b2c-user-flow)
4. [Set up sign-up and sign-in with a GitHub account — Azure AD B2C - Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory-b2c/identity-provider-github?pivots=b2c-user-flow)

Everything was very straightforward, until the moment I executed the “Run user flow” to test this example setup.

I was able to login into my GitHub account and when I got redirected to jwt.ms had the following error:
```
AADB2C90273: An invalid response was received : 'Error: redirect_uri_mismatch,Error Description: The redirect_uri MUST match the registered callback URL for this application.,Error Uri: https://docs.github.com/apps/managing-oauth-apps/troubleshooting-authorization-request-errors/#redirect-uri-mismatch'
Correlation ID: 123456-4321-6789-9876-abcd12345678
Timestamp: 2023-02-10 14:16:22Z
```

The description is very clear and yet I had no misconfiguration after following the tutorials.

When I was almost at the point of giving up, I went back to my GitHub OAuth application settings. Here we have the config field **Authorization callback URL**. Which is where the GitHub error response is referring to. This field is filled in with a value I copy/pasted from the Azure Portal, so I had no typos.

It was, however, a combination of upper and lower cases…

So as last resort, I updated this value to all lower cases, and behold, problem solved!

```
https://MyTenantnName.b2clogin.com/MyTenantName.onmicrosoft.com/oauth2/authresp
-->
https://mytenantname.b2clogin.com/mytenantname.onmicrosoft.com/oauth2/authresp
```

Hopefully, this can spare you some time.
