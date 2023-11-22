---
tags:
  - Conference
  - dotnet
  - MAUI
---
[Previous Session](Dynamic%20PGO.md) - [Next Up](What's%20New%20in%20NuGet%20for%20.NET%208.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers: 
Beth Massi - Principal Product Manager .NET MAUI, Microsoft
& Eilon Lipton - Senior Development Lead, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=u30XwO9-10Q)

>[!note]+ Summary
>Hybrid apps are great for web devs that want native plattform capabilities and store distribution. Hybrid apps allow you to reuse UI/Components across web apps and native app with the help of component libraries, and interfaces which relies on platform implementation. You leverage MAUI's capabilities for multi-platform abstractions and it's multitageting, making it easy to target multiple platforms from one codebase.

# What are hybrid apps?
They're a blend of web and native applications. You're building the UI with web technology like HTML,CSS and JavaScript. But you then wrap the UI in a native app container, which gives you access to nativ platform features and the device hardware. Hybrid targets mobile or desktop. It allows you to reuse code across device platforms and web browsers. 

Example: Electron for desktop apps and Ionic for mobile.

This allows you a choice between reach and depth. The hybrid apps are not going to have the richest native experiences but they're going to reach the most people.

In .NET you got Blazor for building websites and PWAs (Progressive Web Apps - these wrapped web apps that run on native). And for Native applications you got .NET MAUI. 

You can blend them together in hybrid applications which will use Blazor for the UI and MAUI for the platform abstraction layer. 
![|450](dotnetconf-23-blazorhybrid.png)

There's no internet needed for these applications even though their built on web technologies. These applications can be distributed through the app-stores. It's not running a browser, it's all wrapped up.
# Reusing UI
We can reuse our components we make by having our shared UI & interfaces inside a `Razor Class Library`. Then our web application and our native-hybrid application can make references to this library and make use of the components. The interfaces are to any services that the UI might need, and the implementations are then in the platform specific projects.
# Building
There's a template for hybrid applications that's been a part of .NET since MAUI got released 18months ago. 
## Structure
We have the folders we expect from a Blazor project, the Components folder, the `wwwroot` folder with static files. But we also get the Platforms folder which is a folder with subfolders for the different platforms the application like Android and macOS. This is where platform specific implementations get put, permissions etc. 

Then there's the application's glue, the XAML files. These are wrappers, like the `MainPage.xaml` is the native parts UI, which just takes the Blazor root and hosts it within.

`MauiProgram.cs` is extremely similar to other platforms implementations. 
## Shared
As mentioned we use a razor class library to hold our shared components. When you want to use these components in your Blazor code you tell the Router which has registered the assembly of the application, that there are also code in another assembly. And we do this by adding an `AdditionalAssemblies` property which takes an array of assemblies. We do this change in both the Web and the Native application, except the Web doesn't have a `MauiProgram` of course, it has a regular one.
```cshtml
@using SharedRazorLibrary
<Router AppAssembly="@typeof(MauiProgram).Assembly"
	AdditionalAssemblies="new[]{ typeof(SharedComponent).Assembly }">
	// Rest of application
</Router>	
```

In the ASP.NET Application (Blazor Web) we also have to do the `AddAdditionalAssemblies` step in `Program.cs` as an extension to the `MapRazorComponents`. It's something they want to fix but they're not there yet.

Sharing also works with the new Blazor web app template that supports auto rendering.

If we inject the `IFormFactor` interface to our shared component. Then we need to actually register that implementation in our Native and Web projects, or else it won't work.
### Assets
Shared assets in the `wwwroot` folder of a Razor class library follow a specific pattern for them to appear.  It's underscore content, followed by the name of the razor class library project.
`<img src="/_content/RazorClassLibraryName/imgname.png />"`
### Platform specific
In shared components we can also define platform specific code, or code that will only render if we're on a specific platform. If we have some UI elements we only want to display if we're on a mobile device we can put in `#if ANDROID || IOS`. In order for that to work we need to add the Android and iOS target frameworks to the razor class library `csproj` 
# .NET MAUI HybridWebView Experiment
They're running an experiment where you can build hybrid applications but instead of Blazor you can leverage JavaScript frameworks like Angular and React You would then embed these JavaScript frameworks into a MAUI project. It uses a `HybridWebView` that invokes C# code.