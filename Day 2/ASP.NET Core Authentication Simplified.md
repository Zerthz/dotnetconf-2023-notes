---
tags:
  - Conference
  - dotnet
  - Authentication
  - Security
  - ASP-NET
---
[Previous Session](Visual%20Studio%20Updates%20for%20.NET%20Devs.md) - [Next Up](Migrating%20.NET%20applications%20to%20Azure.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers:
Stephen Halter - Principal Software Development Engineer, Microsoft
& Jeremy Likness - Principal Product Manager, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=c__Sf9j_Q2Y)

>[!note]+ Summary
>The ASP.NET Core Identity framework now exposes a bunch of APIs related to authentication. These are meant to be consumed by applications. All rely on having Entity Framework Core. This works with the new Blazor Web App template in .NET 8 in Auto render mode, but you have to set up a `PersistentAuthenticationState` on both the client and the server in order to persist the authentication when you enter WebAssembly rendering.

Auth was one of the most upvoted feature requests on the team's GitHub page. Especially the auth flow for SPA. They've found that 1 in 3 applications with auth use ASP.NET Core Identity. They really wanted to simplify the process for developers.
# Identity APIs
These APIs are designed out of the box with DTOs to make it straight forward to build the identity pieces into SPA and Blazor WASM applications. You enable the user to those basics things like registering and logging into applications. The full set is still available on the server. These APIs are exposed in order to be consumed by clients. They also provide validation feedback. 

In order to opt into this we have to add Identity EF Core, and your EF Core implementation (SQL Server etc.)

The set up is similar to regular Identity solutions with `IdentityDbContext`, and an `IdentityUser`. We add the standard things to the service container
```Csharp
builder.Services.AddAuthorization();

// this will be db provider specific
builder.Services.AddDbContext<ApplicationDbContext>(...);

builder.Services.AddIdentityApiEndpoints<IdentityUser()
	.AddEntityFrameworkStores<ApplicationDbContext>();
```

This third one are the lines that actually enable and expose the endpoints. And it also configures a lot behind the scenes for cookies and tokens. We also need to add `app.MapIdentityApi<IdentityUser>();` to actually map them so they're available. 

For the client the process is pretty straight forward, hit the `/register` endpoint with username/password. The endpoint will return validation errors if any such exists. Otherwise it will register you in the application. You can the head to the `/login` endpoint which takes the username and the password. And here the client should enable `useCookies` since that's the most secure and recommended way to be consuming the API. you do this by passing a query parameter `/login?useCookies=true`
## Cookie less
If you client for whatever reason doesn't support cookies, they provide a proprietary token(can't be read on the client). That will generate an access token and a refresh token. 

---
It's up to the client to provide all the actual safeguarding that this entails. Like protecting it's routes and redirecting to sign-in pages etc..
# Blazor
Blazor has a built in set of components to work with authentication, regardless the kind you're using. You extend the `AuthenticationStateProvider` with your own cookie based version. You can then call the `manage/info` endpoint after the user has been logged in. This endpoint is authorized only so if the user isn't logged in properly it will fail. And from this endpoint you can get your claims and authenticate. With the `CascadingAuthenticationState` that wraps the application, the auth status will affect the whole wrapped application. 
## .NET Blazor Web App
This is the new Blazor template in .NET 8 that works as a unified template. So the "problem" we have when working with this new template is that we can have Auto interactivity mode which uses both `Server` and `WebAssembly`. And both of these needs to be able to use the Authentication.

Compared to the normal non-auth template the biggest difference is a folder under Components called Account. This defines all the Identity related razor components. Like Login, Register, ResetPassword etc. Blazor defines all of these for us so they work out of the box. But we can if we want also go in and edit them so they look the way we want to for our application. These are all only available on the server. And can't be rendered in a pure WASM mode. This is because they're accessing the `UserManager`.

You don't have to use the tag for the `CascadingAuthenticationState` you can add it to the services instead and it does the same; `builder.Services.AddCascadingAuthenticationState();`

Setting up Auth is done in the Server project, and it's very similar to regular Identity. New in .NET 8 is the generic `IEmailSender<T>`, which can take in the `ApplicationUser` and you can access any properties on it when you send them confirmation emails. 

In order for the WASM rendering on the client to be able to authenticate 
The `AuthenticationStateProvider` gets added to the service container in the server project. Which works a lot like the previous Cookie state provider in the WASM Blazor project. This uses the `PersistentComponentState` to take the authentication state from the server and send it to the client via a `UserInfoOptions`. This serializes the `UserInfo` and sends it onwards. This isn't taking the entire `UserPrincipal` however just the UserId and Email, and if you want anything else to be added you will have to go and add it yourself. 

So the server needs to make this implementation of the `AuthenticationStateProvider`, but the client needs to also make one for the persistent component state to work. You inject the `PersistentComponentState` into the constructor on the client and you read the serialized `UserInfo` from the server and get the claims from it and we can configure the authentication state with this.
### Third party
This works super simply with third party providers like a Microsoft Account. You just add it to the Authorization on the server, and of course add the NuGet. And it will work.