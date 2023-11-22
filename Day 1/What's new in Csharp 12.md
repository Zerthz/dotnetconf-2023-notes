---
tags:
  - Conference
  - dotnet
  - CSharp
aliases:
  - What's new in C# 12
---
[Previous Session](Performance%20Improvements%20in%20.NET%208,%20ASP.NET%20Core,%20and%20.NET%20MAUI.md) - [Next Up](Entity%20Framework%20Core%208%20Improved%20JSON,%20queryable%20collections,%20and%20more....md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers:
Mads Torgersen - Lead Designer of C# language, Microsoft
& Dustin Campbell - Principal Software Engineer, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=by-GL-SjHdc)

>[!note]+ Summary
>Primary constructors is probably going to be the biggest take away from this feature of C# 12. Making the code for many types simple to write since you don't have to declare a full separate constructor. Together with capturing the parameters into properties you get less fields and such too.
>
>New syntax for generating collections that will unify the creation syntax. Allowing you to have an easier flow if you need to change collection type. 

# Using Type Alias
Type aliasing has been a part of the language since forever, but up until now you had to be explicit, which you don't need to anymore.
```Csharp
// Old Version
using Grade = System.Decimal;
// New Version
using Grade = decimal;
```
# Primary Constructors
Primary constructors have been part of records since C# 9. And they've worked there because records are meant to be opinionated, it made sense that the properties are public the way they were. C# 12 solved and introduces this for regular classes too. 

With classes you have to be explicit which data you expose outside of the wrapper. These only works internally by default, meaning that you can use them internally in that class without having to declare a separate constructor and fields.

To expose the parameters from the primary constructor you can just use a primary constructor and default it to the parameter. The readonly version below with Id captures the id parameter into Id the property. The name parameter is not stashed since it's just a parameter. This means we can not use this.id, since it's not stored as a field.

```Csharp
// declaring a primary constructor, this a constructor.
public class Student(string name, int id, Grade[] grades)
{
	// exposing Name with a parameter, and defaulting it to name parameter
	public string Name { get; set; } = name;

	// a readonly version of id. 
	public int Id = id;
}
```

Parameters are by default mutable and have been like that forever. You can capture parameters into a private field if you want to keep it around beyond initialization. Since the parameter will be used even if we capture it, for initialization purposes and at creation. And you can then make it readonly. They're talking about putting things like readonly into the primary constructor but they haven't decided yet.
## Bug note
You get notes if you're trying to double store parameters, since most people don't want to do that. What you typically want to do is to create a private field and assign to that. Since then you don't capture.
## More complex constructors
What if we want to do some logic in our constructor? Then we need to go back to regular constructors. There's no way to do that with primary constructors. This might be changed in the future but who knows. 
## VisualStudio Support
There's now a way to go from Primary Constructors to Regular Constructors by just right-clicking the type name. (Only in preview as of, 2023-11-15, but it is in C# DevKit). 

You can also do the same to go back to primary constructors. You even get a little note if your code is able to be converted. If you have fields in your code and want to do this you get 2 options, one where the refactoring will keep the private fields, and one where it will remove them.
## Constructor Chaining
This is a concept since C# 1.0 where you can chain constructors together. With primary constructors you must always use the primary constructor as a this. So you always refer back to that. And you will get errors if you don't. Sooner or later in your chaining you will call to the primary constructor.

# Collections
A new unified syntax to new up collections gets around the problem of when you want to change a collection type you have to change all the call-sites too. This new syntax unifies it so you can declare all collection types in one general way. This can be done because the types themselves declare how they want to implement the creation process. But some types like Lists is so well known and well used that the compiler knows what to do with them.

```Csharp
// this creates a myClass which news up list of ints. If we change it to array of ints // or immutable array of ints we don't have to change the callsite
var myClass = new MyClass( [1, 2, 3, 4] );

// news up a new class with an empty list of ints.
var myClass2 = new MyClass( [] );
public class MyClass(List<int> list){ ... }
```

Also works with interfaces of collections. 
You can use spreads with this collection expression. And also use multiple spreads in one expression, they don't even have to be the same type.

