# Plato.Mathematics.NET

A modern mathematical library for C# and .NET Standard 2.0 written in Plato and cross-compiled into C#.  

# Motivation

We needed for our work a 2D and 3D graphics and mathematical library which was compatible with .NET Standard 2.0  and that had consistent serialization behavior across different platforms and implementations of the .NET run-time. The library was used in applications developed for Windows, Unity, and mobile, and for plug-in development. 

Building numerical libraries for older versions of C# (e.g. pre C# 11 and .NET 8) requires a lot of boilerplate. This is because there is no way to write generic algorithms with overloaded operators that would work on groups of types.    

A common approach to building this kind of library is to use code generation tools such as [T4 Text Templates](https://learn.microsoft.com/en-us/visualstudio/modeling/code-generation-and-t4-text-templates?view=vs-2022) or source generators. These tools take a bit of time to develop and debug, but do produce good results. When we start creating larger libraries with them, certain patterns started to emerge in the operations we generated for different types.

These types of patterns revealed that there was a simpler declarative language underneath which wanted to reveal itself, and thus was started the Plato language.   

# An asside: The Problem C# 11 Generic Math 

C# 11 introduced some features specifically designed to help with writing [generic math libraries](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-11#generic-math-support). 

These features were added to the language in a manner which seemed to be intended to decrease the amount of syntax churn and maximize backwards compatibility. The result is that terminology and features are becoming very complex for newcomers. Out of context does a `private static virtual interface function` even make sense?   

Even the choice of data structure has become very complex. Consider if I want a new type called Pose to represent 3D position and rotation. What should it be? 

* class
* struct
* record
* record struct
  
And if it is a struct should it be `readonly` or not, should it be `ref` or not? If it is a class or a record should it be `sealed`? 

# Plato Solution 
