---
tags:
  - Conference
  - dotnet
  - blazor
---
[Previous Session](Welcome%20to%20.NET%208.md) - [Next Up](Building%20Cloud%20Native%20apps%20with%20.NET%208.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers:
Daniel Roth - Program Manager ASP.NET, Microsoft 
& Steve Sanderson - Developer/Architect, Microsoft (Original creator of Blazor)_
[Link to vod](https://www.youtube.com/watch?v=YwZdtLEtROA)

>[!note]
> ### Summary
>The new Static Server Side Rendering allows for very fast static content to be served. You can then be selective where you sprinkle in interactivity and have server or WebAssembly based interactivity where it's needed. Giving us the best of server side rendering with client side interactivity. 
>## New Template
>To bring this to life we get a new template called Blazor Web App that comes with all the features out of the box.

# Static Server Side Rendering.
Covered most of the changes to Blazor in .NET 8 over in [Blazor in .NET 8](../../../2.%20Frontend/Blazor/Blazor%20in%20.NET%208.md).  

Blazor in .NET 8 combines server & client. Which leverages the best of both worlds. Blazor server communicates with the client over a WebSocket using SignalR, WebAssembly downloads the WASM to the client and you run the program locally. There's a new mode now, a static Server Side Rendering mode. Which sends down a static HTML to the page.

Static SSR is really good at scaling, there's no websockets, and no WASM.
It's really good for just presenting information, thing's that are read only like blogs. 
Interactions are mostly forms and navigation via links.

It doesn't have good rich interactivity or real-time updates which we might get from regular applications.

You can mark components as interactive via server or WASM though and get past this.

With static SSR you're not having Global interactivity but instead per page/per component interactivity. Which is "cheaper". 
## Streaming SSR
With Streaming SSR you don't have wait for API calls or database calls. You instead load static content, and then stream data down to the client. Which provides for a much better user experience.

+ Fast initial UI render/update
+ Begin loading static resources in parallel

Requires UI design to make sense, you don't always want this, or else you will just flash something at the user. Don't use this everywhere.
# Enhanced Navigation
Get SPA-like navigation using SSR.

In standard SSR mode you have to reload everything each time because the browser doesn't remember anything which feels sluggish. With enhanced navigation you're only swapping out the parts that are changed between two views, it keeps the CSS and other HTML. It just updates the DOM.

Thanks to enhanced navigation you can say which components to retain state between navigations. This allows things like a search bar to retain data between navigation. 
# Static SSR Forms
All capabilities of \<form> or EditForm. Works here too. With the default validation and features we expect to be there. Each form has to be given a unique name though, so the server knows which submit-handler you want. You can also bind incoming post data to a model.

Forms can also support EnhancedNavigation, meaning it's not throwing everything away. It's not on by default and you have to explicitly mark a form with Enhance for it to work. This can also be used together with streaming rendering, showing some message when the server process is running then streaming down if it worked or not.
# Interactive components
You can drop in interactive components into statically generated views.
Mark any page/component as server/WASM/both.
Works with enhanced navigation / forms, retain interactive state while navigating or refreshing static SSR content, closes/reopens server circuit automatically.  You can add the render-mode on either the component where you use it or at the component file level. Typically it's better to use it at the point of implementation because you're more limited.
## Circuits
A circuit is a WebSocket connection. And the new mode is smart enough to open and closes these as required, so you get interactivity but you don't waste resources.
## Auto Mode
Allows us to use WebAssembly without the first-download cost. It uses the server while caching WebAssembly files. And then when it is downloaded the client is able to use WebAssembly instead.
This gives you the best of both worlds. But it requires the application to support WebAssembly.
# New Template
There's a new template that empowers all this functionality. Blazor Web App.
# New Features
## Quick Grid
A new easier way to display tabular data
## Router
Has gotten a bunch of new updates and improvements.
## Improved Authentication
Together with the .NET auth improvements. There's a bunch of baked in authentication components in the projects that we can change for our needs. 
# Razor Components using Entity Framework
There's an option for VisualStudio to scaffold pages that do all the basic CRUD features, or choose which of the CRUD letters you want it to scaffold. 

Which you can update yourself. The scaffolder is in preview. It is also using the new Quick Grid feature. 
# With an existing Project
## Blazor
Just change the version and use the new feature. It all works out of the box. 
## ASP.NET  Core app
Add Blazor Pages
![[../_Files/dotnetconf-23/dotnetconf-23-blazor.png]]
