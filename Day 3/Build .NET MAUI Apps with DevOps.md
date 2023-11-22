---
tags:
  - Conference
  - dotnet
  - CI-CD
  - MAUI
---
[Previous Session](Clean%20Architecture%20with%20ASP.NET%20Core%208.md) - [Next Up - Bonus Sessions](../Day%20Bonus/AI%20and%20.NET%20with%20Semantic%20Kernel.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers: 
Sweekriti "Sweeky" Satpathy - Principal Software Engineer, Microsoft
& David Ortinau - Principal Product Manager, Microsoft_

[Link to vod](https://youtu.be/PattkMhmnzE) - [.NET CLI Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/tools/)

>[!note]
>### Summary
>Building your MAUI apps is not that dissimilar to building anything with a pipeline. Important to keep in mind is to keep your tasks/actions modular so you can move things around and plug in where you need them. The MAUI specific things comes from having a block that install the MAUI workload and having a block for installing the necessary certifications to sign your App for distribution.
>
>There are tasks that you can get from the Marketplace to help with deployment on Azure DevOps. For GitHub Actions Sweeky recommends to use Fastlane a service to help with deployment.

There are a bunch of options when it comes to Pipelines and the DevOps cycle of things. This talk primarily focuses on GitHub Actions and Azure DevOps. You could also just have a selfhosted build machine, not having to use any external service. You would then setup .NET MAUI Workloads `dotnet workload install maui`.
# A .NET Dev in a DevOps world
Using the .NET CLI (Command Line Interface) puts you closer to getting to the DevOps pipeline. Since you got commands like `dotnet build`, `dotnet test` `dotnet publish`. These commands are the exact same steps you do in a pipeline. With an extra step of using the published artifact in App Store/Play Store.

> If you're using the .NET CLI you're halfway there already.

The good part about MAUI now is that you can do all the things that you need to do via the CLI.
# Environment Setup
What you need to get going is literally only to have .NET installed. With mobile development you might sometimes need things like Xcode or the Android SDK. The developers of both [Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml#software) and [GitHub Actions](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners#supported-software) have provided lists with the default installed software on their hosted runners. For example the macOS runner for Azure DevOps comes with Xcode preinstalled. Knowing what software is installed on the runners helps us with writing better Pipelines. We don't have to install stuff on them if the stuff is already installed. 
# Secret Files
These are needed for distribution, where you're signing your application verifying that you're the real developer. In order to do this you need to have your secrets. These are unique for Apple, Android, and for Windows. These are helpful to have on hand before you start your Pipeline writing. You want to store these into the DevOps system your using's secrets location. Which all of them have. 
# Let's Write some YAML
The flow that a pipeline goes through can be broken down into blocks, Lego blocks if you will. All your .NET pipelines are going to set the .NET Version. This task either utilities the built in version of .NET or you go ahead and set it up. 

The next block is going to be to install .NET MAUI Workloads, they're connected to the .NET version that's installed. 

The next block is to install the Signing Files as you need them. If you're going to distribute your artifact it needs to be signed appropriately. 

After these installation blocks comes the .NET CLI blocks, where you do all the build, test, publish to target framework etc.

In the last block you got your Generated Artifacts, this is a general term Sweeky used to describe all the different things with a generated artifact. Things like just keep it around in the summary of the pipeline, so someone can download it. You can also ship it off to a specific store for distribution.

---
The point is that these steps are going to be the same or very similar in all of your pipelines. Environment setup -> signing installed -> build publish -> artifact.

These pipelines that have been explained so far are very focused on just sharing with the world. But you can have other types of pipelines to, just a test pipeline, a PR pipeline. 
## PR Pipeline
A PR pipeline, that gets triggered on your Pull Requests, where you just want to check if the code that's in the PR even builds and passes tests. Then you're not going to use the block that's all a part of signing or publishing. Those are not interesting. You just need your version, your Maui workloads, and the .NET build and test. This is basically just using the Lego blocks that you need, no more. 

---
By having concrete blocks we can mix and match, and writing new pipelines becomes less of a hassle because we can copy and reuse blocks. With this modular approach, of distinct blocks there's a lot that can be done. Sweeky explains how she's seen some people have pipeline-files inherit pipeline-files which inherits pipeline-files chaining complex solutions. This would be overwhelming if it wasn't for the Lego Blocks.
## Marketplaces
Both GitHub Actions and Azure DevOps Pipelines comes with marketplaces ([GitHub](https://github.com/marketplace?type=actions) -  [Azure](https://marketplace.visualstudio.com/search?target=AzureDevOps&category=Azure%20Pipelines&sortBy=Installs)) that provide a bunch of free actions/tasks that you as a developer can leverage instead of writing your own. It makes life a whole lot simpler. In GitHub Actions these are used with the "uses" keyword.
# Demo MAUI Library
_Uses GitHub Actions_

The summary page is a good place to start out, you get an overview of the different Jobs, the duration, status etc. And importantly you see all your Artifacts. So you can see the artifacts that got produced by this pipeline.

By clicking into a Job we can see all the blocks that creates it. Here you can also go into each block, and there we can see the logs that got spat out into the Console running the different commands inside the block. This is very helpful and important for failures or debugging. Since you can see the error messages that gets sent.
## YAML
The first thing in the pipeline is the overall controls, which says when a pipeline will be triggered to run, you can specify specific branches for pushes or pull requests. You can also define environmental variables here that's available through out the Job. 

The syntax between different DevOps systems might be a little bit different but the over all flow and the way you work is following the same pattern, for example the keyword for defining when the pipeline is run is called "on" in GitHub Actions and it's called "triggers" in Azure DevOps.

Inside your Job you also specify what VM/agent/runner, you want the pipeline to run on. Then comes the thing that in GitHub Actions is called "Steps" and this is where you have the blocks defined. They're given a name so you can identify them in the list. 
### Sanity Tips
Tip from Sweeky is when you install your .NET version in the environment is to also add in `dotnet --list-sdks` after the install script. This way it will list the SDKs in the logs so if something goes wrong you can diagnose that the correct version is in fact installed. This could also be moved to it's own block for sanity.

Another sanity check that's mostly applicable to when you're running cutting edge versions of .NET is to do a restore on the solution. It's typically not something you need to do, but it might save you some headache if you're running cutting edge, since then things are more likely to break.
### Azure DevOps 
Sweeky shows the YAML for the exact same pipeline but in Azure DevOps, and all the actual steps what she has each block execute is the exact same. The only difference is the semantics of how you write the YAML. The flow is going to be the same, but Azure DevOps name things differently etc. 
# Demo MAUI App
Having an app or a library go through pipelines is very similar, the flow is mostly the same just some different implementations, for example we won't pack as a NuGet. Nothing changes for a MAUI App or a Blazor Hybrid App
## Tests
If you run tests in your pipeline with `dotnet test` you can get a test report out to your summary 
view. Just giving you information about the tests that were run, if they passed, the time it took, if they were skipped etc. You can also view the code coverage directly here if you output it. This is a step where you use the [Test Reporter](https://github.com/dorny/test-reporter), after your `dotnet test`. This will give you a good report.

In Azure DevOps this is built in. But you can specify that it should collect XPlat code coverage. And then specify in the net task to publish that code coverage data.
## VisualStudio version
If we're building on a Windows Runner we might also want to specify which version of Visual Studio we want to use. Since mac doesn't have VisualStudio, it has it's own version that's not the same and it's going away anyway. 
## Publishing
The publishing is literally just running `dotnet publish` both for all the platforms, the only difference is that you're targeting different frameworks with the `-f` flag. Important to note is that if you're going to target iOS or mac Catalyst this must be done from a mac runner. mac runners can build android applications though.

If you want to sign you application you do that by appending the `-p` flag and the code-sign-key and then `-p` with the Publishing-Profile.
## Release
**Azure DevOps**: When you've built your application and it's going to go to the app-stores for distribution there are tasks in the marketplace for [Apple App Store](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.app-store) and [Google Play Store](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play)that will help you get the app where it needs to be.

**GitHub Actions**: In the talk they say that you can use `xcrun altool` to distribute for iOS, but I think this might be an error (I'm not an app developer but [this](https://developer.apple.com/news/upcoming-requirements/?id=11012023a) would imply it's deprecated). But Sweeky recommends to use [Fastlane](https://docs.fastlane.tools/) anyways so. And she recommends to use this for both iOS and Android.