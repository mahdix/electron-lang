# dot Programming Language Reference

Make it as simple as possible, but not simpler. (A. Einstein) 

Perfection is finally attained not when there is no longer anything to add, but when there is no longer anything to take away. (Antoine de Saint-Exupéry, translated by Lewis Galantière.)

Version 0.97

June 26, 2017

# History
- **Version 0.1**: Sep 4, 2016 - Initial document created after more than 10 months of research, comparison and thinking.
- **Version 0.2**: Sep 22, 2016 - Leaning towards Functional Programming.
- **Version 0.3**: Oct 13, 2016 - Added clarifications for inheritance, polymorphism and templates
- **Version 0.4**: Oct 27, 2016 - Removed some less needed features (monad), defined rules for multiple dispatch.
- **Version 0.5**: Nov 13, 2016 - Some cleanup and better organization
- **Version 0.6**: Jan 18, 2017 - Cleanup, introduce object type and changed exception handling mechanism.
- **Version 0.7**: Feb 19, 2017 - Fully qualified type name, more consistent templates, `::` operator and `any` keyword, unified enum and union, `const` keyword
- **Version 0.8**: May 3, 2017 - Clarifications for exception, Adding `where` keyword, explode operator, Sum types, new notation for hash-table and changes in defining tuples, removed `const` keyword, reviewed inheritance notation.
- **Version 0.9**: May 8, 2017 - Define notation for tuple without fields names, hashmap, extended explode operator, refined notation to catch exception using `//` operator, clarifications about empty types and inheritance, updated templates to use empty types instead of `where` and moved `::` and `any` to core functions and types, replaced `switch` with `match` and extended the notation to types and values, allowed functions to be defined for literal input, redefined if to be syntax sugar for match, made `loop` a function instead of built-in keyword.
- **Version 0.95**: May 23, 2017 - Refined notation for loop and match, Re-organize and complete the document, remove pre and post condition, add `defer` keyword, remove `->>` operator in match, change tuple assignment notation from `:` to `=`, clarifications as to speciying type of a tuple literal, some clarifications about `&` and `//`, replaced `match` keyword with `::` operator, clarified sub-typing, removed `//`, discarded templates, allow opertor overloading, change name to `dotlang`, re-introduces type specialization, make `loop, if, else` keyword, unified numberic types, dot as a chain operator, some clarifications about sum types and type system, added `ref` keyword, replace `where` with normal functions, added type-copy and local-anything type operator (`^` and `%`).
- **Version 0.96**: June 2, 2017 - Removed operator overloading, clarifications about casting, renamed local anything to `!`, removed `^` and introduced shortcut for type specialization, removed `.@` notation, added `&` for combine statements and changed `^` for lambda-maker, changed notation for tuple and type specialization, `%` for casting, removed `!` and added support for generics, clarification about method dispatch, type system, embedding and generics, changed inheritance model to single-inheritance to make function dispatch more well-defined, added notation for implicit and reference, Added phantom types, removed `double` and `uint`, removed `ref` keyword, added `!` to support protocol parameters.
- **Version 0.97**: June 26, 2017 - Clarifications about primitive types and array/hash literals, ban embedding non-tuples,  changed notation for casting to be more readable, removed `anything` type, removed lambda-maker and `$_` placeholder, clarifications about casting to function type, method dispatch and assignment to function pointer, removed opIndex and chaining operator, changed notation for array and map definition and generic declaration, remove `$` notation, added throw and catch functions, simplified loop, introduced protocols, merged `::` into `@`, added `..` syntax for generating array literals, introduced `val` and it's effect in function and variable declaration,  everything is a reference, support type alias, added `binary` type, unified assignment semantic, made `=` data-copy operator, removed `break` and `continue`, removed exceptions and assert and replaced `defer` with RIAA, added `_` for lambda creation, removed literal and val/var from template arguments, simplify protocol usage and removed `where` keyword, introduced protocols for types, changed protocol enforcement syntax and extend it to types with addition of axioms, made `loop` a function in core, made union a primitive type based on generics, introduced label types and multiple return values, introduced block-if to act like switch and type match operator, removed concept of reference/pointer and handle references behind the scene, removed the notation of dynamic type (everything is types statically), introduced type filters, removed `val` and `binary` (function args are immutable), added chaining operator and `opChain`.
- **Version 0.98**: ?? ??? ???? - remove `++` and `--`, implicit type inference in variable declaration, Universal immutability + compiler optimization regarding re-use of values, new notation to change tuple, array and map, `@` is now type-id operator, functions can return one output, new semantics for chain operator and no `opChain`, no `opEquals`, Disposable protocol, `nothing` as built-in type, Dual notation to read from array or map and it's usage for block-if, Closure variable capture and compiler re-assignment detection, use `:=` for variable declaration, definition for exclusive resource, Simplify type filters, chain using `>>`, change function and lambda declaration notation to use `|`, remove protocols and new notation for polymorphic union, added `do` and `then` keywords to reduce need for parens


# Introduction
After having worked with a lot of different languages (C\#, Java, Perl, Javascript, C, C++, Python) and being familiar with some others (including Go, D, Scala, Rust and Haskell) it still irritates me that most of these languages sometimes seem to _intend_ to be overly complex with a lot of rules and exceptions to keep in mind. This doesn't mean I don't like them or I cannot develop software using them, but it also doesn't mean I should not be looking for a programming language which is simple, powerful and fast.

That's why I am creating a new programming language: dot (or dotLang). 

dot programming language (or dotLang for short) is an imperative, static-typed, general-purpose language based on author's experience and doing research on many programming languages (namely Go, Java, C\#, C, C++, Scala, Rust, Objective-C, Python, Perl, Smalltalk, Ruby, Swift, Haskell, Clojure, Eiffel, Elm, Falcon, Julia, F\# and Oberon-2). 
I call the paradigm of this language "Data-oriented". This is a combination of Object Oriented and Functional approach and it is designed to work with data. There are no objects or classes. Only data types and functions. But most useful features of the OOP (encapsulation, abstraction, inheritance and polymorphism) are provided to some extent. On the other hand, we have first-class and higher-order functions borrowed from functional approach.

Three main objectives are pursued in the design of this programming language:

1. **Simplicity**: The code written in dotLang should be consistent, easy to write, read and understand. There has been a lot of effort to make sure there are as few exceptions and rules as possible. Software development is complex enough. Let's keep the language as simple as possible and save complexities for when we really need them. Very few things are done implicitly and transparently by the compiler or runtime system. Also I tried to reduce need for nested blocks and parentheses as much as possible.
2. **Expressiveness**: It should give enough tools to the developer to produce readable and maintainable code. This requires a comprehensive standard library in addition to language notations.
3. **Performance**: The compiler will compile to native code which will result in high performance. We try to do as much as possible during compilation (optimizations, de-refrencing, in-place mutation, sending by copy or reference, type checking, type filters, phantom types, ...) so during runtime, there is not much to be done except mostly for memory management. Where performance is a concern, the corresponding functions in standard library will be implemented with a lower level language.

Achieving all of the above goals at the same time is impossible so there will definitely be trade-offs and exceptions.
The underlying rules of design of this language are 
[Principle of least astonishment](https://en.wikipedia.org/wiki/Principle_of_least_astonishment), 
[KISS rule](https://en.wikipedia.org/wiki/KISS_principle) and
[DRY rule](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

As a 10,000 foot view of the language, code is written in files (called modules) organised in directories (called packages).  We have functions and types. Each function acts on a set of inputs and gives an output. Type system includes primitive data types, tuple, union, array and map. Polymorphism, template programming and lambda expression are also provided and everything is immutable.

## Comparison with other languages

**Compared to C**: dotLang is C language + Garabage collector + first-class functions + template programming + better union data types + module system + powerful polymorphism + simple and powerful standard library + lambda expressions + closure + powerful built-in data types (map, string,...) + multiple dispatch + sane defaults + better immutability + concepts and axioms - ambiguities - pointers - macros - header files.

**Compared to Scala**: Scala + multiple dispatch + custom immutability + concepts and axioms  - dependency on JVM - cryptic syntax - trait - custom operators - variance - implicit.

**Compared to Go**: Go + generics + immutability + multiple dispatch + sum types + sane defaults + better orthogonality (e.g. creating maps) + simpler primitives + concepts and axioms  - pointers - interfaces - global variables.


## Components

dotLang consists of these components:
1. The language specification (this document)
2. A command line tool to compile, debug and package applications
3. Runtime system: Responsible for memory allocation and management, interaction with the Operating System and other external libraries and handling concurrency.
4. Core library: This package is used to implement some basic, low-level features which can not be simply implemented using pure dotLang language.
5. Standard library: A layer above runtime and `core` which contains some general-purpose and common functions and data structures.

## Code organization

- **Module**: Source code is written inside files which are called "Modules". Modules contain definitions of data structures and functions. Each module can reference other modules to call their functions or use their data structures.
- **Package**: Modules are organized into directories which are called packages. Each package is represented by a directory in the file-system. Packages have a hierarchical structure:
```
core  
|-----sys  
|-----net  
|-----|-----http  
|-----|-----tcp  
```
In the above examples `/core, /core/sys, /core/net, /core/net/http, /core/net/tcp` are all packages.
- Unlike many other languages, modules are stateless. Meaning there is no variable or static code defined in a module-level.

## General rules
- **Encoding**: Modules are encoded in UTF-8 format.
- **Whitespace**: Any instance of space(' '), tab(`\t`), newline(`\r` and `\n`) are whitespace and will be ignored. 
- **Indentation**: Indentation must be done using spaces, not tabs. Using 4 spaces is advised but not mandatory.
- **Comments**: `//` is used to start a comment.
- **Literals**: `123` integer literal, `'c'` character literal, `'this is a test'` string literal, `0xffe` hexadecimal number, `0b0101011101` binary number. You can separate digits using undescore: `1_000_000`.
- **Terminator**: Each statement must be in a separate line and must not end with semicolon.
- **Order**: Each source code file contains 3 sections: import, definitions and function. The order of the contents of source code file matters: `import` section must come first, then declarations and then functions come at the end. If the order is not met, compiler will give errors.
- Import section is used to reference other modules that are being used in this module.
- Definitions section is used to define data types and protocols.
- Function section is used to define function bodies.
- **Adressing**: Modules are addressed using `/` notation (e.g. `/code/st/net/create_socket`). Where `/` denotes include path.
- **Encapsulation**: If a name (of a type, protocol or function) starts with underscore, means that it is private to the module. If not, it is public. This applies to functions and types.
- **Naming**: (Highly advised but not mandatory) `someFunctionName`, `my_var_name`, `SomeType`, `MyProtocol`, `my_package_or_module`. If these are not met, compiler will give warnings. Primitives (binary, int, float, char), and types defined in core (bool, array, map, string) are the only exceptions to naming rules.

## Language in a nutshell
1. **Primitives**: `int`, `float`, `char`, `union` (Extended primitives: `bool`, `array`, `string`, `map`).
2. **Tuple**: `type Point := {x: int, y:int, data: float}`.
3. **Variable**: `var location: Point = { x=10, y=20, data=1.19 }`.
4. **Inheritance**: By embedding (only for tuples), `type Circle := {Shape, radius: float}`.
5. **Array**: `var JobQueue: array[int] = [0, 1, 2, 3]`.
6. **Generics**: `type Stack[T] := { data: array[T], info: int }`.
7. **Union**: `type Optional[T] := union[Nothing, T]`.
8. **Map**: `var CountryPopulation: map[string,int] = [ "US": 300, "CA": 180, "UK":80 ]`.
9. **Function**: `func calculate(x: int, y: string) -> float { return if ( x > 0 ) 1.0 else 2.0  }`.
10. **Import**: `import /core/std/Queue`.
11. **Immutability**: `val x: int = 12` (no change or re-assignment to `x` is allowed).
12. **Assignment**: `A=B` makes a copy of B's data into A.
14. **Casting**: `var pt = @Shape(myCircle)`.
15. **Lambda**: `var adder: func(var:int,var:int)->val:int = (x,y) -> x+y`.
16. **Protocols**: `protocol Comparable[T] := { func compare(T, T)->int }`, `func sort[T](x:array[T]) +Comparable`.

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><

# Type System

Note that using `=` for resources (like threads or files) will not create new resource. Just create new variables pointing to the same resource.

We have universal immutability. Everything is immutable. You can however re-assign a variable to a new value.
Compiler will detect local variable updates which are not escape and optimize them to use mutable variable (for example for numerical calculations which happens only inside a function).
Channels are the main tool for concurrency and coordination.

## Variabe declaration

**Semantic**: Used to declare and assign a value to a variable.

**Syntax**: `Identifier ':=' exp`

**Examples**

1. `x := 12`
2. `g := 19`
3. `count := 100`

**Notes**

1. Example 1 defines a variable called `x` which is of type `integer` and stores value of `12` in it.
2. You cannot use an uninitialized variable. If you do, you will receive a compiler error.
3. Every variable must have a type either explicitly specified or implicitly inferred from assignment.
4. `exp` is an expression which means it can either be a literal, function call, another variable or a complex expression.
5. There must be a single space between `var` keyword and the idenfitier.


## Assignment
Variable must be declared before.

An assignment does not mutate a variable. It creates a new memory cell containing result of right side of `=` and re-assigns left-side variable to that memory address.
You must re-assign a variable to a new value which is of the same type of the variable.

**Semantics**: To make a copy of a piece of data (called rvalue) and put it somewhere else (called lvalue).

**Syntax**: `lvalue = rvalue`

**Examples**

1. `x = 10`
2. `x = y`
3. `x = y + 10 - z`
4. `x = func1(y) + func2(z) - 10`
5. `x,y = 1,2`

**Notes**:

1. `lvalue` is the identifier which is on the left side of assignment operator `=`. It must be a single identifier.
2. `lvalue` cannot be a function argument because they are immutable. More exaplanation in the Functions section.
3. Note that you cannot re-use lvalues on rvalue section (`a,b=b,a` is invalid).

## Primitives

**Semantics**: Provide basic tools to define most commonly used data types.

**Syntax**: `int`, `float`, `char`, `union`, `array`, `slice`, `map`
`nothing`

**Examples**

1. `x = 12`
2. `x = 1.918`
3. `x = 'c'`

**Notes**:

1. `int` type is a signed 8-byte integer data type.
2. `float` is double-precision 8-byte floating point number.
3. `char` is a single character, represented as an unsigned byte.
4. Character literals should be enclosed in single-quote.
5. For `union`, `array` and `map` types, refer to the following sections.

## Union

**Semantics**: A primitive meta-type to provide same interface which can contain different types.

**Syntax**: `union[type1, type2, ...]`

**Examples**

1. `var int_or_float: unon[int, float]`
2. `int_or_float = 12`
3. `int_or_float = 1.212`
4. `var has_int: bool = int_or_float.(int)`
5. `var int_value, success = int_or_float.(int)`
6. 
```
var stringed = if ( int_or_float ) {
    (int) -> ["int" , toString(int_or_float)],  //inside this block, int_or_float is automatically treated like an int
    else -> "Not_int"
}
```
7. `var day_of_week: union[SAT, SUN, MON, TUE, WED, THU, FRI]`


**Notes**
4. The identifiers used in example number 6 are called "label types" and are explained in the corresponding section.

1. `union` is a meta-type which can take form of different data types. It uses generic programming features which are explained in corresponding section. For more information refer to the next section.
2. Types inside union definition must be named types. Refer to corresponding section.
3. `union[int, union[float, string]]` will be simplified to `union[int, float, string]`
4. Example 4: In a boolean context, `x.(type)` evaluates to a bool indicating true if `x` contains given type.
5. Example 5: If `x.(int)` is used otherwise it evaluates to an integer and a boolean indicating desired type and a flag for whether casting is successfull or no.
7. You can use `type(x)` notation in a block-if (Example 6). Refer to corresponding section for more information about `if`.

## Array
To read from array: `x = arr(0)` or `x = (0)arr`
To update array: `arr = arr[0=>10, 2=>20]`
define array literal: `arr = [1,2,3]`
multi-d array:
`g:=x2(0)(0)`
`y2 :=x2[0,0=>102]`
Array read returns `Maybe[T]` so if index is incorrect it will return `nothing`.

**Semantics**: Define a fixed-size consecutive sequence of element of the same type

**Syntax**: `array[type]`

**Examples**

1. `var arr: array[int] = [1, 2, 3]`
2. `arr(0) = 11`
3. `var x:int = arr(1)`
4. `var arr2: array[int] = [0..10]`
5. `var arr3: array[int] = array(10)`
6. `var twod = array[array[int]] = array(10,10)`
7. `twod(0)(2) = 100`

**Notes**
1. Above example 1 through 3 shows definition and read/write from/to an array.
2. Example 4 shows using range operator `..` to initialize array. More in Operators section.
3. Example 5 and 6 show initializing a 1-D and 2-D array with default values using `array` core function.

## Slice

**Semantices**: Simulate an array which maps to a view into an existing array

**Syntax**: `array(start, end)`

**Examples**

1. `var arr: array[int] = [0..9]`
2. `var slice1: array[int] = arr(1, 2)`
3. `var slice2: array[int] = arr(0, -1)`
4. `slice1(0)++`
5. `slice1 = calculateValues()`

**Notes**

1. Example 2 will give a slice which contains `1,2`.
2. Example 3 will give a slice which contains all elements. End parameter can be negative which is counted from end of the array (`-1` means last element). Same for start.
3. Example 4 will change `arr` array's value at index 1 from 1 to 2.
4. You can use a slice as an lvalue in assignment (Example 5).

## Extended primitives

**Semantics**: Built on top of primitives, these types provide more advanced data types.

**Syntax**: `bool`, `string`

**Examples**

1. `var x: bool = true`
3. `var x: string = 'Hello world!'`

**Notes**

1. All extended primitives are defined using language features and based on primitives and `binary` data type.
2. `string` is defined as an array of `char` data type. The conversion from/to string literals is handled by the compiler.
4. These types are part of `core` package. Please refer to this package documentation for more information about how they work.
5. String literals should be enclosed in double quotes. 
6. String litearls enclosed in backtick can be multi-line and escape character `\` will not be processed in them.

## Tuple
Tuple fields must be named because they are the only way to access their internal data. You can only used unnamed tuple literal when names are specified in the context.
**Semantice**: As a product type, this data type is used to defined a set of coherent variables of different types.

**Syntax**: 
1. For declaration: `{field1: type1, field2: type2, field3: type3, ...}` 
2. For literals: `Type{field1:=value1, field2:=value2, field3:=value3, ...}` 
2. For literals: `{field1:=value1, field2:=value2, field3:=value3, ...}` 
3. For update: `OtherVar{field1:value1, field2:value2, field3:value3, ...}`

**Examples**

1. `point := {x:=100, y:=200}` free tuple litearl (no type associated)
1. `point := Point{x:=100, y:=200}` typed
2. `var point_x: int = point.x`
3. `point.y = point.x + 10`
4. `var another_point = Point{x:=100, y:=200}`
5. `var third_point = Point{200, 400}`
6. `var fourth_point: {x:int, y:int=123} = {300}`

**Notes**

1. Tuple literal does not need to include type name, if the type can be inferred from the context.
2. In examples number 4 and 5, we have used `Point` as a type name. Refer to corresponding section for more information about named types.
3. Compiler will translate tuples to `binary` type with appropriate size.
4. The example number 6, uses default value for `y` field.

- Fields that start with underscore are considered internal state of the tuple and better not to be used outside the module that defines the type. If you do so, compiler will issue a warning.
- You can define a tuple literal using `{}` notation: `var t = {field1=10, field2=20}`.
- If a function expects a specific input type, you can pass a tuple literal, if field order, names and types match.
- If function expects a specific input type and tuple uses unnamed fields, you can use tuple if order and types match.
- You can cast a tuple literal to a specific type. `var g = MyTuple{field=10, field2=20}`
- You can use cast operator to cast a variable initialized with tuple literal to a specific type.


## Composition
`type shapes := union[Shape]` union includes all tuples that embed Shape type.

**Semantics**: To be able to re-use data defined in another tuple.

**Syntax**: `{Parent1Type, field1: type1, field2: type2, Parent2Type, ...}`

**Examples**

1. `type Shape := { id:int }`
2. `type Circle := { Shape, radius: float}`
3. `var my_circle: Circle = {id=100, radius=1.45}`

**Notes**
1. In the above example, `Shape` is the contained type and `Circle` is container type.
2. The language provides pure "contain and delegate" for a limited form of subtyping.
3. A tuple can embed as many other tuples as it wants and forward function calls to itself to functions to other embedded tuples. Refer to function section for more information about forwarding functions.
4. You can define a union type which accepts both `Shape` and `Circle`. It will detect the actual type.
5. To have dynamic polymorphism, you can use `union`: `var result: union[Circle, Square] = createShape()`
6. Note that polymorphism does not apply to generics. So `array[Circle]` cannot substitute `array[Shape]`.
7. We use closed recursion to dispatch function calls. This means if a function call is forwarded from `Circle` to `Shape` and inside that function another second function is called which has candidates for both `Circle` and `Shape` the one for `Shape` will be called.

## Type alias

**Semantics**: To define alternative names for a type

**Syntax**: `type NewName = CurrentName`

**Examples**

1. `type MyInt = int`

**Notes**

1. In the above example, `MyInt` will be exactly same as `int`, without any difference.
2. This can be used in refactoring process or when there is a name conflict between types imported from different modules. See `import` section for more information.

## Label types

**Semantics**: To define types that have only one value: Their name. These types are useful as part of enum or a union.

**Syntax**: `type LabelName`

**Examples**

1. `type true`
2. `type false`
3. `type Saturday, Sunday, Monday`
4. `type Nothing`
4. `var g: Nothing = Nothing`

**Notes**

1. You can define multiple label types at once (Example number 3).

## Named type
You can indicate protocol implementation for a type using `+` notation: `type FileHandle := {handle: int} +Disposable`


**Semantics**: To introduce new, different types based on existing types (called underlying type).

**Syntax**: `type NewType := UnderlyingType`

**Examples**

1. `type MyInt := int`
2. `type IntArray := array[int]`
3. `type Point := {x: int, y: int}`
4. `type bool := union[true, false]`
5. `var x: Point = {10, 20}`
6. `var t1:int = 12`, `var t2: MyInt = t1` - compiler error!


**Notes**

1. There must be a single space between `type` and name.
2. Example number 4, is the standard definition of `bool` extended primitive type based on `union` and label types.
3. In above examples (like example number 1), note that although their binary data representation is the same, `MyInt` and `int` are two separate types. This will affect function dispatch. Please refer to corresponding section for more information.
4. You can use casting operator to convert between a named type and it's underlying type. Please refer to corresponding section.
5. In example number 6, you cannot assign `int` to `MyInt` as they are completely different types.

 


## Variables
- You can declare and assign multiple variables in a single statement:
`var x,y = 1,2`
`var x, val y=1,2`
`var x:int, val y:float = 10, 1.12`

========================================

## Rules



A named type is completely different from it's underlying type and the only similarity is their internal memory representation.
```
type MyInt := int
//in a function
var t: int = 12   //define a variable of type int and named 't'
var y: MyInt = t  //wrong! You have to cast 't'
```

Two variables of same named types, have the same type. Two variables of unnamed types which is structurally similar, have the same type (e.g. `array[int]` vs `array[int]`).

Assignment between different variables is only possible if they have the same type. Otherwise, a casting is needed.
```
var x: int = 12
var y: int = 19
x=y  //valid
```

- If function expects a named type, you cannot pass an equivalent unnamed type. 
- Similarly, when a function expects an unnamed type, you cannot pass a named type with same underlying type. 
- We never do implicit casts like int to float.
- Assigning a value of one named type to variable of a different named type is forbidden, even if the underlying type is the same. 

There are two named types which are called "Extended Primitives" because of their internal role in the lagnuage: `bool` and `string`:
```
type bool := true | false
type string := array[char]
```
`if ( @x == @SAT )` or `if ( x == SAT )`





## Map
To read from map: `x = map(0)` or `x = (0)map`
To update map: `map1 = map1[0=>10, 2=>20]`
define map literal: `map = [0=>1, 1=>2, 2=>4]`
reading from map will return maybe. 
`value := my_map("key1")` normally map query will return a maybe[T]
`value := my_map("key1") >> def(_, 0)` --if does not exist, set value to 0


4. `var my_map: map[string, int] = ["A"=>1, "B"=>2, "C"=>3]`
6. `my_map("A") = 2`

`type map[K,V] := {...}`
```
var m:map[string, int] = ["A" => 1, "B"=>2]
m("C") = 12
```

Maps, hashtables or associative arrays are a data structure to keep a set of keys and their corresponding values.
```
//defining and initializing a map
var y: map[string, int] = ["a": 1, "b": 2]

;read/write from/to a map
y("a") = 100
var t: int = y("b")
```

- If you query a map for something which does not exist, it will return `Nothing`. Below shows two ways to read data from a map:
`var value = my_map("key1") if ( var i, success = @int(value), success )`
`var value = my_map("key1") if ( @value == @int )`
`var value = my_map("key1") if ( value @ int )`
`var t, found = my_map("key1")`






>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><

# Functions
`func process || -> {x:int, y:int} ... { ...return {x:=12, y:=91} }`
- `func process|x:int|->int`
- All functions return something. If they don't, compiler will set their return type to `nothing`.
- Function inputs are immutable. Only local variables can appear on the left side of `=`.
- Note that when I call `process(myIntOrFloat)` with union, it implies that there must be functions for both int and union (or a func with union type).
- explain fwd functions
- We solve expression problem by using open functions.
- If you want dispatch with static type, cast the argument to static type.
If a function expects `x: Stack[Shape]` you cannot send `Stack[Circle]`.

explain relation with subtyping
Example about inheritance, polymorphism and subtyping:
```
type Shape := {}
func Draw(Shape, int)->int

type BasicShape := (x: string)              ; a normal type
func Draw(x: BasicShape, y:int)             ; Now BasicShape is compatible with Shape type

type Circle := (BasicShape, name: string)   ; another type which inherits from BasicShape
func Draw|x: Circle, y:int|                 ; It has it's own impl of Draw which hides Human implementation

type Square := (BasicShape, age: int)       ; another type which embeds BasicShape.
;calling Draw on Square will call BasicShape version

type OtherShape := {}
function Draw(x: OtherShape, y:int)         ; OtherShape also implements Hobby

var all: Shape[] = [myBasicShape, myCircle, mySquare, myOtherShape]
for(Hobby h: all) Draw(h,1,"")
;implicit casting to empty type. Compiler can check if this is a valid casting or no.
;compiler can make sure that all currently defined empty functions which accept Shape
;are defined for MyOtherShape too
var t: Shape = myOtherShape
var r: BasicShape = myCircle ;automatic casting - because Circle inherits from BasicShape
```

- section: function pointer. state it's binding is explicit without dynamic dispatch.
- You can define functions on types and specialize them for special subtypes. This gives polymorphic behavior.
`func paint(o:Shape) {}`
`func paint(o:any){}`
`func paint(o:Circle)...`
`func paint(o:Square)...`


- You can use `_` to ignore a function output: `{t, _} = my_map("key1")`
- A function can only return one output:
```
func process||-> {int, int}
var x:int
val y:int
{x,y} = process
```
- You can define consts using functions: `func PI -> 3.14`
- Storage class: `var/val` of the function output is part of singature (but not output type). And you must capture a functin output.
`process/int/var.int/val.float/val` is a condensed view of the signature of the funtion: `func process(var x:int, val y: float)->val string`.


- There must be a single space between func and function name.
- A function can determine whether is expects var or val inputs. If function wants to promise it won't change the input but caller can send either var or val, it can do so with eliminating qualifier or using val: `func process(x: int)`.
- var/val qualifier is optional for function input/output. if missing, it is considered val and caller can send either val or var. But if it is `val`, caller can only send vals.
- If function output does not have a qualifier, it will be val.
`func process(var x:int) -> x` return is a val
`func process(var x:int) -> int x` return is a val
`func process(var x:int) -> val:int x` return is a val
- If function wants to return a var and use shortcut:
`func process(val x:int) -> var:int x+1`
`func process(val x:int) -> var x+1`
- A function can state it's output var/val. if qualifier is missing, function can return either var or val but caller can only assign the result to a val (unless it is making a copy). But if output is marked with `val`, function can only return a val.
- So missing qualifier: either var or val can be used by sender but receiver should assume val.
- function inputs should have val/var modifier so it won't be ambiguous whether something is potential for shared mutable state. If input modifier is missing, it is assumed to be var or val.
- If function output type misses `var/val` modifier, it will be assumed `val`.
`func add(x:int, y:int)->int`
`func add(val x:int, val y:int)->val:int`
These two definitions are different. var/val are part of function.
Compiler/runtime will handle whether to send a ref or a copy, for val arguments.
- You can omit `()` in function call if there is no local variable or argument with same name and function has no input. If there is local var with same name, compiler will issue warning: `var t:int = sizeof[int]`

function inputs must be named.
- Function output can be any type and any count. Even a tuple or a tuple with unnamed fields.
Function is a piece of code which accepts a series of inputs and can return a single value. 
If you use `{}` for the body, you must specify output type and use return keyword.

```
func my_func1(x: int, y: int) -> float { return x/y }
func my_func1(y:int) -> float { return y/3 } ;you must specify input name
func my_func(y:int, x:int) -> int { return 6+y+x } ;based on runtime arguments, one of implementations will be choosed
func my_func(5:int) -> 9
func my_func3(x: int, y: int) -> x/y  ;you can omit {} if its a single expression
func my_func7() -> int { return 10;} ;fn has no input but () is mandatory
func my_func7() -> 10  ;when function has just a return statement, there is a shortcut
func my_func8() -> (int, int) { return 10,20 } ;function can return multiple values
func myFunc9(x:int) -> {y:int} {y=12} ;you can have named output

 ;below function receives an array + a function and returns a function
func sort(x: int[], comparer: func(int,int) -> bool) -> func(int, bool) {}

func map<T, S>(arr: T[], f: func(T) -> S) -> S[]

;these calls are all the same
new_array = map(my_array, (x) -> x+1)
```
- `map` can work on any type that supports iterable.
```
new_array = map(my_array, (x) -> x+1) ;map will receive a tuple containing two elements: array and lambda
new_array = map(my_array , (x:int) -> {x+1})
```
- Paren is required when calling a function, even if there is no input.
- You can define variadic functions by having an array input as the last input. When user wants to call it, he can provide an array literal with any number of elements needed.
- `rest` is a normal array which is created by compiler for each call to `print` function.
- Functions are not allowed to change (directly or indirectly) any of their inputs.
```
func f(x:int, y:float) -> (a: int, b: string)
{
  ;returning anon-tuple
  return (a=1, b=9) ;or return (1, 9) or return 1,9
}

func read_customer(id:int) -> Nothing | CustomerData
{
  ;no customer was found
  return Nothing
  
  ;some customer found
  return c1
}
```
- You can define a function which does not have a body. This is like an abstract method. So calling it will throw error.
`func adder(x: int, y:int)->int`
- Normally you define general functions as abstract, and speciaize it for specific types in other functions with the same signature.
- Function definition specifies a contract which shows input tuple and output tuple. If input tuple is named, you must pass a set of input or tuple with the exact same name or an unnamed tuple. If input is unnamed, you can pass either unnamed or named tuple.
```
func f(x:int, y:int) -> ...
var g = {x:10, y:12}
f(g) ; this is not correct. f expects x and y not a tuple with another tuple.
f(1,9)
f(g.x, g.y)
```
- Note that you cannot use optional arguments in a function signature. Although you can have multiple functions with the same name:
```
func process(x: int, y:int, z:int) -> ...
func process(x: int) -> process(x, 10, 0)
```
- This is a function that accepts an input of any type and returns any type: `type Function[I,O] := func(I)->O`.
- This is a type for functions that don't return anything: `type NoReturnFunc[T] := func(T)`
- Functions can name their output. In this case, you can assign to it like a local variable and use empty return.
`func process() -> x:int `

## Method call resolution
- We can have dynamic dispatch by using union. When I call `process(intOrFloat)` based on the type inside union, either process for int or the one for float will be called.
- If you don't want to define function with union but still have dynamic dispatch, you will need to write delegation (or forwarding) functions: `func process(float, union[Shape]->Shape, string, int, GradientColor|SolidColor->Color, int)`

- There will be no dynamic type. When you write `var s: Shape = createCircle()` you have only a Shape. Because `=` is supposed to make a data-copy. If you need to support both use union. For example for array storage define array of type `union[Circle, Square]`.
- explain named and underlying type role indispatch
If no function is defined for a named type but for it's underlying type, that one will be called.

Method call is done using full dynamic match. Developer has to define appropriate functions or forwarding functions. This will impose a bit of burden on developer but will simplify compiler, increase method call performance and make code more clear and understandable. No unexpected method call.
To define forwarding function you define a function signature without body, with `-> Target` for the types you want to forward:
You can have multiple forwading in the same definition and use sum type to group multiple functions.
`func process(Polygon|Square|Circle->Shape, GradientColor|SolidColor->Color)`
Above means, any call to `process` with any of `Polygon, Square, Circle` and any of `GradientColor, SolidColor` will be redirected to `process(Shape, Color)`.
You can mix forwarded arguments with normal arguments:
`func process(float, union[Shape]->Shape, string, int, GradientColor|SolidColor->Color, int)`
Note that any argument can only be forwarded to a parent type.
- No forwarding function is automatically generated.
- Suppose that we have `binary -> Shape -> Polygon -> Square` types.
and: `type MyType := Square`
then: `MyType -> MyChild -> MyGrandChild`
then if a variabe of type MyGrandChild is passed to a function.
For static candaidate this is the ordered list: MGC, MC, MT, Square, Polygon, Shape, binary
if for example dynamic type of the variable is `MyGrandChild` and there is `func process(MyGrandChild)` it will be called (note that this can be a fwd function).
It not found, the static candidate will be called.

## Matching
`func add(x:int, y:int, z:int) ...`
`func add(x:int=15, y:int, z:int) ...`
`func add(x:int, y:int, z:int=9)...`
call:
`add(x,y)` will call 3rd version
`add(15, y)` will call 3rd version
`add(15, y, z)` where z is not 9, will call 2nd version
in function definition giving value to a parameters, means it should be exactly equal to that value or it should be missing.
order of match: for (a,b,c) tuple when calling function:
Functions with that name which have 3 or more inputs will be tested.
- Functions with exactly 3 inputs have higher priorirty than those with 3+ inputs with optional values.
- Functions with higher input value equal to values of a, b, c have higher priority.
So, first search for funcciont with 3 inputs then 4 input with last one optional, then 5 inputs ...
In each case, first check functions that have all 3 pre-set, then 2 pre-set then 1-preset then no pre-set.
If in each step, two cancidates are found, give error.
For example:
`func add(x:int=9, y:int)`
`func add(x:int, y:10)`
calling `add(9, 10)` will result in two candidates -> runtime error.
if input is unnamed then ok. If it is named, we have an extra condition: input names must match.
and `(a=15, x=10, y=20)` will match `(x:int)` which is foundation of subtyping.
With named inputs, you can ignore first or middle arguments: 
```
func add(x:int=10, y:int)...
add(y=19)
```
Each function call will be dispatched to the implementation with highest priority according to matching rules. 

## Lambda expression
- If a lambda needs varirables in the parent function, it should capture them first by assigning them to local variables:
```
x = 12
|| -> { y := x, process(y) }
```
they cannot be re-assigned. Compiler will detect this and issue error if they are re-assigned. This is to prevent possible data race in which case, a data is modified outside a thread (which is the closure) while the code inside the thread is reading it. Use channels to communicate between threads.
A function type should not include parameter name because they are irrelevant.
A lambda variable can omit types because they can be inferred: `var x: comparer = |x,y| -> ...`
A function literal which does not have a type in the code, must include argument name and type. `|x:int|->int { return x+1 }(10)` or `var fp = |x:int|->int { return x+1}`

- closure capturing: It captures outside vars and vals. Can change vars.
- Even if a lambda has no input/output you should write other parts: `() -> { printf("Hello world" }`
You can define a lambda expression or a function literal in your code. Syntax is similar to function declaration but you can omit output type (it will be deduced from the code), and if type of expression is specified, you can omit inputs too, also  `func` keyword is not needed. The essential part is input and `->`.
If you use `{}` for the body, you must specify output type and use return keyword.
```
var f1 = |x: int, y:int| -> int { return x+y } ;the most complete definition
var rr = (x: int, y:int) -> x + y  ;return type can be inferred
var rr = { x + y } ;WRONG! - input is not specified
var f1 = (x: int, y:int) -> int { return x+y } ;the most complete definition

type adder := (x: int, val y:int) -> var:int
var rr: adder = (a:int, b:int) -> { a + b } ;when you have a type, you can define new names for input
var rr: adder = (x,y) -> x + y   ;when you have a type, you can also omit input
var rr: adder = (x,y) -> int { return x + y }      ;and also func keyword, but {} is mandatory
var rr:adder = (x,y) -> x + 2      
func test(x:int) -> plus2 { return (y) -> y+ x }
var modifier = (x:int, y:int) -> x+y  ;if input/output types can be deduced, you can eliminate them
```
- Lambdas have read-only access to free variables in their parent semantic scope.
- Function pointers cannot take part in method dispatch. They must point to a specific function. This is specified using their type. 
- Another way to forward a call to another function but without loosing dynamic type:
```
func process(c: Circle) -> int {
;you cannot infer type from a function name, unless there is only one function with that name
 var f: func(s: Shape) = process 
 var g = process ;this is wrong
 return f(c)
}
```

`var g: func(x:int)->int...`
`var g: func[T](x:T)->T...`
`var g: func[T:Stringer](x:T)->T...`
`g("A") g(2) g(1.2)`
- You can use `_` as a shortcut to define a lambda:
If we have `func f(int,int,int)->int` then:
`var t = f(a1, a2, _)` is same as `var t: func(int)->int = (x:int) -> f(a1, a2, x)`
- Lambdas can also use protocols in their type or their value definition.

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><

# Generics
- Compiler will scan body of generic functions and extract their expected methods. If you call them with inappropriate types, it will give you list of required methods to implement.
- Note that a generic function can only have generic types. About immutability or mutability, it cannot be generic. The function signature is responsible about defining whether an argument should be immutable or mutable or doesn't care. Also same for types. You cannot have mutability or immutability as a generic argument.
- Generic arguments can only be types.
- When defining types, you can append `[A, B, C, ...]` to the type name to indicate it is a generic type. You can then use these symbols inside type definition.
- When defining functions, if input or output are of generic type, you must append `[A,B,C,...]` to the function name to match required generic types for input/output. 
- When you define a variable or another type, you can refer to a generic type using it's name and concrete values for their types. Like `Type{int, string]`
- Generic functions, must make use of their type argument in their input.
```
type Map[K,V] := K => V
type Stack[T] := array[T]  ;define base type for generic type
func push[T](s: Stack[T], x: T)
func push[int](s: Stack[int], x: int) ;specialization
func pop[T](s: Stack[T]) -> T
func len[T](s: Stack[T]) -> int   ;general function for all instances
var t : Stack[int]
var h : Map[int, string]
push(t, 10) ;same as push[int](t, 10)
var y = pop(t)
x = len(t)
```
`type optional[T] := Nothing | T`
`type Packet[T] :=   {status: T[], result: (x:int, y:int))`
`type IPPacket := Packet[int]`
`type Tree[T] := {x: T, left: Tree[T], right: Tree[T]}`
`type ShapeTree := Tree[Shape]`
Example:
`func push[T](x: Stack[T], y: T)`
`func push(x: Stack[int], y:int)`
if we call `push(a,6)` and `a` is `Stack[int]` second function will be called because there is full match.
if we call `stack[int](a, b)` still the second one will be called.
- When calling a generic function, you can omit type specifier only if it can be deduced from input. If not, you must specify input.
Example: `func process[T](x: int) -> T`
`process(10)` is wrong. You must specify type: `var g: string = process[string](10)`
- If some types cannot be inferred from function input, you must specify them when calling the generic function.
```
type DepValue[T] := (value:T)
func magic[T](that: DepValue[T])->T that.value
var x = %DepValue[int](1) ;x is int
var y = %DepValue[string]("a") ;y is string
var xx: int = magic(x)
var yy: string = magic(y)
```

For generic functions, any call to a function which does not rely on the generic type, will be checked by compiler even if there is no call to that generic function. Any call to another function relying on generic argument, will be checked by compiler to be defined.

## Phantom types
Phantom are compile-time label/state attached to a type. You can use these labels to do some compile-time checks and validations. Here labels are implemented using generic types which are not used for data allocation. For example we have a string which is result of md5 hash and another for sha-1. We should not be comparing these two although they are both strings. So how can we mark them?

```
type HashType := MD5 | SHA1
 ;when generic type is not used on the right side, it will be only for compile time check
type HashStr[T] := string     
type Md5Hash := HashStr[MD5] 
;Md5Hash type can be easily cast to string, but if in the code a string
;is expected to be of type Sha1Hash you cannot pass Md5Hash
type Sha1Hash := HashStr[SHA1]
func md5(s: string)->Md5Hash {
    var result: string = "ddsadsadsad"
    return %Md5Hash(result)  ;create a new string of type md5-hash
}
func sha1(s: string)->Sha1Hash
var t: Md5Hash  = sha1("A")  ;will give compiler error because output of sha1 is Sha1Hash
func testMd5(s: string, t: Md5Hash) -> md5(s) == t

;if there is only one case, you can simply use named type
type SafeString := string
func processString(s: string)->SafeString
func work(s: SafeString)

;another example: expressions
type ExpType := INT | STR
type Expression[T] := (token: string)
func readIntExpression(...) -> Expression[INT]
func plus(left: Expression[INT], right: Expression[INT])...
func concat(left: Expression[STR], right: Expression[STR])...

;door
type DoorState := Open | Closed
type Door[T] := (string)
func closeDoor(x: Door[Open]) -> Door[Closed]
func openDoor(x: Door[Closed]) -> Door[Open]
```

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><

# Notations

## Operators
- Conditional: `and or not == != >= <= => =<`

- Math: `+ - * % %% (is divisible) **`
- There is no `++` and `--` operators.
- The math operators can be combined with `=` to do the calculation and assignment in one statement.
- `=` operator: copies data.
- `:=` opreator will make left side point to right-side variable or result of evaluation of the right-side expression.
- `x == y` will compare two variables field by field.
- We don't have operators for bitwise operations. They are covered in core functions. 
- Assignment semantics: `x=y` will duplicate contents of y into x (same as `*x=*y` in C++). So if rvalue is a temp variable (e.g. `x=1+y`), it will be a ref-assign handled by the compiler. If you want to ref-assign you should use `:=` notation. Note that for assignment lvalue and rvalue must be of the same type. You cannot assign circle to Shape because as copy is done, data will be lost (you can refer to `.Shape` field in the Circle).
`x=y` will duplicate y into x. So changes on x won't affect y. 
- Comparison semantics: `x==y` will compare data.
- Type-id (`@`): returns type-id of a named or primitive type: `@int`
- Type-check `@` as binary operator (left side is a union and right side is a type), returns true if type matches.
- To cast from named to unnamed type you can use: `Type{value}` notation: `y = int{x}`
- For union: `x=union[int,float]{12}`
- chaining: 
`data >> function` will return `function(data)` if it is applicable (functin can accept that data) else data.
To provide default case in map lookup: `x := map1["A"] >> def(_, 5)`
`def` function in core will return second argument if first one is nothing. if `map["A"]` is not nothing, the expression will evaluate to `map["A"]`

### Special Syntax
- `@` type-id, type-check
- `>>` chaining
- `_`  placeholder for lambda or unknown variable
- `:`  declaration for tuple, tuple literal and type filter
- `:=` custom type definition, variable declaration, tuple literal
- `=`  type alias, copy value
- `=>` map literals and block-if
- `..` range generator
- `->` function declaration
- `<-` loop
- `[]` generics, array and map modification and literals
- `{}` code block, tuple definition and tuple literal
- `()` function call, read from array and map
- `||` function and lambda declaration
- `.`  access tuple fields

Keywords: `import`, `func`, `type`, `if`, `then`, `else`, `loop`, `do`
Primitive data types: `int`, `float`, `char`, `union`, `array`, `map`
Pre-defined types: `bool`, `string`, `nothing`
Important concepts: ExclusiveResource

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><

### block-if, match
We use map literals to do block-if.
```
y = (x)[
    1 => "G",
    2 => "H",
    3 => "N",
] >> def(_, "default")
```
To type match for a union:
```
y = ( type(x) )
[
    @int => "G" + int{x},
    @string => "H",
] >> def(_, "X")
```
In this case, the value of the map for that key is evaluated and if result is not nothing, it will be stored on the lvalue.

###if, else
- You can use `if/then/else` block as an expression.
- If/else are keywords so their blocks can freely access and re-assign local variables.
- You can use if/else as an expression: `a=if cond then 1 else 2`
```
IfElse = 'if' '(' condition ')' then Block ['else' (IfElse | Block)]
Block  = Statement | '{' (Statement)* '}'
```
- Semantics of this keywords are same as other mainstream languages.
- If block is a single statement in one line, you dont need braces.
- Note that condition must be a boolean expression.
- You can use any of available operators for condition part. 
- Also you can use a simple boolean variable (or a function with output of boolean) for condition.
`var max = if x > y then x else y`

```
  if exp1 and exp2 then 11 else -1
```
- `a=xyz if(cond)` is also possible.
`a=(cond)[true=>xyz, false=>a]`
- But if `if` is used as a suffix to a statement, it won't be translated to map lookup:
`return 1 if x`

### Exclusive resource
 every tuple that embeds `ExclusiveResource` is treated like an exclusive resource.
If some data type represents a resource which needs to be handled only by one function or thread at time, it's type must embed with `ExclusiveResource` (for example file handle, db connection, network socket, ...). These types are not supposed to be shared because of their inherent mutability. This protocol has a single `dispose` function to release the resource.
These types have some properties which are enforced by the compiler:
1. Any function which creates them, has to either call dispose on them or pass them to another function.
2. Any function that has an input of their type, must either call dispose or pass it to another function.
3. Any use of them after being passed to another function is forbidden.
4. Closures cannot capture them (but you can pass resources to them).
```
type FileHandle := {ExclusiveResource, handle: int}
func closeFile(x:FileHandle)->bool { ... }
f = openFile(...) 
...
closeFile(f)
```
- If the resource is part of a union, there must be appropriate `dispose` function for other types in the union, so that a call to `dispose` on that union will be guaranteed to work.

### assert (removed)
- `Exception` is a simple tuple defined in core. 
- You can use suffix if for assertion: `return xyz if not (str.length>0)`
- To handle exceptions in a code in rare cases (calling a plugin or another thread), you can use `invoke` core function.
`func invoke[I,O](f: func, input: I)->O|Exception`. If your function has more than one input, you should define a wrapper function or a closure which has one input of type tuple.
`var finalResult: Maybe[int] = input >> check1(5, _) >> check2(_, "A") >> check3(1,2,_)`

- **Nothing**: Nothing is a label type with only one value: `nothing`.
 You can use it's type for return value of a function. If a function does not return anything, it returns `nothing`.

## import

This can be used to resolve conflict types when importing modules.
`type Stack1 = /core/mode1/Stack`
`type Stack2 = /code/mode2/Stack`
`type S[T] = Stack[T]`
`type ST = Stack[int]`

- Exaplein how type alias can be used.
- There must be a single space between `import` keyword and it's contents.
You can import a source code file using below statement. Note that import, will add symbols (functions and types) inside that source code to the current symbol table:
- You can only import one module in each import statement (No wildcard).

```
;Starting a path with slash means its absolute path (relative to include path). Otherwise it is relative to the current file
import /core/st/Socket  ;functions and types inside core/st/Socket.e file are imported and available for call/use
import /core/st/Socket/ ;if you add slash at the end, it means import symbols using fully qualified name. This is used for refering to the functions using fully qualified names. Functions imported with this method won't be used in method dispatch mechanism.
```
It is an error if as a result of imports, there are two exactly similar functions (same name, input and output). In this case, none of conflicting functions will be available for call. 
The paths in import statement are relative to the runtime path specified for libraries.
In case of conflicting function names, you can get a function point to another function in another module without importing it.
`func myFunc(x:int) -> /core/pack2/myFunction;`
So when `myFunc` is called, it will call another function with name `myFunction` located in `/core/pack2` source file.
Note that you must have imported the module before.
Also you can call a function or refer to a type with fully qualified name:
`var x: int = /core/pack2/myFunction(10, 20);`
`var t: /core/pack2/myStruct;`
- By default, `import` works on local file system but you can work on other types too:
`import /a/b` import from local file system
`import file:/a/b` import from local file system
`import git:/github.com/adsad/dsada` import from github
`import /core/std/{ab, cd, ef}` to import multiple modules

### native
`type array[T] := {...}`

Denotes function is implemented by runtime or external libraries.
`func file_open(path: string) -> File {...}`
`type Test := {...}`

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>><


## Examples
### Empty application
```
func main() -> 
{
    return 0 
}
```
or even simper: `func main() -> 0`

This is a function, called `main` which returns `0` (very similar to C/C++ except `main` function has no input).

### Hello world
### Quick sort
### Graph class
### Expression parser
We want to write a function which accepts a string like "2+4/3" and returns result (2)
```
type Expression := int | (op: char, left: Expression, right: Expression)
func eval(input: string) -> float 
{
  var exp: Expression = parse(input)
  return innerEval(exp);
}
func innerEval(exp: Expression) -> float 
{
  return exp @
  {
    x:int -> x,
    (op: char, left: Expression, right: Expression) -> op @
    {
      '+' -> innerEval(left) + innerEval(right),
      '-' -> innerEval(left) - innerEval(right),
      '*' -> innerEval(left) * innerEval(right),
      '/' -> innerEval(left) / innerEval(right),
    }
  }
}
```

## Core package

A set of core packages will be included in the language which provide basic and low-level functionality (This part may be written in C):

- Security policy (how to call a code you don't trust)
- Calling C/C++ methods
- Interacting with the OS
- Load code on the fly and hot swap
- Data conversion
- Garbage collector (Runtime)
- Serialization and Deserialization
- Dump an object
- RegEx operators and functions
- Cast binary to unsigned number

Generally, anything that cannot be written in atomlang will be placed in this package.


### loop
This is a keyword:
`loop var <- exp do block`
var must not declared before. it will be declared here and only valid inside loop block.

```
loop x>0 do printf(x)
loop true do ...
loop x <- [2..10] do printf("Hello world")
loop item <- my_array do printf(item)
loop g <- my_iterable do ...
loop {x,y} <- [2..10], [1..9] do printf("Hello world " +x +y)
```
You can also use iterator type with loop:
```
type Iterator[T] := {...}
iterator := getIterator(myBitSet)
loop g <- iterator do ...
```
there is no break or continue. You should implement them as condition inside loop block or inside loop exp.
- If expression inside loop evaluates to a value, `loop` can be used as an expression:
??? `var t:int[] = loop(var x <- {0..10}) x` or simply `var t:int[] = loop({0..10})` because a loop without body will evaluate to the counter, same as `var t:array[int] = {0..10}`

## Standard package

There will be another set of packages built on top of core which provide common utilities. This will be much larger and more complex than core, so it will be independent of the core and language (This part will be written in dotLang). Here is a list of some of classes in this package collection:

- I/O (Network, Console, File, ...)
- Thread and synchronization management
- Serialization/Deserialization
- Functional programming: map/reduce/filter
- String and Regex
- Collections (Stack, Queue, Linked List, ...)
- Encryption
- Math
- Bitwise operators (and, or, shift, xor, ...)
- Methods to help work with natively mutable data structures and algorithms (sort, tree, ...)
- ...

## Package Manager

The package manager is a separate utility which helps you package, publish, install and deploy packages (Like `maven` or `dub`).
Suppose someone downloads the source code for a project written in dotLang which has some dependencies. How is he going to compile/run the project? There should be an easy and transparent for fetching dependencies at runtime and defining them at the time of development.

Perl has a `MakeFile.PL` where you specify metadata about your package, requirements + their version, test requirements and packaging options.
Python uses same approach with a `setup.py` file containing similar data like Perl.
Java without maven has a packaging but not a dependency management system. For dep, you create a `pom.xml` file and describe requirements + their version. 
C# has dll method which is contains byte-code of the source package. DLL has a version metadata but no dep management. For dep it has NuGet.

## ToDo
- Add native concurrency and communication tools (green thread, channels, spinlock, STM, mutex) and async i/o, 
- Introduce caching of function output
- Build, versioning, packaging and distribution
- Dependency definition and management 
- Plugin system to load/unload libraries at runtime
- Debugger and plugins for Editors
- Atomic operations, mutex and other locking features.
- Testing facilities
- Define a notation to access a location inside a binary and sizeof function
- Actor/Message passing helpers for concurrency.
- Helper functions to work with binary (memcpy, memmove, ...)
- Details of inline assembly flags and their values (OS, CPU, ...)
- Distributed processing: Moving code to another machine and running there (Actor model + channel)
- Define notation to write low-level (Assembly or IR) code in a function body and also force inline.
- Function to get dynamic type of a tuple variable
- Add notation for axioms and related operators like `=>` to protocol to be able to define semantics of a protocol.
- Vet to format code based on the standard (indentation, spacing, warning about namings, ...). And force it before compilation.
