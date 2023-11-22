---
tags:
  - Conference
  - dotnet
  - VisualStudio
---
[Previous Session](Tiny,%20fast%20ASP.NET%20Core%20APIs%20with%20native%20AOT.md) - [Next Up](ASP.NET%20Core%20Authentication%20Simplified.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Sayed Hashimi - Senior Program Manager, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=Ki4Wv-neAsw)

>[!tldr]+ Summary
>The biggest thing is the Http Files which will help you develop more efficiently, not having to leave your flow and move over to something like Postman. Especially with the combined functionality of Endpoint explorer and Generate Requests from endpoint which will automatically generate a request. 
>
>The Dev Tunnels functionality seem neat if you need to share your build with your colleagues or a client for a demonstration.
>
>You can now get automatic CRUD endpoint scaffolding. Which creates all CRUD endpoints for a given model.

[Resources](https://aka.ms/sayedha/netconf2023)
# Dev Tunnels
You're hosting you're application on localhost as you're working. And when it's time to share it with some colleagues you can open up Dev Tunnels in visual studio, in the drop down where you launch your debugger. 

There are two kinds of tunnels. Temporary tunnels which remains open while that session of VisualStudio is open. Persistent tunnels which remains open across a long period of time, we're talking months, as long as it get intermittent usage the URL will stay active.

Tunnels work with and without debugging. 
# Http Files
Http Files are a new feature and comes baked into the WebApi template. The idea is that you can author Http requests directly in VisualStudio, allowing you to call your endpoints, and also see the responses. Without even leaving the IDE. 

You can define variables in Http Files and use them across the file.
```http
// Creating a variable assigned to localhost, note no quotes around the URL
@MyResturantApi_HostAddress = http://localhost5032

// Using the variable in the call
GET {{MyResturantApi_HostAddress}}/weatherforecast/
Accept: application/json
###
```
## Secrets support
There's secrets support inside the Http files, you can create ASP User Secrets and set up secrets. Or you can use services like Azure KeyVault for this too. In his demo he just links to a Config file which in turn pulls from Azure KeyVault. 
# Scaffolding
In a WebApi with models for an application that's going to use CRUD functionality you can now have VisualStudio scaffold those endpoints for you. 
This is located under

	Right Click Project > Add > New Scaffolded Item...

You pick the API options, and you can pick either Controller or Endpoints, and you tell it if you have EF Core or not in use. You get prompted to tell it what you want scaffolded, you provide it with your model, that is the model you want CRUD on. What the `DbContext` is, it can also be created at this point. And of course you have to tell it where you want the scaffolded code, it can be either a new class or an existing one. And you pick your database provider, it comes with four options out of the box (SQL Server, SQLite, PostgreSQL, Azure Cosmos DB).

And this will scaffold the CRUD functionality for us. And this is fully modifiable, you can go in and change what you want after it has generated code for you.

If you choose to have the scaffolder create the `DbContext` for you it will automatically register in `Program.cs`, it will also register the endpoint in `Program.cs`. In `appsettings.json `it also adds a connection-string for the database. 
## Database
The standard EF workflow is usually to drop into the CLI to execute dotnet EF commands. But these have now been baked into VisualStudio. By double clicking Connected Services, you can see your Service Dependencies. And if you followed the scaffolding your db should be here. And you can click the options to find Add Migration/Update database/Generate SQL Script. So you can add your migrations easily through this. 
# Endpoints explorer
This window will show you all the API endpoints that's discovered in your solution. You can through this either navigate directly to the endpoint, or you can generate a request. Generating a request will generate that Http request in the Http file. 
