---
title: "Get Logic App CallbackURL in Bicep"
description: "Learn how to retrieve the CallbackURL of an HTTP triggered Logic App in Bicep scripts using the listCallbackURL function."
categories:
  - Azure
  - TIL
tags:
  - Logic Apps
---

In order to get the CallBackURL of an HTTP triggered Logic App in a Bicep script use: `listCallbackURL('${myLogicApp.id}/triggers/manual', '2017-07-01').value` (The API version depends on the version of your Logic App).

For a full Bicep example see:

{% gist e74043cc46b2e6e8df982e88bc6bc5bc %}