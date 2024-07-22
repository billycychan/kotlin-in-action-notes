>  Kotlin is a modern programming language on the Java virtual machine (JVM) and beyond” “ It’s a general-purpose, concise, safe, and pragmatic language.” Developers used it for writing mobile apps, build service-side applications and create desktop software.

## 1.1 A taste of Kotlin

```kotlin
data class Person(
    val name: String,
    val age: Int? = null
)

fun main() {
    val persons = listOf(
        Person("Alice", age = 29),
        Person("Bob"),
    )
    val oldest = persons.maxByOrNull {
        it.age ?: 0
    }
    println("The oldest is: $oldest")
}

// The oldest is: Person(name=Alice, age=29) 
```

- Data class
- Read-only property
- Nullable type
- Top-level function
- Named argument
- Trailing comma
- Lambda expression
- Null-coalescing Elvis operator
- String template
- Autogenerated toString



## 1.2 Kotlin’s primary traits

- It it a multiparadigm language.

- It is statically typed, catching erros at compile time.

- It combines ideas from objected-oriented and functional language.

    

### 1.2.1 Kotlin use cases: Android, server side, anywhere Java runs, and beyond

- It provides across-the-board productivity improvements for all tasks that come up during development process and aims for an excellent level of integration with libraries that support specific domains of programming paradigms.

- Building mobile apps that run on Android devices.

- Building server-side code (typicall, backends of web application)

- 

- Kotlin also works in other contexts  as well. You can build cross-platform applications for desktop, iOS, and Android - and even run Kotlin in the browser.’

### 1.2.2 Static typing makes Kotlin performant, reliable, and maintainable

- Every expression is known at compile time.

- It has the benefits of

    - Performance
    - Reliability
    - Maintainability
    - Tool support

```kotlin
    val x: Int = 1
    val y = 1
```

### 1.2.3 Combining functional and object-oriented programming makes Kotlin safe and flexible

- Key concept of functional programming

    - First-class functions
    - Immutability
    - No side effects, pure functions

- Benefits

    - Avoid code duplication
    - Safe concurrency
    - Easier testing

- Kotlin’s features to support functional programming

    - Function types
    - Lambda expressions
    - Member references
    - Data classes
    - Standard library APIs

    ```kotlin
    messages
    .filter { it.body.isNotBlank() && !it.isRead }
    .map(Message::sender)
    .distinct()
    .sortedBy(Sender::name)
    ```

    

### 1.2.4 Concurrent and asynchronous code becomes natural and structured with coroutines

- Usual approaches to concrrency are threads, callbacks, futures, promises, reactive extensions and more.

- For Kotlin, it uses suspendable computations called coroutines, where code can suspend its execution and resume its work at a later point.

- Making three network calls one by one

```kotlin
  suspend fun processUser(credentials: Credentials) {
    val user = authenticate(credentials)
    val data = loadUserData(user)
    val profilePicture = loadImage(data.imageID)

    suspend fun authenticate(c: Credentials): User {/* ... */}
    suspend fun loadUserData(u: User): Data { /* ... */ }
    suspend fun loadImage(id: Int): Image { /* ... */ }”
  }
```



Load two images concurrently using async and then wait for the loading to be completed via await.

```kotlin
suspend fun loadAndOverlay(first: String, second: String): Image =
    coroutineScope {
        val firstDeferred = async { loadImage(first) }                  
        val secondDeferred = async { loadImage(second) }                
        combineImages(firstDeferred.await(), secondDeferred.await())    
   }
```

The structured concurrency can also help manage the lifetime of your coroutines.

### 1.2.5 Kotlin can be used for any purpose: It’s free, open source, and open to contributions

- The Kotlin language, including its compiler, libraries and all related tooling, is entirely open source and free to use for any purpose.
- [http://github.com/jetbrains/kotlin](http://github.com/jetbrains/kotlin”)
- Choice of multiple open source IDEs.

    - IntelliJ IDEA Community Edition
    - Android Studio

## 1.3 Areas in which Kotlin is often used

- Server Side
- Android Development

### 1.3.1 Powering backends: Service-side development with Kotlin

- Server-side programming

    - Web applications that return HTML pages to a browser
    - Backends for mobile or single-page applications that expose JSON API over HTTP
    - Microservices that communicate with other microservices over an RPC protocol or message bus.

- Kotlin profits from its seamless interoperability with existing Java code.
- Kotlin has better reliability to avoid NullPointerException at run time.
- Spring provide first-class support for Kotlin out-of-box.


```kotlin
@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
  runApplication<DemoApplication>(*args)
}

@RestController
class GreetingResource {
  @GetMapping
  func index(): List<Greeting> = listOf(
    Greeting(1, "Hello"),
    Greeting(2, "Bonjour!"),
    Greeting(3, "Guten Tag!"),
  )
}
```



- Ktor, server-side framework built by Jetbrains,

```kotlin
fun main() {
  embeddedServer(Netty, port = 8000) {
    routing {
      get ("/") {
        call.respondText("Hello, world!")
      }
    }
  }.start(wait = true)
}
```

### 1.3.2 Mobile Development: Android is Kotlin First

- Android supported Kotlin for building app since 2017.

- 2019, Android became Kotlin first.

- https://developer.android.com/jetpack , Google’s development tools, samples, documentation and training content all primarily focus on Kotlin.

- “The Android KTX library (https://developer.android.com/kotlin/ktx), built by the Android team, improves your experience even further by adding Kotlin-friendly adapters around many standard Android APIs.”

- Google Jetpack Compose Kit for building Android user interfaces is designed for Kotlin from ground up.

```kotlin
Column(
      modifier = Modifier.fillMaxSize(),
      verticalArrangement = Arrangement.Center,
      horizontalAlignment = Alignment.CenterHorizontally,
      content = {
          // Here, SimpleText is a Composable function which is going to describe the contents of
          // this activity that will be rendered on the screen.
          SimpleText("This is the Learn Jetpack Compose By Example tutorial")
      }
     )
```

-
###  1.3.3 Multiplatform: Sharing business logic and minizing duplicate work on iOS, JVM, JS and beyond
- It can be compiled to Javascript, allowing you to run Kotlin code in the browser and runtimes such as Node.js
- With Kotlin/Native, you can compile code to native binaries, allowing you to target iOS and other platforms with self-contained programs.
- With Kotlin./Wasm, a target that is still being developed at the time of writing, will make it possible for you to compile your Kotlin code to the Web-Assembly binary instruction format.
- “Kotlin also lets you specify which parts of your software should be shared between different targets and which parts have a platform-specific implementation, on a very fine-grained level.”
- “With Kotlin Multiplatform, you only have to write your business logic once, but you can use it in both iOS and Android targets in a completely native fashion and even make use of the respective APIs, toolkits, and capabilities these platforms offer.”

## 1.4 The philosophy of Kotlin
###  1.4.1 Kotlin is a pragmatic language
- It is designed to solve real-world problems.
- It features are chosen to address use cases encountered by many software developers.
- It mostly relies on features and solutions that have already appeared in other programming languages and have proven to be successful.
- It does not enforce any particular style.
- It focus on tooling.

### 1.4.2 Kotlin is concise
- It ensures all the code you write carries meaning. No getters/setters boilerplate code.
- It has rich set of standard library.

### 1.4.3 Kotlin is safe
- Running on the JVM provides memory safety, buffer overflows, and other problems caused by incorrect use of dynamically allocated memory.
- It is statically typed language and ensures type-safety.
- It makes it easy to define read-only variable.
- It has nullable type. It can use is performing type check on the variables.

### 1.4.3 Kotlin is interoperable
- Kotlin↔Java

## 1.5 Using the Kotlin tools
### 1.5.1 Setting up and running the Kotlin code”
- small snippets can be run online
https://play.kotlinlang.org/#eyJ2ZXJzaW9uIjoiMi4wLjAiLCJwbGF0Zm9ybSI6ImphdmEiLCJhcmdzIjoiIiwibm9uZU1hcmtlcnMiOnRydWUsInRoZW1lIjoiaWRlYSIsImNvZGUiOiIvKipcbiAqIFlvdSBjYW4gZWRpdCwgcnVuLCBhbmQgc2hhcmUgdGhpcyBjb2RlLlxuICogcGxheS5rb3RsaW5sYW5nLm9yZ1xuICovXG5mdW4gbWFpbigpIHtcbiAgICBwcmludGxuKFwiSGVsbG8sIHdvcmxkISEhXCIpXG59In0=
- IntelliJ IDEA IDE

### 1.5.2 Compiling Kotlin Code

- Kotlin is a compiled language, which mean before you can run Kotlin code, you need to compile it. It could compiled to different targets:

    - JVM bytecode (.class) to run on the JVM
    - JVM bytecode to be further transformed and run on Android
    - Native targets to run natively on different operating systems
    - JavaScript, to run in a browser/
- “Android Runtime (ART) transforms the JVM bytecode to Dex bytecode and runs it instead.