---
title: "Formatting Logging in .NET 5 for AKS Log Analytics"
categories:
  - Azure
  - TIL
tags:
  - AKS
  - Azure Mointor
  - .NET
---

Today I was trying to investigate an issue in a Docker image running on Azure Kubernetes Serice (AKS). Because it was working on my machine, as usual, I had to look in the Log Analytics of our running pod which contains a fairly simple console app. 
I noticed that the output of the logs was very unclear. Every new line was a new log entry and there wasn't even a log level, so I was not able to filter on errors only.

This made me fiddle around with the [console log formatted](https://learn.microsoft.com/en-us/dotnet/core/extensions/console-log-formatter#set-formatter-with-configuration).

The gist (shown below) formats the following result:

![container-log]({{ site.url }}{{ site.baseurl }}/assets/images/posts/law-container-log.png)

If you still want the default logging format local, you can of course override it with e.g. the appsettings.Development.json file.

{% gist 12024e2fdf2dc568f0d7ff3837b3fa05 %}