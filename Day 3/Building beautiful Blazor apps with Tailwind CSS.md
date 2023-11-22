---
tags:
  - Conference
  - dotnet
  - Tailwind
  - blazor
  - CSS
  - Frontend
---
[Previous Session](Spatial%20Data%20with%20Entity%20Framework%20Core%20and%20.NET%20MAUI.md) - [Next Up](Blazor-testing%20from%20A%20to%20Z.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speaker: Chris Sainty - Software Developer/Author/Content Creator/Microsoft MVP, Enable_
[Link to vod](https://www.youtube.com/watch?v=QIdedo8iI4Y)

>[!note]+ Summary
>Installing Tailwind CSS into your Blazor is as easy as it is for your regular JavaScript framework projects. The only change is that you add in your Razor to the content array instead of whatever JavaScript framework filetype. Tailwind also combines very neatly with Blazor's component model in order to make reusable styled components for your project. 

[Tailwind website](https://tailwindcss.com/) - [Tailwind Cheat Sheet](https://nerdcave.com/tailwind-cheat-sheet) 

_I will assume you know what Tailwind CSS, but he does explain what it is and how it compares to Bootstrap._
# Adding Tailwind to a Blazor Project 
## Option 1 - CDN
The first way we can use is to use the Tailwind Play CDN. 

This is a very fast way to get going, but you're not ready to commit to using it fully yet. Maybe you're just testing it out, or trying out to see if it will fit you, your company, or your team. Adding Tailwind via CDN is as easy as adding the CDN to the `<head>` of your page.

```html
<head>
	<script src="https://cdn.tailwindcss.com"></script>
</head>
```

This also gives you access to the plugin system of Tailwind, they're all usable together with the CDN. 

This is however **not a production solution**. Due to the CDN dynamically creating script-tags and adding in the style classes that you're using that way it won't scale properly. It's great for starting out but you will get into pains sooner or later using this method for real things.
## Option 2 - Tailwind CLI
This is the primary way that you should use together with Blazor, and it's the primary focus of this talk. 

This talk uses Node.JS and npm for the Tailwind parts on the CLI. However if you for whatever don't like Node/npm there's a standalone Tailwind executable that you can use. 

The first thing we do is we run `npx tailwindcss init`. This will create a Tailwind configuration file in our project. If we're happy with all the defaults we can almost start going. We just have to fill in the content array of the configuration file. 

```js
content:['./**/*.{razor,html}'],
```

This will tell the Tailwind JIT compiler to track all our razor and html files, and check those for any Tailwind utility classes.

Next up he creates a Styles folder containing a `app.css` file. And in there is the standard Tailwind configuration that is familiar if you know Tailwind.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

The last thing you have to do is to link to `css/app.css` in your `index.html` file. This is the CSS file that Tailwind generates with all of it's styles. To generate this file we do another CLI command
`npx tailwindcss -i .\Styles\app.css -o .\wwwroot\css\app.css -watch`. This will input the CSS file we just created; and output to the `wwwroot css` folder, which is the same folder we linked to in our `index.html`. Adding on `--watch` will tell Tailwind to keep watching and regenerating when changes are made.

With all of this we can now use Tailwind utility classes in our components. 
# Reusing Utility Classes
If we want to reuse classes across our page, say style all our anchor tags or all our headings the same way. You can just duplicate the code, which is something that Tailwind doesn't discourage. Another naive approach would be to break out your classes into a CSS file and use the apply keyword. 

Example:
```css
h1{
	@apply text-gray-800 text-xl mb-2
}
```

What makes this a [naive approach](https://tailwindcss.com/docs/reusing-styles#avoiding-premature-abstraction) is that if you keep doing this for everything you're just writing regular CSS. You're not really gaining anything by using Tailwind.

It's better to use a component based approach. Where you create a `MainTitle` component instead which got the same markup and styling that's now reusable. 
# Variables
We can use variables in our html classes. This enables us to change Tailwind values dynamically.
```razor
<div class="h-full transition duration-500 transform @_animationStyles">
	
</div>

@code{
	private string _animationStyles = "translate-x-full opacity-0"
}
```
This will add in the string `_animationStyles` into the class declaration of the div. We can then change this string and call a `StateHasChanged()` to change the Tailwind property at runtime and such have animations.
# Moving to production
One option is to put in scripts for running Tailwind into your CI-pipeline.

Another option is to add targets to your `csproj` file.

```xml
<Target Name="CheckForNpm" BeforeTargets="BuildCSS">
	<Exec Command="npm -v" ContinueOnError="true">
		<Output TaskParameter="ExitCode" PropertyName="ErrorCode" />
	 </Exec>
	 <Error 
		 Condition="'$(ErrorCode)' != '0'" 
		 Text="You must install NPM to build this project." 
	 />
</Target>

<Target Name="BuildCSS" BeforeTargets="Compile">
	<Exec 
	   EnvironmentVariables="NODE_ENV=production"
	   Command="npx tailwindcss -i Styles/app.css -o wwroot/css/app.css --minify"
	   Condition="'$(Configuration) == 'Release" 
	/>
</Target>
```

This will create all the proper files for us, that's also been minimized for us. And this only happens when we build in Release mode, which means we're not doing this extra step when just debugging.