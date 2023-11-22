---
tags:
  - Conference
  - dotnet
  - template
  - NuGet
---
[Previous Session](Improve%20your%20ASP.NET%20core%20web%20app%20performance%20using%20Azure%20Cache%20for%20Redis.md) - [Next Up](dotnet%20is%20the%20best%20backend%20for%20your%20JavaScript%20frontend.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Rob Conery - Cloud Developer Advocate, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=XzD-95qfWJM) - [Rob's gist](https://gist.github.com/robconery/6182bcdc532a54b2f47b2e969a05cfcc) - [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/tutorials/cli-templates-create-project-template) 

>[!note]+ Summary
>Creating a template is surprisingly simple. You can create any application type you want your project to be and add in a `.template.config` folder to get started. Rob has snippets listed that will help creating the necessary Json files for configuration. When you have your created package it's also very simple to create a NuGet package and share it with the world.

Creating a template allows you to wrap up some complicated things and share it with people. 
# CLI - Cross-platform
You can do this cross-platform on macOS and Windows, using the CLI. The things you write is the same, also works with both VS Code and VisualStudio. 

1. Starts of by creating a new .NET webapp. 
2. Then you need to create a template config folder in the project folder. The naming is very important, `.template.config`  the dot before template is essential.
	1. In the new directory you want to create a file called `template.json`. The schema needed for this file is found either on [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/tools/custom-templates#templatejson) or [Rob's gist](https://gist.github.com/robconery/6182bcdc532a54b2f47b2e969a05cfcc) that help you create a snippet for it.

## Template properties
`defaultName` - this is the name the project will get if the creator doesn't provide a -n.
`identity` - a unique name for the template
`shortName` - the name that's used to create the project template from CLI i.e. `dotnet new ...`
`sourceName` -  this text is placed into the project and will then be replaced with whatever the user typed after -n
`classifications` - a bit confusingly these are listed under 'Tags' when you run 
`dotnet new --list`. 

---
_Continuing..._
3. You want to keep your template as light weight as possible since these are all things that are added when the user creates your project template. 
4. To install the template you run `dotnet new install ./` (given that you're in the project folder). 
	1. This will install it into your template cache.
# Sharing with the world
The best way is to create a NuGet and uploading it. 
1. You need to get a `csproj` file, best way to do this is by just creating a Console application, and take that `csproj`.
2. Inside the Console Application Project we create a folder called "templates". 
	1. To this new folder we copy everything in our template.
3. If VS Code asks you if you want to add things for building and debugging just decline, you don't want to add things that's not necessarily needed. We want to keep the template lean. 
4. If you get a `bin` directory and an `obj` directory these can both be deleted safely. Once again to lean things down. 
	1. The `Program.cs` also has to be deleted. 
	2. This leaves us with the templates folder and a `csproj` file.
5. We open up the `csproj` and modify it with what we need.
	1. We delete everything except the wrapping `Project` tag. 
	2. In the snippets in [Rob's gist](https://gist.github.com/robconery/6182bcdc532a54b2f47b2e969a05cfcc) there's a snippet with the NuGet package xml.

## Template Properties
`PackageId` - a unique identifier that identifies our project. Rob recommend that you think : `Company.Use.WhatItIs` as a helper. Where company can be replaced with your name if you do this solo.
`PackageProjectUrl` - typically a link to a GitHub Repo
`PackageType` - needs to be "Template"
`IncludeContentInPack` - needs to be true
`IncludeBuildOutput` - needs to be false
`NoDefaultExcludes` - true, we want to be specific with what we exclude. And in this case it's just the `bin` and `obj` folders in the template.

---
_Continuing..._ 
6. We can now run `dotnet pack` to pack up the package.
7. We can now upload this to NuGet.org
	1. But you should add a README.md to the templates folder and in there explain what the package actually does. This is considered a best practice and should be followed.
	2. Along with that it's also good form to add a Code of Conduct file (CoC.md)
		1. It might be overkill for small projects but if you're doing open source this is really helpful.
	3. And along with both of those also add a License file to the project.

If you added a license to your project you can also add the `PackageLicenseExpression` tag to your `csproj`. You can also add a `PackageReadMeFile` tag and point to your README file. 

You can also add in icons to your package. 

If you need a Code of Conduct you can always just add the .NET foundation code of conduct to get something that's reasonable.