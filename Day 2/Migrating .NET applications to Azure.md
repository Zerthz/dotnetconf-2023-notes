---
tags:
  - Conference
  - dotnet
  - Azure
  - Cloud
---
[Previous Session](ASP.NET%20Core%20Authentication%20Simplified.md) - [Next Session - Day 3](../Day%203/dotnet%20Configuration%20In%20Depth.md)- [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers:
Mike Rousos - Principal Software Engineer, Microsoft
& Olia Gavrysh - Senior Product Manager, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=awbXa4FoNqo)

>[!note]
>### Summary
>The Azure Migrate Application and Code Assessment tool will scan your source code and/or binaries and give you a detailed report of all the things that will need to be updated to ensure a smooth migration to Azure. The tool will link to your specific code problems and give you advice on what to do. The tool exists both as a VisualStudio extension and as a CLI tool.

They make it sound like this talk is about moving from on-premise not from other Cloud to Azure. But a lot should be applicable.
# How did it use to look like
1. Business decision
The business decides that there should be a move to a cloud premise.
2. Overall evaluation
Of the platform and what it would take
3. Engineering work
The actual moving of applications to the Azure. This is also where a lot of the annoying parts were because a lot of code had to be changed to be Azure specific and to run in the cloud.
# Azure Migrate (Before)
Azure migrate is a tool that helps you move to Azure. It used Agent-based VM and application discovery. So it could find all of the ASP.NET and Tomcat applications by their config files and it conducts a migration that requires minimal code change. It got is great for tools that require no code updates. 
# Azure Migrate (Now)
They received feedback that when developers applications to the cloud they need more assistance. Which is why the new version has application and code assessment. This runs offline, and will do a detail scan of the application and the source code (if it's written in .NET). It discovers and assesses .NET apps and provides code changes. It produces reports and allows tracking of incident progress.

This helps you move applications that might not work out of the box. It does this by providing a separate companion tool.
# Azure Migrate Application and Code Assessment tool
## What does it look at?
- Databases
- Authentication
- Windows Identity - not supported in cloud
- Secret Management 
- Network Resources
- Message Queues
- File System
- Session State
- Application Request Routing.
- Caching
- SMTP

These are all things that can not work, when moving to cloud and Azure Migrate got tools in order to catch these instances.
## Tool 
[Tool](https://aka.ms/appcat/dotnet)
This tool produces the combability report for migration. If you got the VisualStudio [extension](https://aka.ms/appcat/dotnet/vs) tool you can jump to the exact parts of code that's the problem. There's also a CLI [tool](https://aka.ms/appcat/dotnet/cli) . There's also a similar tool for Java.
## Analyzer
The analyzer is easy to run, just right-click the project and click on the Assessment tool. The default is to analyze the source code but it also got functionality to analyze binaries for any loose DLLs or what have you. 

The tool will tell you how many projects it scanned in it's report. And also provide information of how many unique issues it ran into, and how many incidents that amounts to, since an issue can appear more than once. It also gives you an estimate of how many story points this would mean. Basically just giving you a grasp of how much work it would entail to fix the things that needs fixing. 

There's also different severities, these goes from mandatory to information. 
Mandatory means that the analyzer believes that these issues needs addressing for the application to run well in Azure. 
There are also optional issues, that might be improved but they're not blocking. Like moving to cloud native alternatives for what you're doing.
Potential issues represents the biggest block of issues. And these are issues that requires a developer to assess if it would actually be an issue. Like connecting to a database. It wouldn't be a problem if the database exists with it. It's mostly going to be about external dependencies.

The tool will also provide assistance with things for we should do, adding tips and links to resources.
# Upcoming
The team has collaborated with GitHub Copilot so that Copilot will be able to pick up the generated report and walk you through the updates that needs to be done.