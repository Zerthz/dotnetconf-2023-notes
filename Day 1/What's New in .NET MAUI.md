---
tags:
  - Conference
  - dotnet
  - MAUI
---
[Previous Session](Build%20Intelligent%20Apps%20with%20.NET%20and%20Azure.md) - [Next Up - Day 2](../Day%202/Improving%20your%20application%20telemetry%20using%20.NET%208%20and%20Open%20Telemetry.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers:
David Orinau - Principal Product Manager, Microsoft
Maddy Montaquila - Senior Product Manager .NET MAUI, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=4saU9BNY6l4)

>[!note]+ Summary
>.NET 8 introduces a lot of stability fixes and bug squashes. Trying to make it a better experience to develop MAUI. 
>There's now a MAUI extension for VS Code that will allow you to develop MAUI there, it's still in preview but it's still good enough to use. There's also been a bunch of desktop features introducing native functionality that you would expect a framework like MAUI to have. Like drag and drop.

MAUI is now 18months old. And the plan is to build cross-platform from a singular codebase. 1 Codebase - 4 platforms. 

1600+ PRs and 600+ issues closed in .NET 8.

MAUI seems to actually take off, and more and more big companies are using it now. Like 3M, UPS and NBC Sports NEXT. Just meaning that there is trust in it. There's 3 Microsoft teams that use MAUI, the Microsoft 365 Admin app, the Microsoft Azure app, and the Store Commerce app. The mobile phone applications that is of course. 

# In .NET 8
## Improved performance and memory management.
This is something you get by default hopefully. The teams are always slowly improving on it, and sometimes it's a big focus and sometimes it's on the backburner but they hope to not regress. More talk about Maui performance was in the [Performance Improvements in .NET 8, ASP.NET Core, and .NET MAUI](Performance%20Improvements%20in%20.NET%208,%20ASP.NET%20Core,%20and%20.NET%20MAUI.md) talk. 

New hybrid and desktop app features
Flexible tooling for development
## Tons of bug fixes and stability work.
This is the biggest fix in .NET 8, just squashing bugs and improvements to the platform. 
# Offers Choice
These fixes doesn't only apply to MAUI changes but also to Blazor hybrid applications that use the MAUI framework. 
# Desktop Development
MAUI started as a mobile-first platform, but since there's been a lot of work on the desktop side of things. Remembering that the tools is only 18months old, so by .NET 7 it was what, 6 months old? Which makes these .NET 7 features mentioned here a bit more reasonable I suppose. .NET 7 introduced tooltips, pointer hover, right-click and context menus. And for .NET 8 they introduce [custom keyboard accelerators](https://devblogs.microsoft.com/dotnet/announcing-dotnet-maui-in-dotnet-8-preview-7/)(keyboard shortcuts), drag and drop gestures, pointer gestures for pressed & released.

None of these are that hard to build, pretty intuitive building process. 

They're exposing more of the barebones things, allowing developers to cook up some things on their own. Like developers can use the new PointerPressed and PointerReleased gestures to build their own "long press" feature, until that feature maybe gets added some day.
# .NET Multi Targeting 
Not something new per se, but worth hammering home that MAUI runs in what's called a "Single Project". So macOS, Windows, Android, iOS is all in the same project, and is using .NET multi targeting to target the different environments. It's possible to build your own plugins/NuGet packages for Maui, [this](https://www.youtube.com/watch?v=ZCQrlGT7MhI) is the person who's tutorial they're mentioning in the speech here. 

For keyboard accelerators (which again seems to be the official term for shortcuts), you can define different shortcuts for different platforms. On macOS there's a cmd key that gets used a lot, and on windows you instead have the windows key, but the windows key is not used like that. So instead you can have the ctrl key, and leave cmd for mac. And you can check this by platform with an if statement or use the Xamarin markup extension "OnPlatform", all do the same by having different keys on different platforms. 

Using WeakReferenceManager for pub-sub things.
# C# DevKit
There's now a MAUI toolkit extension for VS code that's still in preview. But it lets you develop MAUI applications in VS Code, which practically means you're not locked to windows VisualStudio. Especially since they're removing VisualStudio for Mac. 
# VisualStudio 2022
Not going anywhere it's not to be replaced by vs code. They're introducing something called Live property explorer. When you click something in the XAML Live Preview during runtime you will get highlight that in the xaml and in the live visual tree like always, but it will also bring up this window that's sort of like DevTools in the browser, you can see exactly what's going on with that object. This feature is in pretty early preview. It just got pushed into preview 2 so it's not out in the standard releases yet. You can also test things, like update it during the runtime but it won't persist the changes. Exactly like mucking about in browser devtools. 
# On to .NET 9
A year away now. They have a bunch of goals.
- Faster delivery of bug fixes.
- Improving build times
- Improve runtime performance
- More native desktop experiences
- Improve hot reload
- Swift Interop
- More documentation and samples.

.NET 8 is LTS and it will be aggressively serviced compared to .NET 7, bug fixes will be pushed out more rapidly.