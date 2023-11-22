---
tags:
  - Conference
  - dotnet
  - Cloud
  - Azure
---
[Previous Session](Building%20resilient%20cloud%20services%20with%20.NET%208.md) - [Next Up](In%20.NET%208,%20ASP.NET%20Ate.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speaker: Principal Project Manager Azure App Platform, Microsoft_

[Link to vod](https://www.youtube.com/watch?v=JwGdMrmdtNo)

>[!note]
>### Summary
>When creating a webapp you can now choose to deploy a database from the same workflow. This will setup all the resources and establish the connection between database and webapp without us having to do the manual work.
>
>The portal has gotten some updates with Environment Variables breaking out from Configuration to it's own tab.

Azure App Service got Day 0 support. And Azure got a free tier that you can just throw your application at. 

As of 2023-11-15 .NET 8 is generally available for Azure App Service, Azure Functions, Static Webapps. Right now the workers need to download the generally available (GA) .NET 8 bits JIT which means there can be some cold start for the Azure Functions. But azure is running another deployment which will burn these GA parts into the workers, and that will remove the cold starts.
# GitHub Actions OpenID Connect Support
If you have a webapp and you have it linked to automatically deploy from GitHub, you can now use a managed identity. Which lets you have more stringent security. Which can be enforced  via policies.
# gRPC on App Service Linux is now GA
Full support for Unary, Server streaming, Client streaming, Bidirectional Streaming. gRPC for Windows comes in 2024.

gRPC is very efficient for certain things. It's especially popular for AI since it's using bidirectional streaming. Letting you contact the AI and then stream the answer back when it has thought for a little while. 
# App and Code assessment features for web apps
There are a bunch of tools to migrate your applications that run on data centers and move it over to the cloud in Azure. These tools can first help you take inventory and discover what apps you actually have. 

The scanners can also point out parts that will have to be updated in order to move the code to the cloud. An example if your code talks to a local database it will have to change to talk to a cloud database. 

There's also a workload migration, which will help you take every step in the process to get this running on azure, without even touching the code to update it.

There is support for both .NET and Java applications. - [CodeScan](https://aka.ms/MigrateApps/codescan)
# Demo
## Database
You now have the option to create a database directly in the creation flow of a webapp. Apparently 95% of webapps end up talking to a database anyways, and it's usually not hard to create a resource, but it might be tricky getting them talking to each other in a secure manner. 

You get a recommended database depending on your chosen language. .NET developers typically pick SQLAzure so they get that recommended, while Python developers typically pick PostgreSQL, so that's their recommended. 

By default the webapp is not deployed into a Virtual network(vnet) unless you explicitly call for it. But now if you pick a database in the creation flow, you want your configuration to be secure by default. So that the webapp is the only publicly accessible endpoint. 

Our database and webapp will be deployed to the same vnet and they will communicate over a private endpoint. Which means that the only thing that can talk to the database is the webapp. If you want to access the db from your devbox later you can add those firewall rules but you don't have to at creation.
## Pricing plans
Not new for dotnetconf but still newish is the new Premium v3 P0V3 plan, which is a small premium one instance. That's still premium but it's also cost conscious. On the higher end is the new memory optimized Premium v3 P5mv3. Which can get you up to 256gb RAM. 
## Template for automation
If you want to learn how your deployment you created in the portal works, you can download a template at the point of creation that shows you the IaC behind the deployment.
## Environment Variables
These have been broken out of the configuration tab and is now it's own tab. It's purely UI-sugar, and doesn't change the functionality. 
## Service Connector
Service connector allows us to establish the connection between the database and the app and also monitor the connection. You don't even have to look at credentials. You just tell it to validate, and it just tells you if the app can reach the database. 
## Static Web Apps
Is primarily for displaying static data but you can also use an API running on Functions to give you some access to dynamic data. Classic Web Apps uses staging slots for deployment, where you deploy into a staging slot and then swap that over to production. SWAs got a sort of similar workflow where when you make a pull request in GitHub that's linked to an SWA it will automatically create a revision version. Revisions are the way that SWA stage new updates. 
### Traffic splitting - preview
Traffic splitting is a new feature for SWA where you can split incoming traffic between your prod build and your other environments, revisions. Returning traffic will be routed to the same version that they last visited. This is set by a cookie, so you can manually reset it. 