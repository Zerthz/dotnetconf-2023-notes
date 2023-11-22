---
tags:
  - Conference
  - dotnet
  - AI
---
[Previous Session - Last of Day 3](../Day%203/Build%20.NET%20MAUI%20Apps%20with%20DevOps.md) - [.NET Conf 2023 Parent Page](../README.md)

---
_Speakers:
John Maeda - VP of Design and Artificial Intelligence, Microsoft
& Evan Chaki - Product Leader Semantic Kernel, Microsoft_

[Link to vod](https://www.youtube.com/watch?v=Bnszrmve5tg) - [Semantic Kernel - GitHub Repo](https://github.com/microsoft/semantic-kernel) - [Semantic Kernel - Microsoft Learn](https://learn.microsoft.com/en-us/semantic-kernel/overview/)

>[!note]
>### Summary
>Semantic Kernel is the SDK that's used to interact with AI Services, it supports .NET, Python and Java. Semantic Kernel is still super fresh, version 1.0 drops December 15th. 
>
>What Semantic Kernel is doing is it's abstracting the interactions with the specific services. Allowing you to focus on the specifics.

_I'll be the first to admit that a lot of this talk is going over my head. I think they're giving an overview of the [Cozy Kitchen](https://www.youtube.com/playlist?list=PLlrxD0HtieHjHoXHYSiSvpTp_sE5JhNEE) AI show by John Maeda where they explain AI concepts in a fun manner._
# Design Thinking
Evan invented the thinking that a Foundation Model, something like GPT 3.5 Turbo, is like a brick. It's like a brick because it doesn't soak up any of your data. It just helps you build what you need to build. 
# Moving to Code
To use Semantic Kernel you get the Semantic Kernel NuGet package that's developed by Microsoft. 

In Semantic Kernel there's two different modes, there's either the OpenAI version which uses the public version of OpenAI or there's the Azure OpenAI service. Which allows you to use the Azure service instead.
# Plugins
Semantic Kernel operates on a philosophy that's heavily plugin focused. A plugin is a group of functions that can be exposed to AI apps and services. These functions can then be orchestrated by the application to deal with a user request. 

**Example**: There's a plugin called `ConversationSummaryPlugin` that comes by default with Semantic Kernel, it exposes some functions that will allow you to take a text and summarize it. 

**Another Example**: In the talk they're making a Mentor Application of some kind, and for that they're using a Mentor plugin, that gives you advice.

Plugins can either be brought in from a Plugins directory or they can be inline. They can also be either semantic or native. This allows you to ground your Model with your regular C# code or your prompt based way of speaking with AI.

Plugins are the body of the AI application, and with that the semantic functions are the ears and mouth. They make it so that the application can interact with the user. Meanwhile the native functions allow you to execute C# code to manipulate data or do other operations, to continue the metaphor the native functions is the hands of the appliation. 
### Prepping Input
A plugin has some input, and this input needs to be prepped, but no idea how or why because that wasn't mentioned. 

But it's good to log the input parameters that were used to generate a specific prompt e.g. the temperature, tokens. This way if something goes wrong you have something to go off of.
# Models
AI Models are at a point where they tip over to becoming cheaper and cheaper to use. As they're getting more resource efficient. 

Semantic Kernel also supports you brining in other models, open source models found on [Hugging Face](https://huggingface.co/) for example. Or if you have a local model, that too can be loaded in.
# Embeddings
Embeddings are words and text turned into vectors and numbers in order for a computer to better use it. When you want to get embeddings you use an Embedding Model, which is just another Large Language Model, but you use one of the "weaker" ones, the one most commonly used is the ADA. 

This also allow us to do math on the data, most importantly maybe, we can use the [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity) to find how close a query is to our data. Basically the query gets embedded too, vectorized, and compared to our data. 

ADA is not important unless you're vectorizing things. That's what it's there for, it's not doing the "cool" stuff, it's doing the vectorizing of text.
# Grounded
Grounding a model is very important, it's when we give the model a context to exist within. This makes it so that the model doesn't just make things up. 

When we semantically ground the plugin we tell it what it should behave like, and more specifically we can tell it to not move outside of the context. This will have it so that the model takes the query and the embeddings in it's context and generate answers out of that. This is part of the RAG-Pattern. 
# Agents
[Semantic Kernel Blog About Agents](https://devblogs.microsoft.com/semantic-kernel/assistants-a-first-look-into-using-openai-assistants-with-semantic-kernel/)
Agents are also known as personas, or copilots. They're an abstraction that sits on top of LLMs. They're a trend that has been launched by OpenAI GPTs. Agents can collaborate with each other in a "multi-agent". The user can talk with one Agent and have a conversation, but this agent can behind the scenes orchestrate multiple other agents, like it can get information via a research-agent, it can draw a picture via a painter-agent etc. 

Having agents allow us to split up the responsibilities in smaller components, where each component is focused on one thing, which makes the likelihood of better outputs higher. This also allows us to separate our concerns and we can split up the way data is accessed. For example maybe only one of our agents is allowed access to the internet, that way it's easier to lock it down and have control over the process. 

You can customize the persona to do the work that you need it to do.

Agents are defined in YAML, here we define their name, and we give them a basic job description, the better/more detailed job description the better results you will get.

Important to note that Agents/Personas is super bleeding edge, this just launched. This is where the future is heading however.
# Planners
Planners are there to help the Agent to structure how to do a task, in which manner it's going to use it's Plugins it got available. 