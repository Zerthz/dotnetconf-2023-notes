---
tags:
  - Conference
  - dotnet
  - ASP-NET
---
[Previous Session](App%20Service%20the%20best%20place%20to%20host%20your%20.NET%208%20Web%20Apps.md) - [Next Up](What's%20new%20in%20System.Text.Json.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers:
Safia Abdalla - Software Engineer ASP.NET Core, Microsoft
Stephen Halter - Principal Software Development Engineer, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=eWjtKwRIc54)

>[!note]
>### Summary
>A bunch of upgrades got shipped that doesn't warrant a full session on it's own. The most prevalent ones include AntiForgery support for the framework, not only MVC auth culture. 
>
>Keyed services makes it easy to have multiple implementations of the same type by having unique keys. 
>
>A bunch of new middleware including request timeout which allows you to set a timeout limit for your endpoints.

This session highlights some of the features that's not highlighted anywhere else in the conference.
# SignalR
There used to be something called seamless reconnect which is now known as Stateful Reconnect. The idea is that when a long-running WebSocket dies, it's client and server can still hold on to their most recently sent messages and if they reconnect they can replay messages without problems. Without having to go via a database or have a slow reconnect process.

This gets enabled on the server. In your MapHub connection options. The client must then choose to use stateful reconnects. This is compared to using normal automatic reconnects. With the old version you got errors. And anything that got sent when the people where offline doesn't get sent at all. Important to note that you can use these two together. And stateful is the one that will get attempted first and if that doesn't work it will use normal reconnects. The client has to call them both though.
Form binding in .NET 7 was very limited but that gets fixed for .NET 8. To write this in .NET 8 and bind the parameter of a minimal API to a form we use the new \[FromForm] attribute. 
# Minimal APIs
```Csharp
app.MapPost("/todos", ([FromForm] Todo todo) => todo);
```

This is an indicator to the minimal API parameter binding that we want to use the Form body to populate a Todo object. 
## Keyed Services
Support for keyed services. Services are typically identified by their service type. Keyed service types are uniquely identified by their type and by a service key. Which allows us to distinguish between different implementations of the same type. 

```Csharp
builder.Services.AddKeyedSingleton<IStorageProvider>("local", 
	 new LocalStorageProvider());
builder.Services.AddKeyedSingleton<IStorageProvider>("azure", 
	 new AzureStorageProvider());
```
We're registering the same type, IStorageProvider, but we're providing keys for our unique implementations. This is done with the AddKeyedSingleton. This is brought into the ASP.NET Core framework, which includes Minimal APIs, SignalR, MVC, Blazor. You can now use \[FromKeyedServices] attribute together with the key to get it from the service container.

```Csharp
app.MapGet("/local/{name}, 
   ([FromKeyedServices("local")] IStorageProvider provider, string name) => 
	   provider.Get(name));
   
app.MapGet("/azure/{name}, 
   ([FromKeyedServices("azure")] IStorageProvider provider, string name) => 
	   provider.Get(name));
```
This implements the attribute getting the two different storage providers from the service container. 
## Analyzers
The binding layers in Minimal APIs have been seen magical at times. In .NET 8 new analyzers have been added that help you analyze and diagnose where you're misusing the implicit bindings. 
### ASP0020
If you're telling the route to parse an object from the route-parameter but you haven't implemented TryParse or the IParsable interface on the object, it doesn't know how to do that parsing. There are a few different solutions, like changing the type or implementing the interface. Or you can delete the route-parameter which means we assume that it will come from the JSON body instead and not the route.
### ASP0024
Having multiple \[FromBody] attributes in one routehandler. This is not possible because you can't take one JSON and parse it into two different objects at the same time. So don't have two. Not really sure who tries that. 
### ASP0025
.NET 7 the AddAuthorizationBuilder was added to services which allows us to add authorization and setup policies in fewer lines of code. In .NET 8 if we're using the old AddAuthorization and construct policies in it, like if it was before .NET 7, you get a warning telling you to use the new AddAuthorizationBuilder instead, it will also help you refactor it to that new version. 
## AntiForgery cookies
With .NET 8 there's also support for the antiforgery middleware. Which sends down antiforgery cookies and antiforgery verification token as part of the form data payload. In .NET 7 and prior versions AntiForgery token validation was only a thing for MVC authorization culture. If you wanted to do AntiForgery with minimal API before .NET 8 you had to roll it out yourself. 

it's as simple as registering AddAntiforgery to your services and then UseAntiforgery to your middleware. This now respects MVC, minimal API, Blazor. 
# More Middlewares
## Request Timeout Middleware
This is what's known an endpoint aware middleware. Like we can put \[Authorize] on different endpoints to have authorization on them; with RequestTimeout we can have different timeouts on different endpoints. Just like with auth, middleware we can define policy and have a default policy.

Basically you're telling the endpoint that this is the maximum amount of time it should take. 
The default policy is to not have a timeout and it can take whatever time it wants. The default is also to not write anything into the HttpResponse. 

RequestTimeouts is setup by registering it in services with AddRequestTimeouts, and it's there we add the policies. And then we use it in the middleware with UserRequestTimeouts. 

You can add RequestTimeout directly to a MapGroup to apply it to the entire group. You can also define the specific policy there you want it to use. At your individual endpoints you can also override these policies, with varying results. 
## Short Circuit Middleware
This is used if you have middleware that you want to not run on specific endpoints. This uses the app.MapShortCircuit method. Which takes a first parameter of a HttpStatusCode. and a second argument of a params list of route prefixes. You can also just add it with ShortCircuit metadata directly to your minimal api routes. 

Note that prefixes include the /. 
## UseExceptionHandler
The middleware itself is not new, but the interface IExceptionHandler is new. It's generally not used with web applications. 

UseExceptionHandler takes a string as a parameter which is the route we want to redirect to on an error. 