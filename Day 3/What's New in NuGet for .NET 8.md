---
tags:
  - Conference
  - dotnet
  - NuGet
---
[Previous Session](Build%20hybrid%20apps%20with%20.NET%20MAUI.md) - [Next Up](Vertical%20Slice%20Architecture%20How%20Does%20it%20Compare%20to%20Clean%20Architecture.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speaker: Allie Barry - Product Manager NuGet, Microsoft_

[Link to vod](https://youtu.be/u38Qb0Eay28) - [Release Notes](https://devblogs.microsoft.com/nuget/announcing-nuget-6-8-maintaining-security-with-ease/)

>[!note]
>### Summary
>The new NuGet updates will make it easier for the end user to be sure that the packages that they download or have in their project does not have any known vulnerabilities. You will get alerts everywhere if you do have these. 
>
>Package Source Mapping is a way to make it even more safe to download packages because you ensure that the source is one that you do want to have it from. And in .NET 8 they will enable this by default when you download packages. 
>
>Overall mostly QOL changes that won't be heavily noticed except for the fact that you are safer if you follow their alerts.

Nuget 6.8 gets released together with .NET 8.
# New Features
- NuGet Audit
- New `VulnerabilityInfo` API
- Conditional Package Updating in VisualStudio
- HTTPS Everywhere & `allowInsecureConnections`
- Search by Target Framework on NuGet.org
- Central Package Management in VisualStudio
- ToS updates

# NuGet Audit
A new security feature, it's a process that identifies vulnerabilities, evaluates risk and makes recommendations for improving security. It gets enabled by default in .NET 8. You can run a security audit explicitly or implicitly. 
An audit is performed explicitly whenever you run `dotnet restore` in the CLI. It's run automatically whenever you run a standard package interaction like installing a package, updating a package etc. so you're kept safe even without having to do anything extra.

This will warn you whenever a package has a known security vulnerability. It will tell you the severity of the vulnerability, which package is affected. And it will provide a GitHub link providing you with more information including steps to fix the problem.
## Warning Codes 
Warning codes from the audit are "cryptic", or not directly user friendly. but they also write it out in clear text. 

**Code to severity matching:**

| Warning Code | Severity |
|--------------|----------|
| NU1901 | low |
| NU1902 | moderate |
| NU1903 | high | 
| NU1904 | critical |
## Vulnerabilities in transitive packages
Transitive packages are packages that the main one depend on. If you use NuGet package A, and package A requires package B. Then B is a transitive package.

VisualStudio's  Package Manager UI will warn you if any of these transitive packages have known vulnerabilities. Even when audit only configured for direct packages. These are shown with a little hazard yellow triangle.
## Info bar
In the solution explorer there's a a little information bar if there's packages with vulnerabilities in the solution. It will give you an option right there in the bar to go to Manage NuGet packages.
# VulnerabilityInfo API
Helpful if you're a package source provider. NuGet client can now easily download vulnerability information to use in scenarios such as checking packages during restore operation. This allows providers to supply their own information to NuGet, and it makes NuGet not only having to rely on the information it has from NuGet.org.
# Conditional Updating
If you have projects that target multiple frameworks like MAUI it's likely you have conditionally installed packages. Conditional packages are basically a package that's only work for a certain framework. Historically NuGet have struggled with consistency for this feature when it comes to updating. But this has now been fixed and will actually do what it's meant to. Only update for certain frameworks, but not for others.
# Package Source Mapping
You can configure where your packages are coming from. And you can map these packages to specific sources. 
This can be configured through the dialog under 

	Tools > Options > NuGet Package Manager > Package Source Mappings
Or just in the `nuget.config` file.

This can help protect you what's called a "_Dependency Confusion Attack_". They're a sort of attack where the attacker will take advantage of the fact that people trust NuGet a lot. So they will create a package with the same name as a package on NuGet but on another source. They would then prey on people who get confused because they see the correct name and download the package from the wrong source. This alternate package could have malicious code.

You get a little notification off this in when you go to install a package through the UI. And you get linked to where you configure it to be active, if that's what you want to do.
## New in .NET 8
Coming in NuGet 6.8 they will do this automatically. 

The package source mapping also considers what packages are in the global cache when it goes to get packages for transitive dependencies. It will look in the cache for a package and if it matches the source that's been mapped for the solution it will go get that. If it sees a package in the global packages folder but it's source is different from the mapped ones it will fail with an error and provide you with information of how to fix the problem.
# HTTPS Everywhere
Https Everywhere is an initiative in software development and it's meant to get everyone to use Https instead of insecure versions. Without Https and SSL we wouldn't be able to transmit sensitive information. In 6.3 they added a `NU1803` warning where you get a warning if one of your sources are not using Https. You can use the `allowInsecureConnections` attribute if you want to opt out of these warnings.
# Search by Target Framework on NuGet.org
You can now filter by target frameworks on the website. This goes further than just '.NET', you can also filter by versions of .NET like .NET 5 etc. This was a heavily requested feature
# Central Package Management
Starting with NuGet 6.2 you could centrally manage dependencies across projects, this was enabled via a `Directory.Packages.props` file, and an MSBuild Property. With 6.5 we got the capability to install and update packages directly to this central management. In the past if you had added a new package to this props file it might not have actually translated to what packages were available in the solution.