---
tags:
  - Frontend
  - blazor
  - NET8
  - CSharp
---
With .NET 8 there's a unified version. That allows for SSR and for CSR. 
Blazor Server and SSR is not really the same, sort of but not really. Blazor Server lives forever, with websockets, while with SSR that connection is severed once the HTML is served. 
# New Template
There's a new Blazor template called Blazor Web App that acts as the unified template. 
## Interactive Server components
This checkbox enables Blazor Server Components. There's no option for wasm
# Auto Mode
Setting Rendering to Auto mode will use the server mode websocket approach for the first load in, since it's a bit faster for the first snappy load. But then for the follow up it will use the WASM. since that's better for the client, because you get a very fast interactive experience. 

Seems like this is what people want you to use right now, which means you have to code for WASM. since it will probably end up as wasm sooner or later. 

	@attribute [ComponentRenderMode(WebComponentRenderMode.Auto)]
There's also a rendermode for wasm and for server.
You can also put this on the individual components so that you can choose each time what it should be. So that it's not on the definition of the component but at the implementation level.

They're going to add some more policies, like you don't use WASM on low powered devices.
## StreamRendering
	@attribute [StreamRendering(enabled: true)]
With regular SSR we run into an issue if we need to go fetch data, the website will just be blank. There will be nothing happening at all while we're loading. By enabling StreamRendering we can instead send off a chunk of HTML like a "Loading..." component, and then do the time-heavy work like a database query etc in the background. Then when the query is done we stream down the results to the client. This gives the user a much, much better UX since they can see that something is happening and they don't have to worry about that. And we can still do our heavy work.
