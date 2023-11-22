---
tags:
  - Conference
  - dotnet
  - javascript
  - Frontend
  - Backend
aliases:
  - .NET is the best backend for your JavaScript frontend
---
[Previous Session](Make%20a%20template%20for%20your%20template;%20profit!.md) - [Next Up](ASP.NET%20Basics%20for%20Experts.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers:
Jiayan Chen - Product Manager, Microsoft
& Daniel Roth - Program Manager ASP.NET, Microsoft_ 
[Link to vod](https://youtu.be/H1LlRUqj9U4) - [Samples](https://aka.ms/dotnet/js/samples)

>[!note]+ Summary
>With the new JavaScript Project system and SDK it's easier than ever to have a JavaScript frontend to your .NET backend. They live next to each other directly in VisualStudio, and you have support for React, Angular, Vue both with JavaScript and Typescript. VisualStudio and MSBuild takes care of everything from dependencies, running, publishing, running tests etc. All out of the box.
# Using .NET with JavaScript
.NET is a great backend for any frontend. But it can come with some problems that you need to address. How do you integrate your JavaScript build toolchain with your .NET build? How do manage your .NET dependencies next along side your JavaScript dependencies? How do you set up your environment so that you can both run, debug, test your .NET code and your JavaScript code? When you're done with your project how do you publish your frontend alongside your backend?
# JavaScript project system and SDK
There's now a JavaScript project and SDK that comes with VisualStudio. This integrates JavaScript build-systems like Webpack, and Rollup into MSBuild. You can basically solve all of the issues mentioned above, with dependencies and running and testing etc. It helps you publish your frontend and backend as a single application. And it comes with prebuild project templates to make it easier to use. These include Angular, React, Vue templates. 

You can choose to create stand alone projects, like a standalone JavaScript React project in VisualStudio, or you can create a JavaScript and ASP.NET Core application together. Angular only comes with Typescript and can only be used with that. Under the hood it's using Vite to create applications. For React apps you don't have to install Vite manually prior, just have npm installed. But for Angular you need to have the [NG CLI](https://angular.io/cli).

Hot reload is supported and you also can debug your application directly with VisualStudio.
## npm Dependencies
Just like you have Dependencies in a .NET project where you can manage all the .NET dependencies. In these JavaScript projects you have something called "npm" which is where you manage your JavaScript dependencies. If you want you can use it to install packages with a UI, where you can search for packages and pick the one you want to install, avoiding having to use the CLI.
## Publishing
By simply publishing the ASP.NET Server it will also take the frontend wrap it up into a `wwwroot`. And delivers it as a single output. 
## Testing
If you got tests in your frontend application you can run them directly via the VisualStudio Test Explorer. For some reason it takes quite a long time though, took him roughly 36seconds to run 2 unit tests. But hopefully this will get improved, it sounds like they are thoroughly supporting this.
## Adding to existing ASP.NET App
You can of course add a frontend to your ASP.NET application with this system too. 
The build process to get the reference into ASP.NET is like this
1. MSBuild builds your `ESProj`, the JavaScript based project. 
2. This then calls into the build script in the `package.json` file
3. Which runs TSC to output a dist. folder output which is being automatically picked up by ASP.NET
4. You can now reference the frontend application in your backend.

Getting it into your MVC project is also very simple, and you can experiment with using a library like Zod that's a JavaScript library to validate a MVC application.

It's also possible to get JavaScript into your Blazor applications. And there's also JavaScript interop libraries that allow you to call into and execute JavaScript from Blazor. With the new JavaScript project system this is easier to do. You don't have to have your JavaScript file lying about, you can instead have your JavaScript project and when that's built and outputs it's dist. folder you can directly reference that from your Razor class library. Any JavaScript bundler you want to use will work. 