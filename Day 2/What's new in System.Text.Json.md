---
tags:
  - Conference
  - dotnet
---
[Previous Session](In%20.NET%208,%20ASP.NET%20Ate.md) - [Next Up](Tiny,%20fast%20ASP.NET%20Core%20APIs%20with%20native%20AOT.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speaker: Eirik George Tsarpalis - Principal Software Engineer .NET Libraries, Microsoft_ 
[Link to vod](https://www.youtube.com/watch?v=7Et6ooq4oyI) - [Blog](https://devblogs.microsoft.com/dotnet/system-text-json-in-dotnet-8/)

>[!note]
>### Summary
>Unless you have specific needs there's no reason to use anything but `System.Text.Json`.
>In .NET 8 the Source Generator got improvements that made it more performant and better to handle multiple new scenarios. The serializer also got improvement to handle a bunch new features. More types are supported aswell.

`System.Text.Json` is a high performance JSON library, and it should probably be the one you use if you're building new projects in 2023. `System.Text.Json` focuses on security and compliance which is why it's a bit more restrictive when it comes to serializing patterns. 

# System.Text.Json 7
Added contract customization, allowing you programmatically control almost all aspects of a type contract. Also allowed for user defined serialization attributes and private field serialization. It also added initial support for the required member, however this was restricted to the reflection based serializer.

---
# System.Text.Json 8
## Source Generator Improvements
These follow 3 general themes of improvements. 
1. Native AOT Improvements.
These include functional improvements and size reduction. Since AOT want's to be as small as possible.
2. ASP.NET Core Interop Improvements
This makes it easier to register multiple source generators, and once again size reduction.
3. Bug fixes and reliability improvements
Mostly closing the functional gap with reflection.
### Functional Improvements
Added member support for _required_ and _init_ members. 

Improved source-gen support for polymorphism and unspeakable types, which means it's possible to serialize types as objects. I.e. it will work in middleware. Or even serializing enumerables from iterator methods or IAsyncEnumerable iterator methods. 

They've also unblocked string enum serialization for Native AOT applications.
### Fast-Path Serialization
Since it's release in .NET 6 the source generator has implemented a fast-path serialization mode. Which compliments the default contract based mode. In practice this has the source generator spit out code that serializes the values in a fully inline manner. It takes a writer and just inline writes exactly what needs to be written for each value. This has immense performance benefits over contract based serialization which traverses objects using delegates and indirection. The main drawback of these methods are that they haven't been designed with streaming in mind. Which is why they're not used in async methods. 

In .NET 8 they've incorporated a runtime heuristic which will conditionally enable fast-path serialization as long as the json payload doesn't go over a predetermined size. This gives huge performance improvements. 
### Reflection feature switch
You can now turn off Json Serialization going to reflection. By removing it you're replacing reflection with a fail fast run time exception. This basically ensures that you get the same behavior everytime. Since reflection in the past had a tendency to be flaky.
### JsonSourceGenerationOptions
You can now target almost all of the settings of the options object when creating a context.
## Serializer Features
Historically the library has ignored properties without setters for the purposes of deserialization. With .NET 8 you can actually deserialize this objects. You do this by adding an attribute to the class called `[JsonObjectCreatonHandling(JsonObjectCreationHandling.Populate)]`, which will populate the the properties as needed. 

You don't need to use this attribute at all actually, if you're using it in multiple places that would get annoying and you can instead register it as a global policy. And you can just add it onto the JsonSerializerContext. 
## Unmapped members
Previously if you tried to deserialize a json object into an object that had required members, and the json object didn't contain these members, you would run into errors. This because the contract required the json object to contain the  required property. Also historically Json deserializer would completely skip properties on the Json payload that didn't appear on the target object.

Now you can now change this, and make it instead fail and throw exceptions if it runs into unmapped properties in the payload. 

	[JsonUnmappedMemberHandling(JsonUnmappedMemberHandling.Disallow)]
## New naming policies 
There are now new naming policies, snake case and kebab case both with upper and lower case variants. Complimenting the existing camelCase. This diverges from Json.Net but that's because Json.Net had limitation that made a diversion necessary. 
## Out-of-the-Box support
`Memory<T>` and `ReadOnlyMemory<T>` are now supported using the same semantics as arrays.
Also adding more numeric types, specifically _Half, Int128, UInt128_
## JsonSerializerOptions Analyzers
If you new up your `JsonSerializerOptions` every time you run into performance issues because it needs to do heavy computation everytime not utilizing caches. Now there's an analyzer spotting if you do the wrong thing and tells you to fix it.
## Misc Features
- Relaxed JsonInclude & JsonConstructor, they can now be applied to non public members
- JsonNode DeepEquals & DeepClone methods.
- `JsonSerializerOptions.MakeReadOnly( )`
- `IAsyncEnumerable` extensions in `System.Net.Http.Json` 