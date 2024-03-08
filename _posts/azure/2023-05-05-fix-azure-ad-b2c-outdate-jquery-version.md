---
title: "Fix Azure AD B2C outdated jQuery version"
categories:
  - Azure
  - Issue
tags:
  - AD B2C
---

# TL;DR
Using [custom policies](https://learn.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-overview), update the ContentDefinition DataUri values to the latest version, based on the [jQuery and Handlebars versions table](https://learn.microsoft.com/en-us/azure/active-directory-b2c/page-layout#jquery-and-handlebars-versions). as shown in [the example of this page](https://learn.microsoft.com/en-us/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-custom-policy#begin-setting-up-a-page-layout-version).

1 example is to update (at the time of writing) `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` to `urn:com:microsoft:aad:b2c:elements:**contract**:globalexception:1.2.3`

---

# The whole story

After a pen test at a customer where we set up Azure AD B2C as a Customer Identity Access Management platform, we received a high-risk vulnerability caused by an outdated jQuery version.

When we went to one of our AD B2C pages and opened the networking tab, there was indeed a jQuery step using version 1.10.2.

After a quick search, I was only able to find the 2 following related questions:

- [Azure B2C using outdated version of JQuery (version 1.10.2) — Stack Overflow](https://stackoverflow.com/questions/52635930/azure-b2c-using-outdated-version-of-jquery-version-1-10-2)
- [Azure AD B2C User flows are serving an outdated version of JQuery when using custom ui templates · Issue #45732 · MicrosoftDocs/azure-docs · GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/45732)

Unfortunately, these did not provide a direct answer. Which is why I’m writing this article.

Because we know it happens when loading a B2C page, we needed to find the place where this was configured in our custom policy, which we based on this sample: [samples/policies/custom-claims-provider/poicy at master · azure-ad-b2c/samples · GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-claims-provider/poicy)

In the TrustFrameworkBase.xml file, we found a section ContentDefinition where you can define the custom HTML templates for all available pages in AD B2C, like the signup page, the error page, etc.

With this info, we dived into the AD B2C documentation. This led us to interesting article sections [Begin setting up a page layout version](https://learn.microsoft.com/en-us/azure/active-directory-b2c/javascript-and-page-layout?pivots=b2c-custom-policy#begin-setting-up-a-page-layout-version) and [Select a page layout](https://learn.microsoft.com/en-us/azure/active-directory-b2c/contentdefinitions#select-a-page-layout). Here we noticed that the example was using version 1.2.0 while the GitHub sample we referenced used version 1.0.0.

After trying out the new version, the jQuery step was not present anymore 🥳

This is also confirmed by the [Page layout versions](https://learn.microsoft.com/en-us/azure/active-directory-b2c/page-layout#jquery-and-handlebars-versions) article where you notice the mention of jQuery version upgrades. Notice that different pages have different versions but starting from at least version 1.2.0 the jQuery version is already upgraded to 1.12.4.

Starting from 1.2.0 which includes the `contract` element (explained in [Migrating to page layout](https://learn.microsoft.com/en-us/azure/active-directory-b2c/contentdefinitions#migrating-to-page-layout)), the network tab step with the jQuery version is also not present anymore when you’re not using JavaScript.