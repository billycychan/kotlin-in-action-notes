# 4. Classes, Objects and interfaces

Kotlin's declaration are `final` and `public` by default.

## 4.1 Defining class hierachies
### 4.1.1 Interfaces in Kotlin

```kotlin
interface Clickable {
  fun click()
}
```

It declares an interface with a single abstract method named `click`, which doesn't return any value. All non-abstract classes implmenting the interface need to provide an implementation of this method.

```kotlin
class Button : Clickable {
  override fun click() = println("I w1as clicked")
}

fun main() {
  Button().click()
}
```

The `override` modifier marks mehtods and properties that override those from the superclass or interface. It is mandatory.



```kotlin
interface Clickable {
  fun click()
  fun showOff() = println("I'm clickable")
}
```

  `showoff` is a method with a default implementation



If you need to implement both interfaces with showOff method in your classes. Neither wins and get a compiler error.



``` kotlin
class Button : Clickable, Focusable {
  override fun click() = println("I was clicked")
  override fun showOff() {
    super<Clickable>.showOff()
    super<Focusable>.showOff()
  }
  // or
  // override fun showOff() = super<Clickable>.showOff()
}
```

```kotlin
fun main() {
  val button = Button()
  button.showOff()
  // I'm clickable
  // I'm focosuable
  button.setFocus(true)
  // I got focus
  button.click()
}
```


Calling Inherited and overridden methods
```java
class JavaButton implements Clickable {
    @Override
    public void click() {
        System.out.println("I was clicked");
    }
 
    @Override
    public void showOff() {
        System.out.println("I'm showing off"); 
    }
}
```

### 4.1.2 Open, final, and abstract modifier Final by default

All classes and method are final by default.

Effective Java --> Design and document for inheritance or else prohibit it.

```kotlin
// Declaring an open class with an open method
open class RichButton : Clickable { // open class, others can inherit it
  fun disable(){}  // final function, can't override it in a subclass
  open fun animate(){} // open function, may override it in a subclass
  override fun click(){} // overrides an open function and is open as well
}
```

```kotlin
// Declaring a subclass of an open class that overrides open methods
class ThemedButton : RichButton() {
  override fun animate() {}
  override fun click()
  override fun showOff()
}
```

```kotlin
// forbidding an override
open class RichButton : Clickable {
  final override fun click() {}
}
```

“An abstract class usually contains abstract members that don’t have implementations and must be overridden in subclasses.”

```kotlin
abstract class Animated { // `abstract`, cannot create an instance of it
  abstract val animationSpeed: Double // doesn't not have 20, need to override / accessor 
  val keyframes: Int = 20 //  are not open by default  
  open val frames: Int = 60 //  open explicitly 
  
  abstract fun animate() // does not have implementation, must be overridden in subclass 
  open fun stopAnimating() // Non-abstract functions in abstract classes aren’t open by default but can be marked as such.”
  fun animateTwice()
}
```

| Modifer  | Corresponding Member                            | Comments                                                     |
| -------- | ----------------------------------------------- | ------------------------------------------------------------ |
| final    | can't be overridden                             | used by default for class members                            |
| open     | can't be overridden                             | should be specified explicitly                               |
| abstract | Must be overrridden                             | can be used only in abstract classes, abstract members can't have an implementation |
| override | Overrides a member in a superclass or interface | Overridden member is open by default, if not marked final    |

### 4.1.3 visibility modifiers: Public by default

Kotlin provides public protected and private modifiers,  analgous to their Java counterparts.

public - visible everywhere

internal - visible in a module

protected - visible in subclasses

private - visible inside a class or in the case of top-level declarations, visible inside a file.



```kotlin
internal open class TalkativeButton {
  private fun yell() = println("Hey!")
  protected fun whisper() = println("Let's talk!")
}

fun TalkativeButton.giveSpeech() { //Error, Public member exposes its internal receiver type TalkativeButton
  yell() // Error, cannot access yell, it is private in TalkativeButton
  whisper() //Error, cannot access whisper, it is protected in TalkativeButton
}

```

### 4.1.4 Inner and nested classes: Nested by default

Nested classes in Kotlin don't have access to the outer class instance, unless you specifically request that.

A **nested class** is a static class defined inside another class. It is not bound to a specific instance of the outer class.
An **inner class** is a non-static class defined inside another class. it is bound to a specific instance of the outer class.

```kotlin
interface State : Serializable

interface View {
  fun getCurrentState() : State
  fun restoreState(state: State){}
}
```



```java
public class Button implements View {
  @Override
  public State getCurrentState() {
    return new ButtonState();
  }
  
  @Override
  public void restoreState(State state) {}
  
  // Inner class by default in Java
  public class ButtonState implements State {} // not work, Java.io.NotSerializableException: Button exception
  
  // Used Static, make it as nested class
  public static class ButtonState implements State {} // work
}
```



```kotlin
class Button : View {
  override fun getCurrentState(): State = ButtonState()
  override fun restoreState(state: State) {}
  
  class ButtonState : State {} // default is static nested class in Java
}
```

A nested class in Kotlin with no explicit modifiers is the same as a static nested class in Java.

### 4.1.5 Sealed classes: Defining restricted class hierarchies
Sealed classes and interfaces provide **controlled inheritance** of your class hierarchies

```kotlin
// Expressions as interface implementation
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
 
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
      	// we have to check else branch
        else ->                                     
            throw IllegalArgumentException("Unknown expression")
    }
```

```kotlin
sealed class Expr                                     ❶
class Num(val value: Int) : Expr()
class Sum(val left: Expr, val right: Expr) : Expr()   ❷
 
fun eval(e: Expr): Int =
    when (e) {                                        ❸
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
    }
```

Marking a superclass with the sealed modifier restricts the possibility of creating subclasses

All direct subclasses of a sealed class **must be known at compile time** and declared in the same package as the sealed class itself, and all subclasses need to be **located within the same module**.

```kotlin
// It should be exhausted
sealed class Expr
class Num(val value: Int) : Expr()
class Sum(val left: Expr, val right: Expr) : Expr()
class Mul(val left: Expr, val right: Expr): Expr()
 
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        // ERROR: 'when’ expression must be exhaustive,
        // add necessary 'is Mul' branch or 'else' branch instead
    }
```



We can also create `sealed` `interface`. Handling all implementations of a sealed interface in a `when` statement also means you do not have to specify an `else` branch.

```kotlin
sealed interface Toggleable {
  fun toggle()
}

class LightSwitc : Toggleable {
  override fun toggle() = println("Lights")
}

class Camera : Toggleable {
  override fun toggle() = println("Camera!")
}
```

## 4.2 Declaring a class with non-trivial constructors or properties

Kotlin has primary constructor and a secondary constructor. It also allows you to put additional logic in initializer blocks. 

### 4.2.1 Initializing classes: Primary constructor and initializer blocks

a simple class

```kotlin
class User(val nickname: String)
```

The blcok of code surrounded by parenthese is called primary constructor.

- Specifying constructor parameters.
- Defining properties that are initialized by those parameters.

Under the hood

```kotlin
class User constructor(_nickname: String) {
  val nickname: String
  
  //  initializer block
  init {
    nickname = _nickname
  }
}

class User(_nickname: String) {
    val nickname = _nickname
}
```



```kotlin
class User(val nickname: String,
           val isSubscribed: Boolean = true)

fun main() {
    val alice = User("Alice") // “ Uses the default value true for the isSubscribed parameter”

    println(alice.isSubscribed) // t ru e
    val bob = User("Bob", false)
    println(bob.isSubscribed) // false
    val carol = User("Carol", isSubscribed = false)
    println(carol.isSubscribed) // false
}
```



### 4.2.2 Secondary constructors: Initializing the superclass in different ways

Classes with multiple constructors are much less common in Kotlin code than in Java.

Most situations where you’d need overloaded constructors are covered by Kotlin’s support for default parameter values and named argument syntax.

```java
import java.net.URI;
 
public class Downloader {
    public Downloader(String url) {
        // some code
    }
 
    public Downloader(URI uri) {
        // some code
    }
}
```


Two secondary constructors
```kotlin
open class Downloader {
    constructor(url: String?) { 
        // some code
    }
 
    constructor(uri: URI?) {
        // some code
    }
}
```

Calling the corresponding constructor of the superclass using the super() keyword.
```kotlin
class MyDownloader : Downloader {
    constructor(url: String?) : super(url) {
        // ...
    }
    constructor(uri: URI?) : super(uri) {
        // ...
    }
}
```



Delegate the constructor using this() keyword.

```kotlin
class MyDownloader : Downloader {
    constructor(url: String?) : this(URI(url))      ❶
    constructor(uri: URI?) : super(uri)
}
```

### 4.2.3 Implementing properties declared in interfaces

The interface does not specify whether the value should be stored in a backing field or obtained through a getter. It does not contain any state.

```kotlin
interface User {
    val nickname: String
}

// Concise syntax to declare a property directly in the primary constructor
class PrivateUser(override val nickname: String) : User

// Using getter
class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore('@')
}

// Assign the value to the nickname property in its initializer 
class SocialUser(val accountId: Int) : User {
    override val nickname = getNameFromSocialNetwork(accountId)
}

```

### 4.2.4 Accessing a backing field from a getter or setter

```kotlin
class User(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            println(
                """
                Address was changed for $name:
                "$field" -> "$value". // “Reads the backing field value”
                """.trimIndent()
            )
            field = value // “Updates the backing field value with the provided string”
        }
}

fun main() {
    val user = User("Alice")
    user.address = "Christoph-Rapparini-Bogen 23, 80639 Muenchen"
}
```

### 4.2.5 Changing accessor visibility

```kotlin
class LengthCounter {
    var counter: Int = 0
        private set

    fun addWord(word: String) {
        counter += word.length
    }
}

fun main() {
    val lengthCounter = LengthCounter()
    lengthCounter.addWord("Hi!")
    println(lengthCounter.counter)
}
```





## 4.3 Compiler-generated method: Data classes and class delegation

In Java, it defines several methods that need to be present in many classes and are usually implemented in a mechanical way

e.g equals, hasCode, toString

### 4.3.1 Universal object methods

**toString()**

“By default, the string representation of an object looks like **Customer@5e9f23b4** (the class name and the memory address at which the object is stored), which, in practice, isn’t very useful.

```kotlin
class Customer(val name: String, val postalCode: Int) {
    override fun toString() = "Customer(name=$name, postalCode=$postalCode)"
}

fun main() {
    val customer1 = Customer("Alice", 342562)
    println(customer1)
    // Customer(name=Alice, postalCode=342562)
}
```



**Equals**

In Kotlin, == checks whether the objects are equal, not the references. It is compiled to a call of equals.

 Java’s == compares values, whereas == on reference types compares references.

```kotlin
fun main() {
    val customer1 = Customer("Alice", 342562)
    val customer2 = Customer("Alice", 342562)
    println(customer1 == customer2)
    // false
}

```

**Hash containers: hashcode()**

The hashCode method is always overridden together with equals.

If two objects are equal, they must have the same hash code.



```kotlin
class Customer(val name: String, val postalCode: Int) {
    override fun equals(other: Any?): Boolean {
        if (other == null || other !is Customer)
            return false
        return name == other.name &&
               postalCode == other.postalCode
    }
    override fun toString() = "Customer(name=$name, postalCode=$postalCode)"

    override fun hashCode(): Int = name.hashCode() * 31 + postalCode
}
```



### 4.3.2 Data classes: Autogenerated implementations of universal methods

If you add the modifier `data` to your class, the necessary methods are automatically generated for you.

```kotlin
data class Customer(val name: String, val postalCode: Int)
```

**equals** for comparing instances

**hashCode** for using instances as keys in hash-based containers, such as HashMap

**toString** for generating string representations showing all the fields in declaration order



```kotlin
fun main() {
    val c1 = Customer("Sam", 11521)
    val c2 = Customer("Mart", 15500)
    val c3 = Customer("Sam", 11521)
    println(c1)
    // Customer(name=Sam, postalCode=11521)
    println(c1 == c2)
    // false
    println(c1 == c3)
    // true
    println(c1.hashCode())
    // 2580770
    println(c3.hashCode())
    // 2580770
}
```

**Data classes and immutability: The copy mehtod**

It is strongly recommended to only use read-only properties, making the instances of the data class immutable.

Kotlin has a copy() method that allows you to copy the instances of your classes, changing the values of some properties.

```kotlin
data class Customer(val name: String, val postalCode: Int)

fun main() {
    val bob = Customer("Bob", 973293)
    println(bob.copy(postalCode = 382555))
}
```



### 4.3.3 Class delegation: Using the by keyword

When you extend a class and override some of its methods, your code becomes dependent on the implementation details of the class you’re extending.

With Kotlin's `final` class by default, it ensures that only those classes designed for extensibility can be inherited from.

In simple words, you can understand `by` keyword as **provided by**.



Without By

```kotlin
class DelegatingCollection<T> : Collection<T> {
    private val innerList = arrayListOf<T>()
 
    override val size: Int get() = innerList.size
    override fun isEmpty(): Boolean = innerList.isEmpty()
    override fun contains(element: T): Boolean = innerList.contains(element)
    override fun iterator(): Iterator<T> = innerList.iterator()
    override fun containsAll(elements: Collection<T>): Boolean =
            innerList.containsAll(elements)
}
```

```kotlin
class DelegatingCollection<T>(
    innerList: Collection<T> = mutableListOf<T>() // primary constructor
) : Collection<T> by innerList
```

All the method implementations in the class are gone. The compiler will generate them, and the implmenetation is similar. 

## 4.4 The object keyowrd: Declaring a class and creating an instance, combined

### 4.4.1 Objects declarations: Singletons made easy

Doing singletons usually definie a class with a **private constructor** and a **static field** holding the only existing instance of the class.

```kotlin
object Payroll {
    val allEmployees = mutableListOf<Person>()
 
    fun calculateSalary() {
        for (person in allEmployees) {
            /* ... */
        }
    }
}

Payroll.allEmployees.add(Person(/* ... */))
Payroll.calculateSalary(
```

An `object` declaration is like a class but are not allowed constructors. Object declarations are created immediately at the point of definition, not through constructor calls from other places in the code.



**Comparator** interface

It receives two objects and returns an integer indicating which of the objects is greater.

You can pass this object as an argument to a function that takes a Comparator:

```kotlin
import java.io.File

object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(file1: File, file2: File): Int {
        return file1.path.compareTo(file2.path,
                ignoreCase = true)
    }
}

fun main() {
    println(CaseInsensitiveFileComparator.compare(
        File("/User"), File("/user")))
    val files = listOf(File("/Z"), File("/a"))
    println(files.sortedWith(CaseInsensitiveFileComparator))
}
```

In Java, we used `INSTANCE` to represent the instance of object

```java
/* Java */
CaseInsensitiveFileComparator.INSTANCE.compare(file1, file2);
Person.NameComparator.INSTANCE.compare(person1,  person2)
```

### 4.4.2 Companion objects: A place for factory methods and static members

Classes in Kotlin cant have static members. Kotlin doesn't have a static keyword, like Java does. It usually make use of top-levle functions. But top-level functions could not access private members of a class. 

To write a function that **can be called without having a class instance** but which **has access to the internals of a class**, you can write it as a member of an object declaration inside that class. Exactly one of the object declarations defined in a class can be marked with a special keyword: **companion**.

``` kotlin
class A {
    companion object {
        fun bar() {
            println("Companion object called")
        }
    }
}

fun main() {
    A.bar()
}
```



```kotlin
fun getNameFromSocialNetwork(accountId: Int) = "soc:$accountId"

class User private constructor(val nickname: String) { // it can’t be called from outside the class body”
    companion object {
        fun newSubscribingUser(email: String) =
            User(email.substringBefore('@'))

        fun newSocialUser(accountId: Int) =
            User(getNameFromSocialNetwork(accountId))
    }
}

fun main() {
    val subscribingUser = User.newSubscribingUser("bob@gmail.com")
    val socialUser = User.newSocialUser(4)
    println(subscribingUser.nickname)
}
```

### 4.4.3 Companion objects are regular objects 

A companion object is a regular object declared in a class. It can be named, implement an interface or have extension functions or properties.

```kotlin
class Person(val name: String) {
  // Specify the name
    companion object Loader {                                       
        fun fromJSON(jsonText: String): Person = /* ... */
    }
}
 
fun main() {
    val person = Person.Loader.fromJSON("""{"name": "Dmitry"}""")  
    println(person.name)
    // Dmitry
    val person2 = Person.fromJSON("""{"name": "Brent"}""")
    println(person2.name)
    // Brent
}
```

**Implementing interfaces in companion objects**

```kotlin
interface JSONFactory<T> {
    fun fromJSON(jsonText: String): T
}
 
class Person(val name: String) {
    companion object : JSONFactory<Person> {
        override fun fromJSON(jsonText: String): Person = /* ... */    
    }
}
```

The companion object for a class is compiled similarly to a regular object: **a static field in a class** refers to its instance. If the companion object isn’t named, it can be accessed through the **Companion** reference from the Java code:”



Companion Object extensions

```kotlin
// business logic module
class Person(val firstName: String, val lastName: String) {
    companion object {                                       ❶
    }
}
 
// client/server communication module
fun Person.Companion.fromJSON(json: String): Person {        ❷
    /* ... */
}
 
val p = Person.fromJSON(json)
```

### 4.4.4 Object expressions: Annoymous inner classes rephrased

The `object` keyword can be used not only for declaring named singleton-like objects, but also for declaring anonymous objects. It replace Java's use of anonymous inner classes.

```kotlin
interface MouseListener {
    fun onEnter()
    fun onClick()
}
 
class Button(private val listener: MouseListener) { /* ... */ }
```



```kotlin
// “ Implementing an event listener with an anonymous object
fun main() {
    Button(object : MouseListener {             
        override fun onEnter() { /* ... */ }    
        override fun onClick() { /* ... */ }    
    })
}
```

```kotlin
// You can assign a name to the object, you can store it in a variable.
val listener = object : MouseListener {
    override fun onEnter() { /* ... */ }
    override fun onClick() { /* ... */ }
}
```



## 4.5 Extra type safety without overhead: Inline classes

Sometimes it is useful to wrap a value in a class to create a more domain-specific type. However, it introduces runtime overhead due to additional heap allocations.

To solve such issues, Kotlin introduces a special kind of class called an *inline class*. 

```kotlin
value class Password(private val s: String)
```

To declare an inline class for the JVM backend, use the `value` modifier along with the `@JvmInline` annotation before the class declaration:

```kotlin
// For JVM backends
@JvmInline
value class Password(private val s: String)
```

```kotlin
interface PrettyPrintable {
    fun prettyPrint()
}
 
@JvmInline
value class UsdCent(val amount: Int): PrettyPrintable {
    val salesTax get() = amount * 0.06
    override fun prettyPrint() = println("${amount}¢")
}
 
fun main() {
    val expense = UsdCent(1_99)
    println(expense.salesTax)
    // 11.94
    expense.prettyPrint()
    // 199¢
}
```

This small change avoids the needless instantiation of objects **without giving up on the type safety** provided by your UsdCent wrapper type. At run time, instances of UsdCent will be represented as the wrapped property. 

This is also where inline classes get their name from: **the data of the class is inlined at the usage sites.**
