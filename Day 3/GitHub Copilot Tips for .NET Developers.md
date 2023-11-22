---
tags:
  - Conference
  - dotnet
  - AI
---
[Previous Session](Integrating%20Third-party%20Services%20with%20.NET%208's%20Identity%20Framework.md) - [Next Up](Clean%20Architecture%20with%20ASP.NET%20Core%208.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Mark Downie - Product Manager, Microsoft_
[Link to vod](https://youtu.be/nsSNVZF8NYo)

>[!note]+ Summary
>By using GitHub Copilot in VisualStudio we can get an AI companion which can solve problems that are annoying, such as Regular Expressions, with natural language. By using the inline chat we don't give it more context than it needs. We can get help with error solving too, where we have a conversation with Copilot where it will provide us with a hypothesis of how to solve the issue.
>
>The most important thing is to recognize that it's our job as a developer to verify what the AI Companion provides. We have to make sure that the code is tested and good to actually use in a variety of situations.

# Mitigation Layers
In order to use AI for our work we should put in mitigation layers that make it so we're using AI in a responsible manner. At the heart of this is the platform itself. The platform itself is about the model and OpenAI. This is about the data in and the data out, all the research that goes into the model. There's also a safety system around this that ensures that the outputs are reasonable and not full of hate and racism etc. 

There's also the application where things like the Copilot in VisualStudio lives. This is about ensuring that the data that it provides is as good and as correct as possible. And this stems from using good meta prompts, and grounding to ensure that the application does what we want it to.
# Explaining Code
The code Mark is working in specifically for this demo is part of an open source blog-engine that he develops and maintains. The specific method he works on is based on code that he didn't write. Since he didn't write this specific code he can bring up the Inline Copilot Chat. (This is alt + slash, but that's surely configurable/an English-layout shortcut). 

This gives you an inline chat window where you can ask Copilot things. Copilot picks up context based on where you are right at that moment. You can also instead of asking it a question, use '/' to get some commands, including the '/explain' command. Which will explain what the entire context it is inside of. 
# Regular Expressions
The code now is to refactor a method that found the first image in a blog post, and used that as a hero image, into allowing an author to declare an image as a hero image explicitly. For this the old method needs to be refactored with a new regular expression.

Inline chat can be used to explain with natural language what you want the regular expression to do. Making it so that you don't have to write it yourself.

It's now important to take a step back and remember that you actually have no real clue if the code that Copilot spits out works. If you're like Mark and very careful with how you write your prompts you might get a better answer, but you're still not sure, it can still be wrong. It's a probabilistic model. It's the developers job to ensure that the AI produces correct things.

The refactored method looks almost identical to the original method. Which means that Copilot grounds itself in the context of the file, and it knows what method it's refactoring.
# CSS
The regular expression is now working. But the styling is wrong. We can use inline chat in a `.cshtml` file to get the help we need to create the styling we want. By using the inline chat in this file we ground Copilot with the context, and it will know that we're writing CSS by our question. This allows us to use natural language to describe how we want the end result to look. 

Even if you don't know the correct property you can use language to describe the outcome you expect. And it might do the trick. 

Once again it's important to look at what Copilot spits out, remembering that it's the developers job to verify the AI's work. In the demo Copilot sat the code in the wrong location, so it was up to Mark to take the code and move it to where it actually should go. Copilot could also not determine what the image's source should be, so Mark again had to go in and fix that up. If we just trust Copilot, these are bugs we're introducing for no reason.
# Exceptions
When we run into exceptions when debugging we now have an option to ask Copilot about the exception. This will open a chat that's not inline but instead in the separate window. Copilot will try to give you an hypothesis why the error happened. It will also have a conversation with you where it might ask for more values or context. And when the context is provided the conversation continues.

What feels good about this is also that Copilot doesn't have more context than you've given it. While this way of working might feel slow, it also feels safer to know that Copilot isn't taking everything including things it might not need. 

In the chat window you can give Copilot a selection of code by using the # selector which will open a context menu where you can pick the current selected code.
# Main Takeaway
Trust nothing Copilot creates before reading it and testing it. Luckily we can also use Copilot to test it's own code. By using the command '/createtests' and we can then also further specify that we want positive and negative cases. Unit tests are by definition easier for us to read and verify, the name of the test will tell us what it's trying to do. And then the actual unit tests are thin. So it's easy to see if it works. By accepting the tests we get here we can then run tests on the code that Copilot created.

This takes the code from just generated code to making it your own code.
# Access
All Copilot for individuals now have access to GitHub Copilot Chat and GitHub Copilot preview extension for Visual Studio.
[Copilot](https://aka.ms/vs/copilot) - [Copilot Chat](https://aka.ms/vs/copilot-chat)