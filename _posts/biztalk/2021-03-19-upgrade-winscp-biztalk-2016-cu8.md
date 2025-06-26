---
title: "Upgrade WinSCP when installing BizTalk 2016 CU8"
description: "Fix SFTP receive location failures after BizTalk 2016 CU8 installation by updating WinSCP to the latest version and configuring assembly binding redirects."
categories:
  - BizTalk
  - Issues
tags:
  - Dinosaurs
---

Today I installed Feature Pack 3 CU8 on my customer’s BizTalk development server, coming from Feature Pack 3 CU7.

After the installation was successful, I rebooted the server and soon found out the SFTP receive locations were all disabled. When going through the logs, I found the following error:

> The Messaging Engine failed to add a receive location “RL.SFTP” with URL “sftp://sftp.myserver.com:8022/subfolder/" to the adapter “SFTP”. Reason: “System.IO.FileLoadException: Could not load file or assembly ‘WinSCPnet, Version=1.7.2.10803, Culture=neutral, PublicKeyToken=2271ec4a3c56d0bf’ or one of its dependencies. General Exception (Exception from HRESULT: 0x80131500)

This clearly states that the current WinSCP version placed in “C:\Program Files (x86)\Microsoft BizTalk Server 2016” is not correct.

I downloaded the latest .NET assembly / COM library at [https://winscp.net/eng/downloads.php](https://winscp.net/eng/downloads.php) and replaced the **WinSCP.exe** and **WinSCPnet.dll** files in the BizTalk installation folder.

Because I downloaded WinSCP v5.17.10, which has an assembly file version of 1.7.2.11087, I had to update the WinSCPnet binding redirect in **BTSNTSvc.exe.config** and **BTSNTSvc64.exe.config**:

```xml
<runtime>
  <assemblyBinding xmlns=”urn:schemas-microsoft-com:asm.v1" >
    <probing privatePath=”BizTalk Assemblies;Developer Tools;Tracking;Tracking\interop” />
      <dependentAssembly>
        <assemblyIdentity name=”WinSCPnet” publicKeyToken=”2271ec4a3c56d0bf” culture=”neutral” />
        <bindingRedirect oldVersion=”1.7.2.10803" newVersion=”1.7.2.11087" />
     </dependentAssembly>
   </assemblyBinding>
 </runtime> 
```
After restarting the hosts, I was able to start the receive locations.

Very strange that I didn’t find any documentation about this, maybe I overlooked it somehow. Nevertheless, I hope this post might be helpful for someone in the future.
