# Kotlin
## Types
### Basic types
#### q
### Type checks and casts
#### q
## Control flow
### Conditions and loops
#### q
### Returns and jumps
#### q
### Exceptions
#### q
Packages and imports
#### q
## Classes and objects
### Classes
#### >>>1. What is a primary constructor in Kotlin? +
A class in Kotlin can have a primary constructor and one or more secondary constructors. The primary constructor is a part of the class header, and it goes after the class name and optional type parameters.

```kotlin
class Person constructor(firstName: String) { /*...*/ }
```

#### >>>2. List all members of a class +
- Constructors and initializer blocks
- Functions
- Properties
- Nested and inner classes
- Object declarations

#### >>>3. Is it possible to declare a property of a class inside a secondary constructor? ++
```kotlin
class Student (var name: String) {

    init {
        println("Student has got a name as $name")
    }

    constructor(name: String, var id: Int) :this(name) {
    }
}
```
The property of the class can’t be declared inside the secondary constructor.
This will give an error because here we are declaring a property `id` of the class in the secondary constructor, which is not allowed.
If you want to use some property inside the secondary constructor, then declare the property inside the class and use it in the `secondary constructor`:
```kotlin
class Student(var name: String) {

    var id: Int = -1

    init {
        println("Student has got a name as $name")
    }

    constructor(name: String, id: Int) : this(name) {
        this.id = id
    }
}
```
#### q
### Inheritance
#### q
### Properties
#### >>>1. What is the difference between var and val in Kotlin? +
Properties in Kotlin classes can be declared either as mutable, using the `var` keyword, or as read-only, using the `val` keyword.
```kotlin
var a = 10
a = 11 //works as expected
...
val b = 10
b = 11 //cannot reassign, as expected
```

#### >>>2. What is Lateinit in Kotlin and when would you use it?
Normally, properties declared as having a non-null type must be initialized in the constructor.
However, it is often the case that doing so is not convenient. 
For example, properties can be initialized through dependency injection, or in the setup method of a unit test. 
In these cases, you cannot supply a non-null initializer in the constructor, 
but you still want to avoid null checks when referencing the property inside the body of a class.
To handle such cases, you can mark the property with the `lateinit` modifier:
```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()  // dereference directly
    }
}
```
This modifier can be used on `var` properties declared inside the body of a class 
(not in the primary constructor, and only when the property does not have a custom getter or setter), 
as well as for top-level properties and local variables. The type of the property or variable must be non-null, and it must not be a primitive type.
```kotlin
lateinit var A: String

class Test(val t: String) {

   constructor(t: String, k: Int) : this(t) {
       //it is local var
       lateinit var B: String
   }

   lateinit var C: String
   
   lateinit var D: Int // ERROR: 'lateinit' modifier is not allowed on properties of primitive types

   fun perform() {
       lateinit var E: String
       if (this::C.isInitialized) {
           println(this.C)
       }
   }
   
   companion object {
       lateinit var F: String
   }
}

```

'lateinit' modifier is not allowed on abstract properties
```kotlin
abstract class Example {
    abstract lateinit var name: Any //'lateinit' modifier is not allowed on abstract properties
}
```
Accessing a `lateinit` property before it has been initialized throws a special exception that clearly identifies the property being accessed and the fact that it hasn't been initialized.

#### q
### Interfaces
#### >>>1. Is there a back field in the properties in the interface? ++ 
You can declare properties in interfaces. A property declared in an interface can either be abstract or provide implementations for accessors (`get()` and `set()`).
Properties declared in interfaces can't have backing fields, and therefore accessors declared in interfaces can't reference them. 
It is also important to understand that the `back field` contains the state of the object. And the interface has no state.
Therefore, interface properties do not have a `back field`

#### >>>2. Please explain why there is an error in this code?? ++ 
```kotlin
interface MyInterface {
    val prop: Int // abstract

    var propertyWithImplementation: String //ERROR: Property in an interface cannot have a backing field
        get() = "foo"

    fun foo() {
        print(prop)
    }
}
```
If you specify only a `get()` for a mutable property in interface, Kotlin also generates a (default) `set()` for the property and you can see a error `Property in an interface cannot have a backing field`. 
In a default implementation of the `set()` method, a `back field` is used. Which is not possible in the interface. 
To fix this, you need to use `val` or implement a `set` method


#### q
### Functional (SAM) interfaces
#### q
### Visibility modifiers
#### q
### Extensions
#### q
### Data classes
#### >>>1. What is a data class in Kotlin? +
It is not unusual to create classes whose main purpose is to hold data. In such classes, some standard functionality and some utility functions are often mechanically derivable from the data. In Kotlin, these are called data classes and are marked with data:
```kotlin
data class User(val name: String, val age: Int)
```

The compiler automatically derives the following members from all properties declared in the primary constructor:
 - `equals()`/ `hashCode()` pair
 - `toString()` of the form `"User(name=John, age=42)"`
 - `componentN()` functions corresponding to the properties in their order of declaration.
 - `copy()` function.
 
To ensure consistency and meaningful behavior of the generated code, data classes have to fulfill the following requirements:
 - The primary constructor needs to have at least one parameter.
 - All primary constructor parameters need to be marked as val or var.
 - Data classes cannot be abstract, open, sealed, or inner.
 

#### >>>2. How to create empty constructor for data class in Kotlin? +++
 - Assign a default value to each primary constructor parameter:
 ```kotlin
 data class Example(
    var updatedOn: String = "",
    var tags: List<String> = emptyList(),
    var description: String = "",
)

...

val example = Example()
val newExample = example.copy(description = "test")
```

- Make values nullable by adding `?` and then assing `null`:
```kotlin
data class Example(
    var updatedOn: String? = null,
    var tags: List<String>? = null,
    var description: String? = null,
)
 ```
 
- Declare a `secondary` constructor that has no parameters:
```kotlin
data class Example(
    var updatedOn: String = "",
    var tags: List<String> = emptyList(),
    var description: String = "") {

    constructor() : this("", emptyList(), "")
}
```
 
- `No-arg compiler plugin`. The `no-arg compiler plugin` generates an additional zero-argument constructor for classes with a specific annotation. [More info](https://kotlinlang.org/docs/no-arg-plugin.html)
```Groovy
plugins {
  id "org.jetbrains.kotlin.plugin.noarg" version "1.6.0"
}
```


 
 
#### q
### Sealed classes
#### q
### Generics: in, out, where
#### q
### Nested and inner classes
#### q
### Enum classes
#### q
### Inline classes
#### q
### Object expressions and declarations
#### >>> 1. How to create singleton in Kotlin? +
Just use `object`. 
```kotlin
object SomeSingleton
```
The above Kotlin object will be compiled to the following equivalent Java code:

```java
public final class SomeSingleton {
   public static final SomeSingleton INSTANCE;

   private SomeSingleton() {
      INSTANCE = (SomeSingleton)this;
      System.out.println("init complete");
   }

   static {
      new SomeSingleton();
   }
}
```
This is the preferred way to implement singletons on a JVM because it enables thread-safe lazy initialization without having to rely on a locking algorithm like the complex double-checked locking.
A `static` block is a set of instructions that is run only once when a class is loaded into memory. The `class loader` is responsible for loading classes into memory. Loading and initializing classes is a thread-safe operation, the `class loader` itself takes care of this.

#### q
### Delegation
#### q
### Delegated properties
#### q
### Type aliases
#### q

## Functions
### Functions
#### q
### Lambdas
#### q
### Inline functions
#### q
### Operator overloading
#### q
### Builders
#### Type-safe builders
#### q
#### Using builders with builder type inference
#### q
## Null safety
#### >>>1. Explain the Null safety in Kotlin + 
Kotlin's type system is aimed at eliminating the danger of null references, also known as The Billion Dollar Mistake. One of the most common pitfalls in many programming languages, including Java, is that accessing a member of a null reference will result in a null reference exception. In Java this would be the equivalent of a `NullPointerException`, or an `NPE` for short.
In Kotlin, the type system distinguishes between references that can hold `null` (nullable references) and those that cannot (non-null references). For example, a regular variable of type `String` cannot hold `null`:
```kotlin
var a: String = "abc" // regular initialization means non-null by default
a = null // compilation error
```
To allow nulls, you can declare a variable as a nullable string by writing `String?`:
```kotlin
var b: String? = "abc" // can be set to null
b = null // ok
print(b)
```
Now, if you call a method or access a property on `a`, it's guaranteed not to cause an NPE, so you can safely say:
```kotlin
val l = a.length
```
But if you want to access the same property on `b`, that would not be safe, and the compiler reports an error:
```kotlin
val l = b.length // error: variable 'b' can be null
```



## Equality
## This expressions
## Asynchronous programming techniques
## Coroutines
## Annotations
## Destructuring declarations
## Reflection
# Collections
#### >>>1. Explain the difference between IntArray and Array<Int>
`Array<Int>` is an `Integer[]` under the hood, while `IntArray` is an `int[]`. This means that when you put an `Int` in an `Array<Int>`, it will always be boxed (specifically, with an `Integer.valueOf()` call). 
In the case of `IntArray`, no boxing will occur, because it translates to a Java primitive array.
