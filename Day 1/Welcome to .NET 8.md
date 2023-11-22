---
tags:
  - Conference
  - dotnet
aliases:
  - Keynote .NET Conf 2023
---
[Next Up](Full%20stack%20web%20UI%20with%20Blazor%20in%20.NET%208.md) - [.NET Conf 2023 Parent Page](../README.md)

---
[Link to vod](https://www.youtube.com/watch?v=mna5fg7QGz8)

>[!note]
>### Summary
>.NET Aspire is a new stack, with a new Template and project type that will help you build Cloud Native applications. Coming with preconfigured services ready to get going.
>Upgrades across the board. To performance and more. 
>
>.NET 8 out now

The .NET team's goal is to develop a framework for people to develop what they want for wherever they want. 
.NET 8 released as of today, 2023-11-14.

.NET 8 is the biggest release so far, and it got improvements across the board, from MAUI to the runtime and all in between.

.NET 8 is the most performant .NET version so far, with over 2150 performance updates, they got like 21k updates overall I believe so it's not insignificant.

The eShop application that .NET has used for a long time for examples have now been updated.

In one word .NET 8 is **NIFTY**. 
# Fullstack
With the new combined Blazor template you don't have to pick one, you get the best of both worlds. Blazor in .NET 8 supports Server Side Rendering. By having blazor.script in the App.razor allowing for a snappier experience and not having to reload the entire page, you're just updating the parts that has changed.

With Streaming Rendering Blazor will render some static content in the client while it is loading data or does a process in the background. Then the data is streamed down to data and automatically updates. No websockets needed, it uses the same response as the static content.
# Primary constructors
You can now define services that will be injected and available for the rest of the class directly in the declaration instead of having to create a constructor.
# Endpoints explorer
A tool that now comes with VisualStudio that allows you to explore all the endpoints of the application. You can rightclick an endpoint, and press generate request to generate a request, another new VisualStudio feature. Which allows you to test your endpoint directly in VisualStudio, similar to Postman.
# Debugger
The debugger display has been improved for a whole bunch of types, actually giving helpful information. Like for example it tells you which endpoints are registered.
# MAUI
.NET gets better performance, increased memory usage and startup time. Over 600bugs have been squashed. 
## Developer Productivity
New MAUI extension for vs code, currently in preview. Together with C# devkit it allows you to develop .NET MAUI wherever you are. All instructions needed are in the README for the .NET MAUI extension.
# Cloud Native
In .NET 8 Open Telemetry will provide a lot more integrations, and together with Grafana it will be easier to get an overview of the application
## Containers - Native AOT
Improves security by not making you run as root, also decreasing size. With fewer dependencies you have less risk but also it makes the application faster.  Native AOT gives you a much smaller file size and lets you run way more copies of an application on a cluster. Native AOT is also way faster to come up and running. Not everything can run as Native AOT, it has to be the correct dependencies. If you're app is not compatible and you're trying to publish to native AOT you will get a bunch of warnings, it's still in early days and more and more packages will come along.
## .NET Aspire
Is a new stack to build Cloud Native applications. Open source on GitHub and out today in preview.
### Components
To address that people have feel overwhelmed with choice Aspire comes with preconfigured selected services. These services comes configured for you to suit the cloud. You don't have to directly add connection strings, just using references in your Program.CS
Developer dashboard, is a dashboard that really helps to diagnose your distributed applications. Helps running your distributed application with a single application, also helps deploying your application. Either directly or getting your code mapped into IaC code.
### Launching Apphost
By launching your Apphost it will run all the containers, and the entire distributed application. It launches the dashboard where you can see where all errors are coming, and all the endpoints. Even go in and see console logs on the applications.

You can even follow the distributed trace for a call, and seeing exactly what it is hitting. Aspire has templates, either a starter application or regular(?).

The preview of Azure Developer CLI understands Aspire out of the box, allowing you to deploy easily.
