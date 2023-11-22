---
tags:
  - Conference
  - dotnet
  - Cloud
---
[Previous Session](Improving%20your%20application%20telemetry%20using%20.NET%208%20and%20Open%20Telemetry.md) - [Next Up](App%20Service%20the%20best%20place%20to%20host%20your%20.NET%208%20Web%20Apps.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Martin Tomka - Senior Software Engineer, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=BDZpuFI8mMM)

>[!note]+ Summary
>Resilience is massively important for Cloud applications because they will fail sooner or later, and it's part of developing for cloud to roll with the punches with good resiliency policies.
>Polly got a new massive version that got released this September. .NET 8 takes advantage of this 
>new feature and with some easy NuGet packages you can add a more performant easy to use resilience pipeline. There are 2 pre configured pipelines setup, and you have the option to configure one on your own. Pollys docs are also improved so it's really a good time to get going with Polly.

Why do we want to have resilience? Because failures will happen, we can get network outages, [transient errors](https://learn.microsoft.com/en-us/azure/architecture/best-practices/transient-faults#why-do-transient-faults-occur-in-the-cloud) on the server side, or we can suffer a remote service outage. 
To handle this we want to have resilient cloud services, and we need to anticipate issues, recover and adapt.
# Polly - .NET Resilience Library
[www.pollydocs.org](https://www.pollydocs.org)
Polly is the most popular library for .NET, this latest version with heavy collaboration with the .NET Aspire team.

It enhances system stability and resilience, while also handle transient failures. It protects your systems resources. The way it does all of these things is through resilience strategies called [policies](https://www.pollydocs.org/strategies)
- Retry- try again.
- Circuit Breaker- Stop trying if something is broken or busy
- Timeout- give up if something takes too long
- Fallback- do something else if something fails
- Rate limiting- limit how many requests you make or accept.

Polly is powering some of the largest cloud services at Microsoft. Both for client and server applications. They've also built internal tooling to suit scenarios that wasn't officially supported by Polly yet. These added telemetry support, DI support, and options-based configuration. These internal libraries got widespread adoption within Microsoft. But they ran into issues with the design of Polly v7 APIs. Polly's performance was found lacking when it came to allocations on thot-paths. 

Microsoft brought the enhancements they've made over to the Polly team, and they also provided the maintainers and community with an improved and modernized API which was well received. So this started the Polly v8 project.
# Polly v8
A collaboration between Polly community and Microsoft. 
Brings a new modernized API and simplified design. 
Improved allocation performance along hot-paths.
Native telemetry is built in. 
Dependency injection added.
They got a new "hedging" resilience strategy. Which does more than one thing at the same time and uses the fastest one. _"hedging your bets"_.
They addressed many of the long-standing community issues and improved the documentation.
Was released in September.
## Usage
The central API is the resilience pipeline. Which can be created by using the resilience pipeline builder. You use this builder to add one or more strategies to the pipeline. There's also generic forms which requires a type and only does callbacks on that type.

# Http Client + Resilience
This is a very common scenario to need resilience. There's a seamless integration with IHttpClientFactory. It employs Polly v8 so if you know that it's easy to use. It's based around ResiliencePipeline\<HttpResponseMessage>.  

## Libraries
Microsoft has now released libraries that's built upon Polly v8 for specific needs.

	Microsoft.Extensions.Http.Resilience
For building resilient HTTP requests. It got APIs for using resilience pipelines, which includes custom pipelines, standard pipelines, and standard hedging pipelines.

	Microsoft.Extensions.Resilience
Enriches the built in telemetry functions of Polly.
# Demo
To add resilience handling to our Http Client we use the extension method after we add it to the services.

```Csharp
services.AddHttpClient(...)
	.AddResilienceHandler("test", callback);
```

The name/string/identifier is what will be used for telemetry. And it also needs a callback, the callback takes arguments. The type of this callback is the previously mentioned ResiliencePipeline\<HttpResponseMessage>. So it is here we add our resilience strategies (policies). 

When we define the retry strategy we can do it like this to get a unique policy experience. And not only use the default. But we still inherit defaults that we can choose to overwrite, so we don't have to write each and every strategy option.
```Csharp
services.AddHttpClient(...)
	.AddResilienceHandler("test", builder => 
	{
		builder.AddRetry(new HttpRetryStrategyOptions
		{
			MaxRetryAttempts = 5,
			BackoffType = DelayBackofType.Exponential,
			UseJitter = true,
			Delay = TimeSpan.FromMilliseconds(100)			
		});
	});
```

## Service Outage
So far above we don't handle a service outage at all. The solution to this is to use a circuit breaker
```Csharp
services.AddHttpClient(...)
	.AddResilienceHandler("test", builder => 
	{
		builder.AddRetry(new HttpRetryStrategyOptions{...});
		builder.AddCircuitBreaker(new HttpCircuitBreakerStrategyOptions
		{
			SamplingDuration = TimeSpan.FromSeconds(5),
			FailureRatio = 0.9,
			MinimumThroughput = 5,
			BreakDuration = TimeSpan.FromSeconds(5)
		});
	});
```
What this says is that if for a duration of 5seconds, you have a minimum of 5 requests coming through. And 90% of those 5 requests are not successful, then the Circuit Breaker will open, which means there is no communication going out. The Circuit Breaker will be open for 5seconds. Then after the break of 5seconds the Circuit Breaker will allow out a single request, called a "probing request". If that probing request is successful the Circuit Breaker is closed and the communication can flow again.

You can also just use the AddStandardResilienceHandler to get default policies that's determined by the Microsoft team. And we an change these with the Configure extension method.

## Standard Hedging Handler
Works very similar to the standard handler, but instead of using Retry strategy it uses Hedging strategy which will run requests parallel. This should only be used for [Idempotent](../../../Agnostic%20Principles/Idempotent.md) operations. Because otherwise you can get messed up state. Since Hedging by request can send multiple requests at the same time. 

```Csharp
services.AddHttpClient(...)
	.AddStandardHedgingHandler()
	.Configure(options => 
	{
		options.TotalRequestTimeout.Timeout = TimeSpan.FromSeconds(10);

		// The difference from standard handler
		options.Hedging.MaxHedgedAttempts = 5;
		options.Hedging.Delay = TimeSpan.FromMilliseconds(50);

		options.Endpoint.CircuitBreaker.SamplingDuration = TimeSpan.FromSeconds(5);
		options.Endpoint.CircuitBreaker.MinimumThroughput = 5;
		options.Endpoint.CircuitBreaker.FailureRatio = 0.9;
		options.Endpoint.CircuitBreaker.BreakDuration = TimeSpan.FromSeconds(5);

		options.Endpoint.Timeout.Timeout = TimeSpan.FromSeconds(1);
	});
```
This has a very similar syntax to retries. What delay means in this context is that if after 50ms you haven't received a successful response; then send a new request. And pick the fastest one that works. 