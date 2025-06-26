---
title: "Unit Tests in a .NET 6 project with Azure Pipelines DotNetCoreCLI@2"
description: "Fix the 'Microsoft.AspNetCore.App version 5.0.0 was not found' error when running .NET 6 unit tests in Azure Pipelines by adding proper restore and test steps."
categories:
  - Azure
  - Issues
tags:
  - .NET
  - Unit Testing
---

Today I migrated our project from .NET 5 to .NET 6.

An issue I stumbled upon was the Azure YAML Pipeline throwing an error in the DotNetCoreCLI@2 task with the 'test' command:

```
It was not possible to find any compatible framework version
The framework 'Microsoft.AspNetCore.App', version '5.0.0' (x64) was not found.
   - The following frameworks were found:
        6.0.0 at [C:\hostedtoolcache\windows\dotnet\shared\Microsoft.AspNetCore.App]
```

As described in [this GitHub issue](https://github.com/dotnet/core/issues/6907) you have to add the UseDotNet@2 task and select version '6.0.x' which was already the case. Also note that vmImage 'windows-latest' still targets 2019, so if you use a Windows vmImage, specify 'windows-2022'. But I use 'ubuntu latest'. So this didn't solve my issue.

[This StackOverflow answer](https://stackoverflow.com/questions/70043477/netsdk1045-the-current-net-sdk-does-not-support-newer-version-as-a-target/70050863#70050863) did solve my issue. I included a 'restore' step and in the 'test' step added an argument not to restore.

The solution that worked for me:
{% gist 891ea231cf56a9811fcc6aff11f3dd94 %}

Note the following parts:

![azure-pipeline-dotnetcorecli2]({{ site.url }}{{ site.baseurl }}/assets/images/posts/azure-pipeline-dotnetcorecli2.png)