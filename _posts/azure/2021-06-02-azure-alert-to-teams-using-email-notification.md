---
title: "Azure Alert to Teams using Email Notification"
description: "Compare two methods for sending Azure alerts to Microsoft Teams: using Logic Apps vs direct email notifications. Learn the pros and cons of each approach."
categories:
  - Azure
  - TIL
tags:
  - Azure Monitor
  - Teams
---

Yesterday I implemented an Azure alert with an Action Group that triggers a Logic App to send a message to Microsoft Teams. I followed this article: [Customize alert notifications by using Logic Apps](https://docs.microsoft.com/en-us/azure/azure-monitor/alerts/action-groups-logic-app)

Today a colleague told me if the organization has “Allow users to send email to a channel email address” enabled you can use an Email notification to send an alert to that channel. For more info about the option see: ["Get email address" option is not available in Teams Channel](https://docs.microsoft.com/en-us/answers/questions/40495/get-email-address-option-is-not-available-in-teams.html)

---

# Logic App

### Pros

- Custom notification format

### Cons

- More work
- More complex
- Possible failure in the Logic App

# Email

### Pros

- Easy and simple to configure
- No custom implementation needed

### Cons

- No custom format
- Requires a Teams organization-wide config enabled
- Content is too long so you have to download the email for details

---

If you are interested in the Bicep code for both options, see the following gists:

{% gist 01a50ba061411f599fd12317f1ee3db1 %}