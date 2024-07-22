# 2. Kotlin basics

## 2.1 Basic Elements: Function and variable

### 2.1.1 Writing your first kotlin program: "Hello world!"

```kotlin
fun main() {
    println("Hello World!")
}
```

### 2.1.2 Declaring functions with parameters and return values

```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}

fun main() {
    println(max(1, 2))
    // 2
}
```

- What is an expression?→an expression has a value, which can be used as part of another expression,
- What is an statement?→a statement is always a top-level element in its enclosing block and doesn’t have its own value.

### 2.1.3 Making function definitions more concise by using expression bodies

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

- What is a block body→a function is written in curly braces
- What is expression body→a function returns an expression directly2.1.4 Declaring variables to store data

### 2.1.4 Declaring variable to store data

A variable declaration in Kotlin starts with a keyword (`val` or `var`)

```kotlin
val question: String = "The Ultimate Question of Life, the Universe, and Everything"
val answer: Int = 42

// Omit the type declaration
val question = "The Ultimate Question of Life, the Universe, and Everything"
val answer = 42

val yearsToCompute = 7.5e6 // Double


```

### 2.1.5 Marking a variable as read only or reassignable

- val declares a read-only reference and can be only assigned once. 
- var declares re-assignable reference.

### 2.1.6 Easier string formatting: String templates

```kotlin
fun main() {
    val input = readln()
    val name = if (input.isNotBlank() ) input else "Kotlin"
    println("Hello, $name!")
}

// with curly braces, we could put expression 
fun main() {
    val name = readln()
    if (name.isNotBlank()) {
        println("Hello, ${name.length}-letter person!")     ❶
    }
    // Blank input: (no output)”  
```

## 2.2 Encapsulating behavior and data: Classes and properties

```kotlin
// Java
public class Person {
    private final String name;
 
    public Person(String name) {
        this.name = name;
    }
 
    public String getName() {
        return name;
    }
}
```

```kotlin
class Person(val name: String)
```

### 2.2.1 Associating data with a class and making it accessible: Properties

```kotlin
class Person(
    val name: String, // read only, generate a field and trival getter
    var isStudent: Boolean // a field, getter, setter
)
```

```kotlin
// Use Person.kt in Java class
public class Demo {
    public static void main(String[] args) {
        Person person = new Person("Bob", true);
        System.out.println(person.getName());
        // Bob
        System.out.println(person.isStudent());
        // true
        person.setStudent(false); // Graduation!
        System.out.println(person.isStudent());
        // false
    }
}
```

```kotlin
// User Person class from Kotlin
fun main() {
    val person = Person("Bob", true)          
    println(person.name)                      
    // Bob
    println(person.isStudent) )               
    // true
    person.isStudent = false // Graduation!   
    println(person.isStudent)
    // false
}
```



### 2.2.2 Computing properties instead of storing their values: Custom accessors

```kotlin
class Rectangle(val: height: Int, val width: Int) {
    val isSquare: Boolean
    get() { // property getter declaration
        return height == width   
    }
    // val isSquare get() = height == width
}
```

### 2.2.3 Kotlin source code layout: Directories and packages

- Kotlin uses the concept of packages to organize classes. 
- Every Kotlin file can have a package statement at the beginning, and all declarations (classes, functions, and properties) defined in the file will be placed in that package

```kotlin
package geometry.shapes

class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() = height == width  
} 

fun createUniteSquare(): Rectangle {
    return Rectangle(1, 1)
} 
```

```kotlin
// They need to be imported if they’re in a different package
package geometry.example
 
import geometry.shapes.Rectangle          
import geometry.shapes.createUnitSquare  
// import geometry.shapes.* 
 
fun main() {
    println(Rectangle(3, 4).isSquare)
    // false
    println(createUnitSquare().isSquare)
    // true
}
```



## 2.3 Representing and handling choices: Enums and when

### 2.3.1 Declaring enum classes and enum constants

```kotlin
package ch02.colors
enum class Color {
RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
} 
```

- enum is a soft keyword. It has a special meaning when it comes before class. 
```kotlin
package ch02.colors

enum class Color(
    val r: Int,
    val g: Int,
    val b: Int
) {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238);

    fun rgb() = (r * 256 + g) * 256 + b
}

fun main() {
    println(Color.BLUE.rgb())
}
```
If you define any methods in the enum class, the semicolon separates the enum constant list from the method definitions. 

### 2.3.2 Using the when expression to deal with enum classes

```kotlin
fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }

fun main() {
    println(getMnemonic(Color.BLUE))
}.
```

```kotlin
fun measureColor() = Color.ORANGE
// as a stand-in for more complex measurement logic

fun getWarmthFromSensor(): String {
    val color = measureColor()
    return when(color) {
        Color.RED, Color.ORANGE, Color.YELLOW -> "warm (red = ${color.r})"
        Color.GREEN -> "neutral (green = ${color.g})"
        Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold (blue = ${color.b})"
    }
}

fun main() {
    println(getWarmthFromSensor())
}
```

.
```kotlin
// simplying the code with import 
import ch02.colors.Color.*

fun measureColor() = ORANGE

fun getWarmthFromSensor(): String {
    val color = measureColor()
    return when (color) {
        RED, ORANGE, YELLOW ->
            "warm (red = ${color.r})"
        GREEN ->
            "neutral (green = ${color.g})"
        BLUE, INDIGO, VIOLET ->
            "cold (blue = ${color.b})"
    }
}

fun main() {
    println(getWarmthFromSensor())
}
```

### 2.3.3 Capturing the subject of a when expression in a variable

```kotlin
// Assigning the subject of a when to a variable
import ch02.colors.Color.*                                   
 
fun measureColor() = ORANGE
 
fun getWarmthFromSensor() =
    when (val color = measureColor()) {                      
        RED, ORANGE, YELLOW -> "warm (red = ${color.r})"     
        GREEN -> "neutral (green = ${color.g})"
        BLUE, INDIGO, VIOLET -> "cold (blue = ${color.b}
```

### 2.3.4 Using the when expression with arbitrary objects

`when` is more flexible as you can use any kind of object as a branch condition.

```kotlin
import ch02.colors.Color
import ch02.colors.Color.*

fun mix(c1: Color, c2: Color) =
        when (setOf(c1, c2)) {
            setOf(RED, YELLOW) -> ORANGE
            setOf(YELLOW, BLUE) -> GREEN
            setOf(BLUE, VIOLET) -> INDIGO
            else -> throw Exception("Dirty color")
        }

fun main() {
    println(mix(BLUE, YELLOW))
}
```

### 2.3.5 Using the when expression without an argument

```kotlin
// Better performance but less readable
import ch02.colors.Color
import ch02.colors.Color.*

fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLOW && c2 == RED) ->
            ORANGE

        (c1 == YELLOW && c2 == BLUE) ||
        (c1 == BLUE && c2 == YELLOW) ->
            GREEN

        (c1 == BLUE && c2 == VIOLET) ||
        (c1 == VIOLET && c2 == BLUE) ->
            INDIGO

        else -> throw Exception("Dirty color")
    }

fun main() {
    println(mixOptimized(BLUE, YELLOW))
}
```

### 2.3.6 Smart casts: Combining type checks and casts

In Kotlin, you check whether a variable is of a certain type by using an `is` check..

```kotlin
package ch02.ex3_6_SmartCasts

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int {
    if (e is Num) { // redundant 
        val n = e as Num // explicit cast
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left) 
        // The variable e is smart cast
        // The IDE highlights smart casts with a background color
    }
    throw IllegalArgumentException("Unknown expression")
}

fun main() {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
}
```

### 2.3.7 Refactoring: Replacing an if with a when expression

```kotlin
fun eval(e: Expr): Int =
    if (e is Num) e.value
    else if (e is Sum) eval(e.right) + eval(e.left)
    else throw IllegalArgumentException("Unknown expression")
```

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value                         
        is Sum -> eval(e.right) + eval(e.left)     
        else -> throw IllegalArgumentException("Unknown expr")
      }
```

### 2.3.8 Blocks as branches of if and when 

Both `if` and `when` can have blocks as branches. The last expression in the block is the result. 

We could add logging before the last expression.

## 2.4 Iterating over things: while and for loops

Iteration in Kotlin is very similar to Java, C# and other language.

### 2.4.1 Repeating code while a condition is true: The while loop

while and do-while

```kotlin
while (condition) {
    if (shouldExit) break
}

do {
    if (shouldSkip) continue
    
} while (condition)
```

We could specify a label for the while loop for nested loops with \@

```kotlin
outer@ while (outerCondition) {
    while (innerCondition) {
        if (shouldExitInner) break
        if (shouldSkipInner) continue
        if (shouldExit) break@outer
        // ...    
    }
}
```

### 2.4.2 Iterating over numbers: Ranges and progressions

```kotlin
val oneToTen = 1..10 // inclusive, closed

fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz"
    i % 3 == 0 -> "Fizz"
    i % 5 == 0 -> "Buzz"
    else -> "$i"
} 

fun main() {
    for (i in 100 downTo 1 step 2) {
        // ...
    }
}
```

### 2.4.3 Iterating over maps

**Iterating over a map with a for loop** 

```kotlin
fun main() {
    val collection = listOf("red", "green", "bliue")
    for (color in collection) {
        print("$color")
    }
}
```

**Initializing and iterating over a map** 

```kotlin
fun main() {
    val binaryReps = mutableMapOf<Char, String>()
    for (c in 'A'..'F') {
        val binary = c.code.toString(radix = 2)
        binaryReps[c] = binary
    }

    for ((letter, binary) in binaryReps) {
        println("$letter = $binary")
    }
}
```

**loop with indices** 

```kotlin
fun main() {
    val list = listOf("10", "11", "1001")
    for ((index, element) in list.withIndex()) {
        println("$index: $element")
    }
}
```

### 2.4.4 Using in to check collection and range membership

We can use `in`  or  `!in` operators to check whether a value is in a range or its opposite.

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'
```

It also works in `when` expression

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know..."
}
val isKotlin = "Kotlin" in "Java".."Scala"
val isKotlin = "Kotlin" in setOf("Java", "Scala")
```

## 2.5 Thorwing and catching exception in Kotlin

Function can complete in a normal way or throw an exception if an error occurs. The function caller can catch this exception and process it; if it doesn’t, the exception is rethrown further up the stack.

```kotlin
if (percentage !in 0..100) {
    throw IllegalArgumentException(
        "A percentage value must be between 0 and 100: $percentage"
    )
}

val percentage =
    if (number in 0..100)
        number
    else
        throw IllegalArgumentException(       
            "A percentage value must be between 0 and 100: $number"
        )
```

### 2.5.1 Handling exceptions and recovering from errors: try, catch, and finally

You can try to recover from erros with `try` , `catch`  and `finally`.

```kotlin
import java.io.BufferedReader
import java.io.StringReader

fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException) {
        return null
    } finally {
        reader.close()
    }
}

fun main() {
    val reader = BufferedReader(StringReader("239"))
    println(readNumber(reader))
}
```

In Java, checked exceptions are part of the method signature

```java
Integer readNumber(BufferedReader reader) throws IOException
```

- Java's readLine and close may throw IOException, which is a checked exception. It needs to be handled explicitly in Java. 
- Kotlin doesn’t differentiate between checked and unchecked exceptions. You don't specify the exceptions thrown by a function, and you may or may not handle any exceptions.

In Kotlin, the compiler does not force you to handle exceptions 

```kotlin
fun readNumber(reader: BufferedReader): Int {
    val line = reader.readLine()
    reader.close()
    return Integer.parseInt(line)
}
```



### 2.5.2 Using try as an expression

`try` is an expression just like `if` and `when` . You can assign the value of your try expression to a variable

```kotlin
import java.io.BufferedReader
import java.io.StringReader

fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
        // not printing if error
    }

    println(number)
}

fun main() {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}
```

```kotlin
import java.io.BufferedReader
import java.io.StringReader

fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }

    println(number)
}

fun main() {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}
```

