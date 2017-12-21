# Entity Framework Text Templating Improvements

TextTemplating files to use with Entity Framework as Code Generation items, avoiding Circular Reference when serialized (json) + DB methods (InsertALotsOfEntity)

These are the result of a struggle to serialise any entity from the framework as json.
Two problems were happening:
* Circular Reference Exception (due to navigation properties)
* Proxy class name instead of declared

Also, I took this opportunity to add a DBUtils code generation to add the most basic methods (Get, AddOrUpdate, etc...) but also a InsertALotsOfEntity method to insert more than a thousand entity in a row and very quickly.

## Getting Started

Copy/paste the content of [ModelName].Context.tt and [ModelName].tt, add new Code generation item with the last text templating : [ModelName].tt (with the edmx file open)
(By convention, I replacing [ModelName] by the entity model name)

## Prerequisites

* Software
 * Visual Studio 2012 - 2017
 * Entity Framework 5
 * Compatible with EF6

* Components
 * Applicable under .edmx file

[[readme.assets/ADO.NET file.PNG]]


## Next To Come
* how to
* usage code sample

### ENJOY !