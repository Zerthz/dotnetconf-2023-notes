---
tags:
  - Conference
  - dotnet
  - Storage
  - Performance
  - Cache
  - Azure
---
[Previous Session](Blazor-testing%20from%20A%20to%20Z.md) - [Next Up](Make%20a%20template%20for%20your%20template;%20profit!.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers:
Catherine Wang - Senior Product Manager, Microsoft
& Marc Gravell - Principal Software Engineer, Microsoft_

[Link to vod](https://www.youtube.com/watch?v=hVdgRw68GHM)

>[!note]
>### Summary
>Implementing Redis Cache into your cloud application is a way to get improved performance very easily. ASP.NET provides libraries that Redis hooks into, so the developer doesn't have to think about using Redis directly at all. Having your cache centralized instead of in-process allows you to have many nodes but still ensure that they all have the same cache values, it also allows you to persist data if the process-session is short like in serverless solutions.

# Redis Cache
Redis is an in-memory key value store. It's Open source and there are enterprise versions of it. Azure Cache for Redis is a fully managed cloud solution, it's in partnership with the Redis team. This resource is performant, scalable, secure and it works seamlessly with other Azure integrations. 

The issue that this cloud service solves is when you have multiple nodes of your webapplication and you have an in-process cache you can have different nodes with different caches. This can lead to the same key having different values in different nodes. So you get clashing data. The processes can also be short, in the world of serverless, this can lead to the cache not actually getting populated and utilized. Another issue is that the cache's memory can scale beyond what you want for the rest of the webapplication. By moving the data to something like Azure cache for Redis you solve all of these issues.
# Cache use-cases
## Output cache 
Where you want to reuse the entire HTTP Response between compatible requests. Basically means anonymous requests that are just simple GET data. But if you can reuse the entire HTTP response that means you're saving all of that work it took to create that page and all the database calls etc. All of that work gets reused for multiple requests. 
## Distributed Cache
If we have repeated data-queries in our webapplication we can cache that, so we don't have to go down to the resource (e.g. database) each and every time we want the data. 
## Session store
This is a specific implementation of a distributed cache. It's where we store information about the current session, like user information; shopping basket. That's reflects the current connection.
## More
It's also more than just a cache-system it's a full NoSQL database. It got a lot of different datatypes and we can leverage this to create things like a leaderboard where we store some data in Redis and leverage Redis APIs directly to get the data we need for our components. 

---
Most of these cases you don't even have to know anything about Redis at all, it's all baked into ASP.NET directly. The one exception being the leaderboard which uses the `StackExchange.Redis` library, or other similar Client libraries. 
# Output caching
Output caching have existed since .NET 7 but it didn't have Redis implementation, it was all in-memory. But now with .NET 8 it supports Redis Cache directly. It's well optimized for performance and memory management. There's no breaking changes, but you can opt into this now. 

Adding Output caching just takes registering it into the service container. with `AddOutputCache` and also adding in `StackExchange.Redis` to the service container.

In our routes we can then either use the tag `.CacheOutput()` or use the annotation method `[OutputCache(Duration = 15)]` to set caching. You can also declare tags to group your caching. And you can then also use the cache API to purge going by tags. Everything is declarative and simple to use.
# Distributed Cache
At the moment the implementation is very raw. You have to do a lot of work yourself, you have to create the key yourself. And also do all the work with the data you get back as well. Which you get back as a chunk of bytes. All the serialization, verification, handling if not verified and so on needs to be done manually.

In .NET 9 they're aiming to fix this to make it easy to use.
## User Session Store
User Session store builds upon the `IDistributedCache` interface. And once distributed cache is configured it's very easy to add this on top of it. **Note** It's easy to configure distributed cache right now, not easy to use it. You just add `AddSession` to the service container and it will automatically discover the distributed cache provider.

Then you can just get the session from the `HttpContext` you don't need to know what lies behind it. 
# StackExchange.Redis
This is a library that Microsoft is using, it's used to wrap Output caching and Distributed caching with Redis. And if you want to do more advanced things with your caching and cache data then you need to talk directly to the Redis server. And you can do this through this library.