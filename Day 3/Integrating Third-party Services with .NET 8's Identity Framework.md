---
tags:
  - Conference
  - dotnet
  - Security
  - Authentication
---
[Previous Session](ASP.NET%20Basics%20for%20Experts.md) - [Next Up](GitHub%20Copilot%20Tips%20for%20.NET%20Developers.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speaker: Kiah Imani - Senior Developer Advocate, Auth0 by Okta_ 

[Link to vod](https://youtu.be/X7VmmVLtI7c)

>[!note]
>### Summary
>Adding third party providers to your Identity framework in .NET 8, be it social logins or something like Auth0, is as simple as it gets. You register your application with the external service, get the specific libraries, register the authentication service in your service container. With .NET 8 you get access to Identity Endpoints, and with that you're good to go. Adding new external logons will be automatically picked up by Identity.
# Preferences
According to a poll Kiah did a majority of the people preferred to use social login instead of magic links or one-time passwords. With one-time passwords being a not too distant second. There's actual studies that support this claim too. A study showed that 78% of people aged 18-25 prefer social logins. 

According to another poll by Kiah developers preferred way to implement auth is by using a 3rd party IdP, and this was a large majority, 66.7%, runner up was "Something else" at 22,2%. 
# Talking points
One of the main drawbacks from running with a selfhosted solution, is that you - the developer - have to write a lot of code and make a lot of solutions about have to do things. Rolling with a third party solution instead will take that cognitive load off of your shoulders. And even after the code is written, you need to maintain the code.
# .NET Identity Updates
.NET identity really makes selfhosting easy, _makes me wonder how many of the poll answers came from .NET developers_. 

.NET Identity is built into ASP.NET, but you must opt in to use Entity Framework Core to manage your user-stores. To do this you get the Identity EF Core library, this can be described as the glue that holds Identity together. And then you of course need you EF Core database specific provider.

We then "need" to extend the identity `dbcontext` and the `IdentityUser` (we technically don't _have_ to, but it's good to do because it gives you the option to add your own properties).

When we register our Identity Core we now got the option to also Add our API Endpoints, which will expose the endpoints that were introduced in .NET 8. Then we can map these with our given Identity User.
```csharp
// This all assumes that MyUser extends IdentityUser
builder.Services.AddIdentityCore<MyUser>()
	.AddEntityFrameworkStores<AppDbContext>() // AppDbContext : IdentityDbContext
	.AddApiEndpoints();

// ...
var app = builder.Build();
app.MapIdentityApi<MyUser>();
```
# Blazor Identity UI
When you create your project, if you select "Individual Accounts" you get a lot of help out of the box. Blazor now comes with a bunch of components that make Auth so much easier, the components are prebaked for all the Identity Endpoints. These components are customizable so you can make them look however you want. But if you're lazy you can just leave them. 

Third party services will also show up here automatically with minimal work on our end.
# OAuth Providers
There's a [collection](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) of a massive amount of providers for OAuth in .NET that's assembled by the community. These follow the correct semantics and can give you access to almost whatever external provider you want.
# Twitter Integration
This assumes that your application is registered with Twitter and that you have gotten your keys already.
1. Install the `AspNet.Security.OAuth.Twitter` package.
2. Set up your Twitter Api-Key and Api-Secret in user secrets.
3. Configure it in your `Program.cs`
```csharp
builder.Services.AddAuthentication(IdentityConstants.ApplicationScheme)
	.AddTwitter(options => 
	{
		options.ClientId = builder.Configuration["Twitter:ClientKey"]!;
		options.ClientSecret = builder.Configuration["Twitter:ClientSecret"]!;
	});
```

And now Blazor Identity picks it up.
# Auth0
There are plenty of options third party identity providers, not only Auth0. This assumes that you have registered your application with Auth0 and gotten your domain key and your client Id key.
1. Add the auth packages `Auth0.AspNetCore.Authentication` & `Auth0.AuthenticationApi`
2. Register your keys in User Secrets.
	1. In the demo they use `appsettings.json`, but I don't know why you would want this in your repository.
3. Configure it in `Program.cs`
```csharp
builder.Services.AddAuth0WebAuthentication(options => 
{
	options.Domain = builder.Configuration["Auth0:Domain"]!;
	options.ClientId = builder.Configuration["Auth0:ClientId"]!;
	
});
```
Not this is all done from the C# side. This will send over te user to Auth0 to let them handle the auth flow, so that you don't have to. To further configure the things we go over to Auth0 portal. There we can do things like toggle on other 3rd party social log ins, toggle password log in etc.
## Templates
Auth0 has created some [templates](https://github.com/auth0/auth0-dotnet-templates) for .NET that make it very easy to get going with Auth0. It looks like they're updating them to .NET 8 based on some pull requests I saw in the repo.