---
tags:
  - Conference
  - dotnet
  - AI
aliases:
  - .NET 💖 AI
---
[Previous Session](Entity%20Framework%20Core%208%20Improved%20JSON,%20queryable%20collections,%20and%20more....md) - [Next Up](Build%20Intelligent%20Apps%20with%20.NET%20and%20Azure.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers: 
John Maeda - VP of Design and Artificial Intelligence, Microsoft
& Scott Hanselman - VP Developer Community, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=A_PfDjcWIeA)

>[!note]+ Summary
>AI Models are nothing without the context that we provide it with. At least not if we want something that's actually helpful for our scenarios. 
>Semantic Kernel is the SDK that allows us to interface with these LLMs through .NET code. They're build by a Plugin first policy, where you pull in plugins to have agents do what you want them to do. 

This is basically the opening act for the next session
It seems like people are confused about the fact that AI is more than just the hype, it's the data science and everything behind.

Scott's part of the beginning here is the exact same as the [AI: Superhero or super villain](https://www.youtube.com/watch?v=1TlI8lduPTE) speech from GitHub Universe. 

The fact that AI models are using context and probabilistic methods to pick what it says next. Not always picking the most likely option. And if you don't have context it's not shifting the corpus at all and it will only pick probabilistic option. The more context the more it shifts what the next picked word is. It also shifts the weights. 

It used to go from "don't trust user input". And now with AI, user input is anything and it's what makes AI do stuff.

Researchers proved a little while ago that if you're kind to a LLM you get better answers back. Maybe because the model associates your kindness together with the helpful answers in it's corpus.
# Models 
There's a bunch of models out there. And the newer ones are way more resource intensive than new ones. And it might not be as much better. It all depends on what you need the model to do. According to John the better ones are going to become cheaper and cheaper.
# Semantic Kernel
Semantic Kernel is the SDK that makes talking to AI APIs possible.
Using a toaster as a metaphor for a semantic kernel. And using cooking as a metaphor to learn the materials we're using for AI. 

We want our orchestrator, toaster, to have both semantic code that's specific to the AI, and old-fashioned code. That could be any code, it could be a website we wrote yesterday. And we want them to play along. It's not helpful if all old code is deprecated. This is the basis of what the LLM does, how it does it's prediction of the next word. But out of the box this is ungrounded, and we want to ground it. To ground it we don't use the GPT model like GPT-4, we use an embedding model like ADA. ADA is a small model but it does what we need for this. It's perfect for embeddings. It's good at taking words and making them to long tokens and vectors. Which is the grounding process. The combination of embeddings and completion models enables a grounded semantic computation. 

By grounded what we actually mean is that it's not going to be less likely to get confused. It's not going to make things up as much. It's going to be biased according to the bias we're putting into the things we put into it. Just like GitHub Copilot is biased to only caring about coding. 

On OpenAI day they announced Assistants, which is an abstraction upon these components that's mentioned above. That makes it easier to write this kind of AI. The problem is that the metaphor is more humanized/anthropomorphised. In order for these AI models to do more than just generate words, like if we want it to go and search for actual information, we need to have Plugins. We also have personas/agents/assistants which means what kind of AI-assistant it is we're getting. In their example they use a Designer agent/persona and there they describe to it what it's specific role is that it's a designer that creates images etc.

We can have these AI personas having specific personalities talking each other, working together. Getting separation of concerns, and sort of AI microservice. 
