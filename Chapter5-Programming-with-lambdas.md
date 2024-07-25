# 5. Programming with lambdas

Lambda expressions are small chunks of code that can be passed to other funtions.

## 5.1 Lambda expressions and member reference

### 5.1.1 Introduction to lambdas: Blocks of code as values

**Functional programming**

- First class functions 
- Immutatbility
- No side effects.



Listener implementation. Object vs lambda

```kotlin
// listener with an object declaration
button.setOnClickListener(object: OnClickListener) {
	override fun onClick(v: View) {
		println("I was clicked!")
	}
})
```

```kotlin
// listener with a lambda
button.setOnClickListener {
  println("I was clicked!")
}
```



### 5.1.2 Lambdas and collections

Searching via a for loop and lambda

```kotlin
// for loop
fun findTheOldest(people: List<Person>) {
  var maxAge = 0
  var theOldest: Person? = null
 
  // linear search
  for (person in people) {
    if (person.age > maxAge) {
      maxAge = person.age
      theOldest = person
    }
  }
  
  println(theOldest)
}
```

```kotlin
// maxOrNull funtion
fun main() {
  val people = listOf(Person("Alice", 29), Person("Bob", 31))
  println(people.maxByOrNull { it.age })
}
```

The funtion that sepcifies wihich values should be compared to find the maximum elements. 

The code in curly braces `{ it.age }` is a lambda implementing this selector logic. 

As the lambda only takes one argument, and we don't specify an explicit name for it, we refer it to using the implicit name `it`

```kotlin
// searching using a member reference
people.maxByOrNull(Person::age)
```



### 5.1.3 Syntax for lambda expressions

lambda expression syntax - a lambda is always surrounded by curly braces, specifies a number of parameters, and provides a body of the lambda which contains the actual logic

`{x: Int, y: Int -> x + y }`

```kotlin
fun main() {
  // store a lambda in a variable
  val sum = { x: Int, y: Int -> x + y }
  println(sum(1, 2))
  
  // call the lambda directly
  { println(42) }()
  
  // you could use the library function run that executes the lambda
  run { println(42) }
  
  // a block of several statements stored in a varaible
  val myFavoriteNumber = run {
    println("I'm thinking!")
    println("I'm doing some more work!")
    42
  }
}
```

```kotlin
people.maxByOrNull({ p: Person -> p.age })
// move a lambda out of parentheses if it's the last arg in a function call
people.maxByOrNull() { p: Person -> p.age }
// When the lambda is the only argument to a function, you can also remove the empty parentheses from the call/
people.maxByOrNull { p: Person -> p.age } // explicit type
people.maxByOrNull { p -> p.age } // type inference
people.maxByOrNull { it.age } // implicit name for the only lambda parameter `it`
people.maxByOrNull(Person::age)
```



.joinToString() examples

```kotlin
fun main() {
  val people = listOf(Person("Alice", 29), Person("Bob", 31))
  val names = people.joinToString(
  	separator = " ",
    transform = { p: Person -> p.name }
  )
  println(names)
  // Alice, Bob
}
```

outside the parentheses

```kotlin
people.joinToString(" ") { p: Person -> p.name }
```



### 5.1.4 Accessing variable in scope

“If you use a lambda in a function, you can access the parameters of that function as well as the local variables declared before the lambda”

```kotlin
fun printMessageWithPrefix(messages: Collection<String>, prefix: String) {
  messages.forEach {
    println("$prefix $it") // accessing the prefix param in lambda
  }
}

fun main() {
  val errors = listOf("403 Forbidden", "404 Not Found")
  printMessagesWithPrefix(errors, "Error:")
}
```



**Capturing a mutable variable: Implementation details**

Java allows you to capture only final variables. 

- declare an array of one element in which to store the mutable value
- create an instance of a wrapper class that stores the reference that can be changed.

```kotlin
// immutable variable is captured but the actual value is stored in the field and can be changed 
class Ref<T>(var value: T)

fun main() {
  val counter = Ref(0)
  val inc = { count.value ++ }
}
```



Lambda used as an event handler

```kotlin
fun tryToCountButtonClicks(button: Button): Int {
  var clicks = 0
  button.onClick { click++ }
  return clicks
}
```

From ChatGPT

> [!TIP]
>
> The reason the return value of clicks is always 0 is that the variable clicks is modified inside the lambda expression, but **the value returned by the function is captured before any clicks actually happen**. In Kotlin, lambdas capture variables by reference, but the return statement executes immediately, long before the lambda (onClick handler) has a chance to run.
>
> Here’s an explanation of why this happens:
>
> ​	1.	**Variable Initialization**: clicks is initialized to 0.
>
> ​	2.	**Lambda Expression**: The lambda expression button.onClick { clicks++ } is defined and set as the onClick listener for the button.
>
> ​	3.	**Return Statement**: The function tryToCountButtonClicks immediately returns the current value of clicks, which is still 0 because no clicks have occurred yet.

### 5.1.5 Member reference

“What if the code you need to pass as a parameter is already defined as a function?" You can pass a lambda that calls that function, but doing so is somewhat redundant. 

Java 8, if you can convert function to a value. You use the `::` operator for that. 

```kotlin
val getAge = Person::age
```

Person is the class, age is the member, separated by a double colon.

```kotlin
val getAge = { person: Person -> person.age }
```

A member reference has the same type as a lambda that calls that function or property, you can use the two interchangeably.

```kotlin
people.maxByOrNull(Person:: age)
people.maxByOrNull { person: Person -> person.age }
```

Omitting the class name and start with ::. It can have a reference to a function that's declared at the top level
```kotlin
fun salute() = println("Salute!")
 
fun main() {
    run(::salute) 
    // Salute!
}
```

When a lambda delegates to a function that takes several parameters, it is convienient to provide a member reference

```kotlin
val action = { person: Pesron, message: String -> 
	sendEmail(person, message)             
}
val nextAction = :: sendEmail
```

You can store or postpone the action of creating an instance of a class using a constructor reference.

```kotlin
data class Person(val name: String, val age: Int)

fun main() {
  val createPerson = ::Person
  val p = createPerson("Alice", 29)
  println(p)
}
```

Reference extension functions in the same way
```kotlin
fun Person.isAdult() = age >= 21
val predicate = Person:: isAdult
// Although isAdult isn’t a member of the Person class, you can access it via reference,
```

### 5.1.6 Bound callable references

“Using bound callable references, we can use the same member reference syntax to capture a reference to a method on a specific object instance”

```kotlin
fun main() {
  val seb = Person("Sebastian", 26)
  val personAgeFunction = Person::age
  println(personAgeFunction(seb)) // 26
  
  val sebsAgeFunction = seb::age
  println(sebsAgeFunction()) // 26
}
```

![image-20240725121625302](/Users/chiyuchan/Library/Application Support/typora-user-images/image-20240725121625302.png)

## 5.2 Using Java functional interfaces: Single abstract methods

```java
/* Before Java 8 */
button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        /* ... */
    }
}
 
/* Only since Java 8 */
button.setOnClickListener(view -> { /* ... */ })
```

```kotl
button.setOnClickListener { view -> /* ... */}
```

The lambda used to implement OnClickListener has one parameter of type View, as in the onClick method. 

```java
public interface onClickListener {
	void onClick (View v)
}
```

### 5.2.1 Passing a lambda as a parameter to a Java method

You can pass a lambda to any **Java** method that expects a functional interface.

```java
void postponeComputation(int delay, Runnable computation)
```

In **Kotlin**, you can invoke it and pass a lambda as an argument. 

```kotlin
postponeComputation(1000) { println(42) }
// ONLY only instance is created for the entire program
```

Or, you can create an anonymous object that implements `Runnable` explicitly.

```kotlin
postponeComputation(1000, object: Runnable {
  override fun run() {
    println(42)
  }
})
// New instance is created on each invocation.
```

### 5.2.2 SAM constructors: Explicit conversion of lambdas to functional interfaces

A SAM constructor is a **compiler-generated function** that lets you perform an explicit conversion of a lambda into an instance of an interface with a single abstract method.

If you have a method that returns an instance of functional interface, you can't return a lambda directly; you need to wrap it into a SAM construtor.



**Using a SAM constructor to return a value.**

```kotlin
fun createAllDoneRunnable(): Runnable {
  return Runnable { println("All done!")}
}

fun main() {
  createAllDoneRunnable().run()
  // ALL DONE
}
```

The SAM constrctor takes a single argument - a lambda that will be used as the body of the single abstract method in the functional interface, and returns an instance of the class implementing the interface. 

> [!NOTE]
>
> **Runnable** is a functional interface in Java that has a single abstract method: run()
>
> In the line Runnable { println("All done!")}, the lambda expression { println("All done!") } is used to **create an instance of the Runnable interface**.
>
> The Kotlin compiler understands that Runnable is a functional interface with a single abstract method.
>
> The lambda expression is **converted to an instance of Runnable**, and println("All done!") becomes the implementation of the run() method.

## 5.3 Defining SAM interfaces in Kotlin: fun interfaces

By declaring a `fun interface` in Kotlin, you can define your own functional interfaces.

```kotlin
fun interface IntCondition {
  // exactly one abstract method
  fun check(i: Int): Boolean
  fun checkString(s: String)= check(s.toInt())
  fun checkChar(c: Char) = check(c.digitToInt())
}

fun main() {
  val isOdd = IntCondition { it % 2 != 0 }
  println(isOdd.check(1))
  println(isOdd.checkString("2"))
  println(isOdd.checkChar('3'))
}
```

You define a functional interface called `IntCondition` with an abstract method check. You define an additional, non-abstract method called `checkString`, which invokes check after converting its parameter to an integer. As with Java SAMs, you use the SAM constructor to instantiate the interface with a lambda that specifies the implementation of check.

```kotlin
fun checkCondition(i: Int, condition: IntCondition): Boolean {
  return condition.check(i)
}

fun main() {
  // Either pass a lambda directly
  checkCondition(1) { it % 2 != 0}
  val isOdd: (Int) -> Boolean = { it % 2 !=0 }
  
  // or reference a lambda with a matching signature 
  checkCondition(1, isOdd)
}
```

## 5.4 Lambdas with receivers: with, apply, and also

### 5.4.1 performing multiple operations on the same object: with

Many languages have special statements you can use to perform multiple operations on the same object without repeating its name.

 Kotlin also has this facility, but it’s provided as a library function called `with`

```kotlin
fun alphabet(): String {
  val result = StringBuilder()
  for (letter in 'A'..'Z') {
    result.append(letter)
  }
  
  result.append("\nNow I know the alphabet!")
  return result.toString()
}

fun main() {
  println(alphabet())
  // ABCDEFGHIJKLMNOPQRSTUVWXYZ
  // Now I know the alphabet.
}
```



**Using with**

```kotlin
fun alphabet(): String {
  val stringBuilder = StringBuilder()
  return with(stringBuilder) {
    for (letter in 'A' .. 'Z') {
      this.append(letter)
    }
    this.append("\nNow I know the alphabet!")
    this.toString()
  }
} 
```

The with structure looks like a special construct, but it’s a function that takes **two arguments: stringBuilder, in this case, and a lambda.**



Using with and an expression body to build the alphabet

```kotlin
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
    toString()
}
```



### 5.4.2 Initializing and configuring objects: The apply function

Almost exactly the same as with;

**apply - always returns the object passed to it as an argument.**

```kotlin
fun alphabet() = StringBuilder().apply {
  for (letter in 'A'..'Z') {
    append(letter)
  }
  append("\nNow I know the alphabet!")
}.toString()
```

**Using apply to initialize a TextView**

```kotlin
fun createViewWithCustomAttributes(context: Context) =
    TextView(context).apply {
        text = "Sample Text"
        textSize = 20.0
        setPadding(10, 0, 0, 0)
    }
```

In the lambda passed to apply, the TextView instance becomes the receiver, so you can call methods and set properties on it. 

After the lambda is executed, apply returns that instance, which is alreadey initailized.

**Using buildString to build the alphabet**

```kotlin
fun alphabet() = buildString {
  for (letter in 'A'..'Z') {
    append(letter)
    append("\nNow I know the alphabet!")
  }
}
```

Use of buildList, buildSet, buildMap

```kotlin
val fibonacci = buildList {
    addAll(listOf(1, 1, 2))
    add(3)
    add(index = 0, element = 3)
}
 
val shouldAdd = true
 
val fruits = buildSet {
    add("Apple")
    if (shouldAdd) {
        addAll(listOf("Apple", "Banana", "Cherry"))
    }
}
 
val medals = buildMap<String, Int> {
    put("Gold", 1)
    putAll(listOf("Silver" to 2, "Bronze" to 3))
}
```

### 5.4.3 Performing additional actions with the object: also

You can use the also function to take a receiver object, perform an action on it, and then return the receiver object.

When using also, **the object doesn’t become the receiver type inside the lambda** but **makes the object available as an argument,** named `it` by default. The also function returns the object it was called on, as you can see in these inlay hints.

```kotlin
fun main() {
    val fruits = listOf("Apple", "Banana", "Cherry")
    val uppercaseFruits = mutableListOf<String>()
    val reversedLongFruits = fruits
        .map { it.uppercase() }
        .also { uppercaseFruits.addAll(it) }
        .filter { it.length > 5 }
        .also { println(it) }
        .reversed()
    // [BANANA, CHERRY]
    println(uppercaseFruits)
    // [APPLE, BANANA, CHERRY]
    println(reversedLongFruits)
    // [CHERRY, BANANA]
}
```

