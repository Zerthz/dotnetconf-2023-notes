---
tags:
  - Conference
  - dotnet
  - database
  - EFCore
aliases:
  - "Entity Framework Core 8: Improved JSON, queryable collections, and more..."
---
[Previous Session](What's%20new%20in%20Csharp%2012.md) - [Next Up](dotnet%20💖%20AI.md) - [.NET Conf 2023 Parent Page](README.md)

---
_Speakers:
Arthur Vickers - .NET Data Engineering Manager, Microsoft
& Shay Rojansky - Principal Software Engineer on Data Access and Performance, Microsoft_
[Link to vod](https://www.youtube.com/watch?v=_8iH5QnkIJo)

>[!note]+ Summary
>Document and relational databases both have strengths. EF core with this update aims to converge and take advantage of these strengths, while still having common patterns for both schemas. 
>EF Core uses the first-class JSON support modern relational databases comes with, and together with a rich EF model it can query for specific things inside JSON columns.
>It also enables powerful patterns like primitive collections, and collection of entities. Support for PostgreSQL, SQL Server, SQLite.

EF 8 fun with Json. In this this session they have a model that's a customer. A customer has a bunch of standard primitive fields. It also has CustomerDetails which in itself contains  Addresses, and Phone Numbers. Both which are complex types. So when we talk about spreading properties we talk about spreading addresses and phone numbers across tables. This is what I mean with nested properties. We get 4 tables out of this, since it's spread.
# Document vs Relational Databases
Both databases types are good and bad at certain things. And Entity Framework Core aims to be the leverage common patterns for both types, like [[UoW]] and [[LINQ]]. But it also want to leverage whatever it is the database is good at too. As a document database is good at point-reading, they want EF to be able to do this too. And they want to promote best practices.

Since they want to leverage the different databases features you can't just swap databases under EF like if EF was an abstraction layer over a generic DB.
## Relational Database
EF will do all the classic things you expect when you're working in a relational db mode, EF will spread your model over an appropriate amount of tables joining them together with foreign keys. 
## Document Database
Even the output is different, since it doesn't create a schema. It just creates a document database. And enters the model into that. It nests properties too instead of spreading them across tables. Every model gets their own document instead. It's just different strategies. 

The nested properties doesn't need foreign keys since it's embedded right there into the document.

---
Since Document databases is good at point-reading EF lets you do this. You read the entire document all at ones, you query the db for a name for example and you get back the entire document and all the fields are available, even the nested ones. No need for roundtrips.

If you just then swap the document db to a relational db without changing your code you get a no reference exception. EF will not get the entire graph included the nested properties, it will only get from the table you requested from, so when you try to drill down you don't get what you want. Since EF hasn't loaded that. The way to work around this is to use includes. 

Joins is heavy work for a db which is why you don't do it by default, you don't want to do heavy load work every time. 

The competitive nature between relational databases and document databases leads each paradigm to improve on the competitors strong suit. Document databases improve things that can seem to fit a relational database and vice versa. 

For example you can tell EF using a relational database to not spread properties across tables, but to instead nest them into Json. This allows us to do the query the name and get all the details because it's now Json and it's right there in the same table. 

This is not a either or, you can still have other relations with standard joins. Mix and match as you need. All relational databases got JSON support these days. We can use this to query into the JSON columns. By targeting properties in the JSON. Which it couldn't do if you just dump Json into a text column. We can now also use LINQ operators on JSON arrays. 

You can also use the LINQ operations on sets of primitives like DateTimes, and you can also use parameters. The parameters are not part of the SQL.
# Big fix
The example they use here is they query the database on Customers who have been since one of a list of specific dates. So you want the rows that hit those Ids. In relational database it's hard because you don't have arrays. In EF7 doesn't put the parameters for this as parameters, they're using them as literals, because there's no concept of arrays. This is a problem due to the fact that when you have parameters the database can cache the plan. And as long as it sees the same SQL it can use the same SQL as it's cached. This is true as long as the SQL itself doesn't change and you have proper parameters. The issue then with EF7 is that they're not parameters, so every time you run the SQL with different parameters the db has to do all the heavy lifting and plans again. This leads to massive performance issues. And up until this release it was EF cores 2nd most requested feature.

The new EF8 feature will turn the parameters that were a list into a JSON array, because the database knows how JSON works, but not arrays. So you can then use the parameters and do the heavy lifting of planning etc. only once.
# Also new
[Website with changes](https://aka.ms/ef8-whats-new)
![](dotnetconf-23-new-ef8.png)

There's also an EF Core provider for MongoDB.