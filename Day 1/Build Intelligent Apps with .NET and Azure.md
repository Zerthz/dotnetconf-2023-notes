---
tags:
  - Conference
  - dotnet
  - AI
---
[Previous Session](dotnet%20💖%20AI.md) - [Next Up](What's%20New%20in%20.NET%20MAUI.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers: 
Luis Quintanilla - Program Manager, Microsoft
Maria Naggaga - Principal Program Manager, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=-3SrUqjq9Ic)

>[!note]+ Summary
>Adding AI to your applications is not that hard anymore. In C# it mostly comes down to understanding Semantic Kernel which is an abstraction on the models. [There's](https://aka.ms/ai-dotnet-learn) a link here for much, much more hands on information. Semantic Kernel is just an orchestrator that strings the intelligent model together.

To dive into this on your own there's a resource page gathering a lot of repos [here](https://aka.ms/ai-dotnet-learn). Also comes with links to all the documentation.

This session is going to show how easy it is to leverage AI into our applications. There's going to be a bunch of demos here.
The bot they build is going to be about GitHub Issues, and it's going to be about getting information from GitHub Issues over to Teams. 

The first thing we're going to need to build this type of application is a model, a model like GPT. Which brings the intelligence to the intelligent apps. One of the way to interact with a model is through the nuget package, "Azure.AI.OpenAI". We then go through basic configuration, setting up our Azure OpenAI key, Azure OpenAI endpoint and the model we're going to be using called chatDeployment. 

You can set up your chat-completion-options that tells the AI how to behave, how many tokens we want it to use, which temperature (how creative the model is, high temperature is very, very creative). 

Then we define a system prompt, where you define how the AI agent should behave. It gets a persona and a job. We then add this system prompt as an initialized message with the bot. Then the pattern is pretty simple of waiting for user input, saving it to our messages and sending that up to OpenAI and then display the reply. 

Right now if we try to this we can't get any answers because the model doesn't have the data. To get the GitHub data they're using a NuGet package that can get it, and they do that and also store it to disk so they can load it from there. Nothing groundbreaking. 

To be able to search through our data we need to use embeddings. Embeddings are a way to represent text as numbers, number arrays to be more specific. The benefit of this is that we can do maths on it, and more specifically do distance calculations on it. To see how far apart text is by the distance from the query. 

These models can only handle a certain amount of tokens at a time before it runs into problems. To get around this we split up our texts into what's called _Chunks_. And we're then processing these chunks one at a time. To actually do this embedding we're using what's called an embedding model, which in this case/most cases, is ADA. ADA is going to take our text and turn it into a float array. 

The text is split into tokens with the help of a AIUtilities helper library. And then the issues is looped over, but not all of them because there was too many. The text is chunked up by an arbitrary number of tokens, in this case 3000. But you also introduce an overlap because that number is arbitrary a chunk might lose it's context that was relevant. So to compensate there's an overlap. 

These chunks are then able to be batch-processed by the OpenAI model to get the embeddings.

Now that there are embeddings in play we can do better search. 

By using the System.Numerics.Tensors library we get access to the TensorPrimitives, which gives us helpful tools to do work on these embeddings. In this case they're using the Cosine Similarity, which takes two float arrays and compare them. Since we want to compare embeddings we want to first get the embedding of our query. We're then scoring by Similarity, where we compare our chunks we created before to our query. 

In order to store this now we need something that's designed to work with this, a vector data base. Like for example Qdrant Vector Db and it now got a C# client available. 

Semantic Kernel works as an abstraction over the APIs for the different providers of models. And it does all this chunking and embedding for us, so we don't have to. 