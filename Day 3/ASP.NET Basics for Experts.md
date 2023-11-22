---
tags:
  - Conference
  - dotnet
  - ASP-NET
  - Fundementals
---
[Previous Session](dotnet%20is%20the%20best%20backend%20for%20your%20JavaScript%20frontend.md) - [Next Up](Integrating%20Third-party%20Services%20with%20.NET%208's%20Identity%20Framework.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Layla Porter - Developer Advocate/Content Creator, VMware_
[Link to vod](https://youtu.be/GDCMiBu_2gI) - [Repo](https://github.com/Layla-P/BasicsForExperts-CodeEdition)

>[!note]+ Summary
>.NET has gotten a lot of quality of life features and a lot of features that might be jarring for new developers or returning developers. This talk covers things from how the new `Program.cs` file looks and operates to how the new Dependency Injection system works, going via the new Minimal API system. It will give you a start of point to get going.

# Why? 
Maybe you come from an older versions of .NET or a different language language all together. But you do know a lot of what's going on in software development. This talk is about how to do things in .NET 8.
# Program.cs
If you come from an old version of .NET(pre .NET 6) you might be used to your Start Up file where you configure your services and middleware. But this got completely changed in .NET 6. These days, in .NET 8, you have a `Program.cs`. This file is missing a lot of the things that you might remember. It doesn't use [top level statements](https://learn.microsoft.com/en-us/dotnet/csharp/tutorials/top-level-statements), so the code is right there in the root of the file. The Program and the Main method are hidden behind the scenes, and it actually compiles down to that still. If you come from other languages like Node working with Express then this might be a lot more familiar. 

At the core of `Program.cs` we got the a builder that might vary depending on your project type. But for all of them we're registering our services in the builder. We also modify our configuration, where we have to take note which order we do things because the later things we add on take priority in case of key-clashes. 
# User Secrets
User secrets is a thing we can use to store secrets that we don't want to check into our repository. They will be accessible just like the properties in `appsettings.json` but they're stored outside your repository so they are safe from the version control. 

ASP.NET will come with configuration set up, but if we don't use a webapplication but instead use a console application then we must set it up ourselves. This means we must tell the configuration on the builder to use  `appsettings.json` and User Secrets. 

```csharp
builder.Configuration
	.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
	.AddUserSecrets<Program>()
	.Build();
```

When we're done setting up our builder's services we go ahead and build the webapplication.
`var app = builder.Build();`. And we can from the app go ahead and register our Middleware, keeping in mind they will be executed in the order they're registered.
# Minimal API
Since .NET 6 we got what's called minimal API. These are APIs that are designed to have just what's needed to work, so they're less boilerplate and better performance that regular controllers. You can register them directly in `Program.cs`. This way you have your endpoints directly in `Program.cs`. 

However if your application is even somewhat complicated this would make the Program file very messy, so the common pattern is to break it out and instead use extension methods to register your endpoints.
# Global Usings
In order to clear up our files we also have global using statements. Some of the most commonly used using statements have been grouped together and are by default used in every single file, you just don't see them but they're there.

What you can do is create a new file and in there delete everything and write your own global using statement. If you use a specific namespace a lot you can just add it to be available in all the files instead of having to import it to each and everyone. This is done by adding the keyword `global` before `using`.
# Dependency Injection
If you come from an old version you might be tempted to use something like Autofaq for your dependency injection needs, this is however no longer needed in .NET. It's all built in and it's very simple to use. 

The way we do it in .NET now is as said super simply. We add the service to the application-builder. And we give it a lifecycle, of transient; scoped; or singleton. When the service is registered we can then go to a constructor, or endpoint parameter, and type in the service name. And it will automatically be discovered and implemented. The common way is to register an interface, and it's implementation as the service, and then inject the interface. The service container will see what the implementation is and give you that type. 

```csharp
// When you ask for IMyClass you get the implementation MyClass
builder.Services.AddTransient<IMyClass, MyClass>();

app.MapGet("/", (IMyClass myClass) => {
	return MyClass.HelloWorld();
});
```

If you have a lot of services this can be messy, and you could instead break it out to it's own Extension Method. Then over in `Program.cs` we can just call the extension method on the builder.
```csharp
public static IServiceCollection AddDependencies(this IServiceCollection services)
{
	services.AddTransient<IMyClass, MyClass>();
	return services; 
}
```

If we go ahead and add multiple implementations of the same interface to our service container it will overwrite and just use the last one. This is why in .NET 8 we can use the `AddKeyed...` which will allow us to add multiple of the same interface, but with different implementations, which are uniquely identified by a key.
```csharp
builder.Services.AddKeyedTransient<IMyClass, MyClass>("myclass");
builder.Services.AddKeyedTransient<IMyClass, MyOtherClass>("myotherclass");
```

To then get this out we can use the attribute 
```csharp
// rest of code removed..
([FromKeyedServices("myclass")] IMyClass myClass)
``` 
## HttpClient
HttpClients are special, and you want to register those on their own, with the `builder.Services.AddHttpClient();` This will allow you to give control of the lifecycle of the HttpClients to the runtime. Which will ensure that it's used in a reasonable manner. 

You can also register these as strongly-typed or named. Giving you the power to configure your HttpClient with things like base-address that's specific for the special use-case. It also allows you to register resiliency policies, this is something you can't do on the standard HttpClient, only the typed and named ones. 

To add policies we primarily use the library Polly (other options might exist).