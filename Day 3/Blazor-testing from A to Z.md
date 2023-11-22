---
tags:
  - Conference
  - dotnet
  - blazor
  - Testing
---
[Previous Session](Building%20beautiful%20Blazor%20apps%20with%20Tailwind%20CSS.md) - [Next Up](Improve%20your%20ASP.NET%20core%20web%20app%20performance%20using%20Azure%20Cache%20for%20Redis.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Egil Hansen - Distinguished Developer, Delegate_
[Link to vod](https://www.youtube.com/watch?v=aorfcDeHUpw) - [Demo Repo](https://github.com/egil/BlazorTestingAZ)

>[!note]+ Summary
>Blazor supports Playwright for end-to-end testing. Together with `WebApplicationFactory` you can easily create e2e tests that uses the actual browser while keeping the applications in-process. The recommended test-runner is NUnit or MS Test. For component testing bUnit makes it very simple to create small unit-tests. The library makes it easy to write thin tests that follow the AAA principle.
>

_Egil speaks very fast with a Danish accent. If you watch the vod, be aware of this and you might have to slow him down._
# Writing valuable tests for Blazor
There's one thing to write a lot of tests, but it's more important to write tests that actually are suitable and test what's relevant. 
# End-to-End testing or Integration testing
If your Blazor application is consuming APIs, talks to a database then you're probably going to fall into this scenario. It's important to test the application the same way that the user will use it through the web browser. So we're testing all the HTML, CSS, JavaScript and Blazor Component code, and ensuring that they're working together as we expect them to.
## Playwright
Playwright is one of the go-to tools for frontend testing at the moment. And it got support for [dotnet](https://playwright.dev/dotnet/). Playwright comes with a lot of developer focused tools which allows you to write efficient code, since it comes with things like automatic retries and waiting for component to load. You don't have to write code for that yourself.

Telerik Test Studio is an alternative if you don't want to use Playwright.

---
To be able to run our Blazor application isolated way so that it's no interfere with other services that might be running you use the `WebApplicationFactory` that's created by Microsoft. This will spin up an application in process with the running test. This will give you an HttpClient that gives you access to this under test application. This is a problem for Playwright however since it needs a real web browser in order to do it's work. But the factory is configurable to expose the application it's running on an IP-address that's outside the process. Allowing Playwright to do what it needs to do, while still keeping the applications separate and non-interfering. 
# Demo
## Project Setup
The setup is quite simple. The primary supported test runner for Playwright is [NUnit](https://playwright.dev/dotnet/docs/test-runners). Otherwise you're missing out on running parallel tests. In the NUnit test project you're also going to want to install Playwright of course, and `Microsoft.AspNetCore.Mvc.Testing`, this is the library that gives us that webapplication factory that we need. Egil also uses the **Verify** libraries for Playwright and NUnit.

Building this will spit out scripts into our Debug folder. Which we can run with the install command in order to get all of the dependencies and so on that Playwright needs
`.\bin\Debug\net8.0\playwright.ps1 install`. 
## Test Setup
We now want to combine Playwright and our WebApplication Factory, so that the browser we're getting from Playwright is actually able to access the in-process webapplication.

We do this by creating a `BrowserNewContextOptions` and give it the factory's server-address. This is not hardcodable since the factory will use random ports. We also tell it to ignore HTTPS errors since that's a problem with dev-certs. We can then use this browser-context to create a new context for the Playwright browser and to launch a new page. This new page will then be served by the in-process application.
## Abstraction
It's a good idea to generalize and create a "Base Page Test" class. That does the set up that you need, all the create context and page and so on. This is not something you want duplicated in each and every test. It becomes a maintainability nightmare. Instead having the base class and being to derive from it allows you to have a much better developer experience. 

Using Blazor you can have server rendered sites, which streams down intractability. This means you have to wait a little while, (the tests can't run as soon as it would like). This can also be abstracted where you create an extension method that will go to a given page, but it will actually wait there until the network is idle. 

**Example by Egil:**
```csharp
public static Task<IResponse?> GotoPreRenderedAsync(this IPage page, string url)
	=> page.GotoAsync(url, new() {WaitUntil = WaitUntilState.NetworkIdle});
```

This allows you to define this in one place instead of each test. It also allows you to change in the future what the implementation means.
## Database testing
Through the "page-testing" we gain access to the `ConfigureWebHost` override, in which we can override the implementation of the repository or service that talks to the database with our own, stubbed version. This gives us control of how the database works while still using an actual database if we'd like with Docker etc.
## Snapshot testing
This allows you to capture the entire markup. Which is done with the Verify library. You can capture the entire html of the page. You can also use Verify to do visual screenshots of the page and store these as Jpegs (other formats are supported). 
# Component Testing or Unit-Testing
For this you could use the library [bUnit](https://bunit.dev/), **note** that this is a library developed by Egil. bUnit doesn't use the browser, it's only the semantic html you've written in the component, and the C# code that's been written in the component that's actually tested. bUnit doesn't care about the render mode of the components, everything works. 
## Project setup
In order to setup bUnit you simply add the library and you add the Verify library for bUnit. You also need to change the `csproj` for the test project, adding to the sdk so you're allowed to run the tests in razor/Blazor files `<Project Sdk="Microsoft.NET.Sdk.Razor">`.
## Using bUnit
Egil uses bUnit inside razor components for the ease of development. Writing the tests are very simple, you just follow the AAA structure of testing. You create a bUnit context which allows you to render components inside of it, then you render the component you want to test and store it in a variable. Then you can act and write assertions similar to how we did during e2e and integration testing, the only difference is that these are individual components inside the bUnit context.
## Abstraction
You can abstract away the Context creation into it's own type and inherit it in your tests. This way the creation and disposing is handled in a uniform manner.
## Key Feature
bUnit shines when you have shared components, that are used in more than one location. The setup is similar, you render the component inside a bUnit context. You're able to go in and find the component that's under test, and dive into that component and explore it's tree and it's properties. Since at the end of the day it's just C#. 

You can also assert on the markup. This is a method on the component under test where you can assert that components markup against a given markup, and see if they are the same. This will work on the semantics not on the raw typed html. Meaning if you go and change the way the CSS classes are ordered in the component, that's still the same component semantically. And the test will still be approved.

You can also pass in a bunch of different testcases to the test. Combinations of the same component but with different states. Then you can also use Verify to assert on these. And create up a bunch of different files for the components.