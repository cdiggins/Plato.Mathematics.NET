# Plato.Mathematics.NET

A modern mathematical library for C# and .NET Standard 2.0 written in Plato and cross-compiled into C#.  

# Motivation

We needed for our work a 2D and 3D graphics and mathematical library which was compatible with .NET Standard 2.0  and that had consistent serialization behavior across different platforms and implementations of the .NET run-time. The library was used in applications developed for Windows, Unity, and mobile, and for plug-in development. 

Building numerical libraries for older versions of C# (e.g. pre C# 11 and .NET 8) requires a lot of boilerplate. This is because there is no way to write generic algorithms with overloaded operators that would work on groups of types.    

A common approach to building this kind of library is to use code generation tools such as [T4 Text Templates](https://learn.microsoft.com/en-us/visualstudio/modeling/code-generation-and-t4-text-templates?view=vs-2022) or source generators. These tools take a bit of time to develop and debug, but do produce good results. When we start creating larger libraries with them, certain patterns started to emerge in the operations we generated for different types.

These types of patterns revealed that there was a simpler declarative language underneath which wanted to reveal itself, and thus was started the Plato language.   

# An Aside: The Problem with Generic Math in C# 11 

C# 11 introduced some features specifically designed to help with writing [generic math libraries](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-11#generic-math-support). 

These features were added to the language in a manner which seemed to be intended to decrease the amount of syntax churn and maximize backwards compatibility. The result is that terminology and features are becoming very complex for newcomers. Out of context does a `private static virtual interface function` even make sense?   

Over time the choice of data structures for writing has become very complex. Consider if I want a new type called Pose to represent 3D position and rotation. What should it be? 

* class
* struct
* record
* record struct
  
And if it is a struct should it be `readonly` or not, should it be `ref` or not? If it is a class or a record should it be `sealed`? What are the implications and tradeoffs of the different choices? Even for a very experienced developer the choice between what approach to use becomes a bit arbitrary.    

# The Plato Approach

Plato has one very specific task to solve: write generic mathematical routines in a clear and straightforward way, with a minimum amount of boilerplate. 
Plato started off as a C#-style language, and then removed as many superfluous features as possible while still remaining an expressive language.   

Some of the major differences from C# are:

* All data structures are immutable.
* All functions are effectively static extension methods 
* Everything is public
* There are no `virtual` or `abstract` methods
* There is no implicit `this` parameter
* Fields and properties are indistinguishable
* Any function without arguments can be called without parentheses.
* Operators are generated from functions with specific names (e.g., Add, Not, etc.)
* All types can be implicitly cast to/from a value-tuple that has the same shape
* Only fields are declared in types, their operations all declared in libraries.
* Constructors and with methods are generated implicitly    

Plato has three top-level abstractions:

* Types - concrete data structures (similar to classes)
* Concepts - abstract data types (similar to interfaces)
* Libraries - collections of functions with no state    

It would be more accurate to say that a Plato `type` is like a `ref readonly record struct` but that would be a bit confusing.  

<!--
form, e.g., (3.12, 4.20) can be passed where `Point2D`, `Complex`, or a `Vector2D` is expected.   
-->

# Concepts

A concept is very much like an interface. It declares a set of operations that a compatble data type must implement. Data types 
can declare which concepts they implement. A concept can be used as a type declaration. 

## Self Types 

Concepts allow a special type identifier called `Self` to be used in the 
type expressions. This represents the concrete type which is implementing the interface. 

```
concept Equatable
{
    Equals(a: Self, b: Self): Boolean;
    NotEquals(a: Self, b: Self): Boolean;
}
```

Translated to C# this looks like:

```
public interface Equatable<Self>
{
    Boolean Equals(Self b);
    Boolean NotEquals(Self b);
}
```

Concepts are allowed to declare type parameters. Type parameters are names followed by a "constraint" 
which is another concept that the type parameter must implement. If there is no restriction on which type
can be allowed as parameters, then the top type `Any` may be used.  

For example: 

```
concept AdditiveArithmetic<T: Any>
{
    Add(self: Self, other: T): Self;    
    Subtract(self: Self, other: T): Self;
}
```

This translates to:

```
public interface AdditiveArithmetic<Self, T>
{
    Self Add(T other);
    Self Subtract(T other);
}
```

# Where is the Plato implementation? 

While the output of the Plato to C# translator is relatively robust, the tooling for Plato is still in a very experimental stage and not ready for wider distribution. 

Reach out to me, [Christopher Diggins](mailto:cdiggins@gmail.com). 
