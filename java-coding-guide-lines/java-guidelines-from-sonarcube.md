# Coding Guidelines

## **Methods "wait\(...\)", "notify\(\)" and "notifyAll\(\)" should not be called on Thread instances**

Internally, the JVM relies on these methods to change the state of the Thread \(`BLOCKED`, `WAITING`, ...\), so calling them will corrupt the behavior of the JVM.

## Loops should not be infinite

An infinite loop is one that will never end while the program is running, i.e., you have to kill the program to get out of the loop. Whether it is by meeting the loop's end condition or via a `break`, every loop should have an end condition.

#### Noncompliant Code Example

```java
for (;;) {  // Noncompliant; end condition omitted
  // ...
}

int j;
while (true) { // Noncompliant; end condition omitted
  j++;
}

int k;
boolean b = true;
while (b) { // Noncompliant; b never written to in loop
  k++;
}

```

#### Compliant Solution

```java
int j;
while (true) { // reachable end condition added
  j++;
  if (j  == Integer.MIN_VALUE) {  // true at Integer.MAX_VALUE +1
    break;
  }
}
int k;
boolean b = true;
while (b) {
  k++;
  b = k < Integer.MAX_VALUE;
}

```

## Resources should be closed

Connections, streams, files, and other classes that implement the `Closeable` interface or its super-interface, `AutoCloseable`, needs to be closed after use. Further, that `close` call must be made in a `finally` block otherwise an exception could keep the call from being made. Preferably, when class implements `AutoCloseable`, resource should be created using "try-with-resources" pattern and will be closed automatically.

#### Noncompliant Code Example

```java
private void readTheFile() throws IOException {
  Path path = Paths.get(this.fileName);
  BufferedReader reader = Files.newBufferedReader(path, this.charset);
  // ...
  reader.close();  // Noncompliant
  // ...
  Files.lines("input.txt").forEach(System.out::println); // Noncompliant: The stream needs to be closed
}

private void doSomething() {
  OutputStream stream = null;
  try {
    for (String property : propertyList) {
      stream = new FileOutputStream("myfile.txt");  // Noncompliant
      // ...
    }
  } catch (Exception e) {
    // ...
  } finally {
    stream.close();  // Multiple streams were opened. Only the last is closed.
  }
}
```

#### Compliant Solution

```java
private void readTheFile(String fileName) throws IOException {
    Path path = Paths.get(fileName);
    try (BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
      reader.readLine();
      // ...
    }
    // ..
    try (Stream<String> input = Files.lines("input.txt"))  {
      input.forEach(System.out::println);
    }
}

private void doSomething() {
  OutputStream stream = null;
  try {
    stream = new FileOutputStream("myfile.txt");
    for (String property : propertyList) {
      // ...
    }
  } catch (Exception e) {
    // ...
  } finally {
    stream.close();
  }
}

```

## Hibernate should not update database schemas

The use of any value but `"validate"` for `hibernate.hbm2ddl.auto` may cause the database schema used by your application to be changed, dropped, or cleaned of all data. In short, the use of this property is risky, and should only be used in production with the `"validate"` option, if it is used at all.

#### Noncompliant Code Example

```markup
<session-factory>
  <property name="hibernate.hbm2ddl.auto">update</property>  <!-- Noncompliant -->
</session-factory>

```



#### Compliant Solution

```markup
<session-factory>
  <property name="hibernate.hbm2ddl.auto">validate</property>  <!-- Compliant -->
</session-factory>
```

## Strings and Boxed types should be compared using "equals\(\)"

It's almost always a mistake to compare two instances of `java.lang.String` or boxed types like `java.lang.Integer` using reference equality `==` or `!=`, because it is not comparing actual value but locations in memory.

#### Noncompliant Code Example

```java
String firstName = getFirstName(); // String overrides equals
String lastName = getLastName();

if (firstName == lastName) { ... }; // Non-compliant; false even if the strings have the same value

```

#### Compliant Solution

```java
String firstName = getFirstName();
String lastName = getLastName();

if (firstName != null && firstName.equals(lastName)) { ... };

```

## Week Year \("YYYY"\) should not be used for date formatting

Few developers are aware of the difference between Y for "Week year" and y for Year when formatting and parsing a date with SimpleDateFormat.

That's likely because for most dates, Week year and Year are the same, so testing at any time other than the first or last week of the year will yield the same value for both y and Y. But in the last week of December and the first week of January, you may get unexpected results

#### Noncompliant Code Example

```java
Date date = new SimpleDateFormat("yyyy/MM/dd").parse("2015/12/31");
String result = new SimpleDateFormat("YYYY/MM/dd").format(date);   //Noncompliant; yields '2016/12/31'
```

#### Compliant Solution

```java
Date date = new SimpleDateFormat("yyyy/MM/dd").parse("2015/12/31");
String result = new SimpleDateFormat("yyyy/MM/dd").format(date);   //Yields '2015/12/31' as expected
```

## Exception should not be created without being thrown

Creating a new `Throwable` without actually throwing it is useless and is probably due to a mistake.

## Collection sizes and array length comparisons should make sense

The size of a collection and the length of an array are always greater than or equal to zero. So testing that a size or length is greater than or equal to zero doesn't make sense, since the result is always `true`. Similarly testing that it is less than zero will always return `false`. Perhaps the intent was to check the non-emptiness of the collection or array instead.

#### Noncompliant Code Example

```java
if (myList.size() >= 0) { ... }

if (myList.size() < 0) { ... }

boolean result = myArray.length >= 0;

if (0 > myArray.length) { ... }

```

#### Compliant Solution

```java
if (!myList.isEmpty()) { ... }

if (myArray.length >= 42) { ... }

```

## Optional value should only be accessed after calling isPresent\(\)

`Optional` value can hold either a value or not. The value held in the `Optional` can be accessed using the `get()` method, but it will throw a

`NoSuchElementException` if there is no value present. To avoid the exception, calling the `isPresent()` or `! isEmpty()` method should always be done before any call to `get()`.

Alternatively, note that other methods such as `orElse(...)`, `orElseGet(...)` or `orElseThrow(...)` can be used to specify what to do with an empty `Optional`.

#### Noncompliant Code Example

```java
Optional<String> value = this.getOptionalValue();

// ...

String stringValue = value.get(); // Noncompliant

```

#### Compliant Solution

```java
Optional<String> value = this.getOptionalValue();

// ...

if (value.isPresent()) {
  String stringValue = value.get();
}

```

## Consumed Stream pipelines should not be reused

Stream operations are divided into intermediate and terminal operations, and are combined to form stream pipelines. After the terminal operation is performed, the stream pipeline is considered consumed, and cannot be used again. Such a reuse will yield unexpected results.

## Non-thread-safe fields should not be static

Not all classes in the standard Java library were written to be thread-safe. Using them in a multi-threaded manner is highly likely to cause data problems or exceptions at runtime.

This rule raises an issue when an instance of `Calendar`, `DateFormat`, `javax.xml.xpath.XPath`, or `javax.xml.validation.SchemaFactory` is marked `static`.

#### Noncompliant Code Example

```java
Stream<Widget> pipeline = widgets.stream().filter(b -> b.getColor() == RED);
int sum1 = pipeline.sum();
int sum2 = pipeline.mapToInt(b -> b.getWeight()).sum(); // Noncompliant

```

## Non-serializable objects should not be stored in "HttpSession" objects

If you have no intention of writting an `HttpSession` object to file, then storing non-`serializable` objects in it may not seem like a big deal. But whether or not you explicitly serialize the session, it may be written to disk anyway, as the server manages its memory use in a process called "passivation". Further, some servers automatically write their active sessions out to file at shutdown & deserialize any such sessions at startup.

The point is, that even though `HttpSession` does not `extend Serializable`, you must nonetheless assume that it will be serialized, and understand that if you've stored non-serializable objects in the session, errors will result.

```java
public class Address {
  //...
}

//...
HttpSession session = request.getSession();
session.setAttribute("address", new Address());  // Noncompliant; Address isn't serializable

```

## Non-public methods should not be "@Transactional"

Marking a non-public method `@Transactional` is both useless and misleading because Spring doesn't "see" non-`public` methods, and so makes no provision for their proper invocation. Nor does Spring make provision for the methods invoked by the method it called.

Therefore marking a `private` method, for instance, `@Transactional` can only result in a runtime error or exception if the method is actually written to be `@Transactional`.

#### Noncompliant Code Example

```java
@Transactional  // Noncompliant
private void doTheThing(ArgClass arg) {
  // ...
}

```

## "hashCode" and "toString" should not be called on array instances

While `hashCode` and `toString` are available on arrays, they are largely useless. `hashCode` returns the array's "identity hash code", and `toString` returns nearly the same value. Neither method's output actually reflects the array's contents. Instead, you should pass the array to the relevant static `Arrays` method.

```java
String argStr = Arrays.toString(args);
int argHash = Arrays.hashCode(args);
```

## Spring "@Controller" classes should not use "@Scope"

Spring `@Controller`s, `@Service`s, and `@Repository`s have `singleton` scope by default, meaning only one instance of the class is ever instantiated in the application. Defining any other scope for one of these class types will result in needless churn as new instances are created and destroyed. In a busy web application, this could cause a significant amount of needless additional load on the server.

This rule raises an issue when the `@Scope` annotation is applied to a `@Controller`, `@Service`, or `@Repository` with any value but "singleton". `@Scope("singleton")` is redundant, but ignored.

## Tests should include assertions

A test case without assertions ensures only that no exceptions are thrown. Beyond basic runnability, it ensures nothing about the behavior of the code under test.

* JUnit
* Fest 1.x
* Fest 2.x
* Rest-assured 2.0
* AssertJ
* Hamcrest
* Spring's org.springframework.test.web.servlet.ResultActions.andExpect\(\)
* Eclipse Vert.x
* Truth Framework
* Mockito
* EasyMock
* JMock
* WireMock
* RxJava 1.x
* RxJava 2.x
* Selenide
* JMockit

## Execution of the Garbage Collector should be triggered only by the JVM

Calling `System.gc()` or `Runtime.getRuntime().gc()` is a bad idea for a simple reason: there is no way to know exactly what will be done under the hood by the JVM

## "static" members should be accessed statically

While it is _possible_ to access `static` members from a class instance, it's bad form, and considered by most to be misleading because it implies to the readers of your code that there's an instance of the member per class instance.

#### Noncompliant Code Example

```java
public class A {
  public static int counter = 0;
}

public class B {
  private A first = new A();
  private A second = new A();

  public void runUpTheCount() {
    first.counter ++;  // Noncompliant
    second.counter ++;  // Noncompliant. A.counter is now 2, which is perhaps contrary to expectations
  }
}

```

#### Compliant Solution

```java
public class A {
  public static int counter = 0;
}

public class B {
  private A first = new A();
  private A second = new A();
  public void runUpTheCount() {
    A.counter ++;  // Compliant
    A.counter ++;  // Compliant
  }
}

```

## Tests should not be ignored

When a test fails due, for example, to infrastructure issues, you might want to ignore it temporarily. But without some kind of notation about why the test is being ignored, it may never be reactivated. Such tests are difficult to address without comprehensive knowledge of the project, and end up polluting their projects.

#### Noncompliant Code Example

```java
@Ignore  // Noncompliant
@Test
public void testDoTheThing() {
  // ...

```

#### Compliant Solution

```java
@Test
public void testDoTheThing() {
  // ...

```

## Exception types should not be tested using "instanceof" in catch blocks

Multiple catch blocks of the appropriate type should be used instead of catching a general exception, and then testing on the type.

#### Noncompliant Code Example

```java
try {
  /* ... */
} catch (Exception e) {
  if(e instanceof IOException) { /* ... */ }         // Noncompliant
  if(e instanceof NullPointerException{ /* ... */ }  // Noncompliant
}

```

#### Compliant Solution

```java
try {
  /* ... */
} catch (IOException e) { /* ... */ }                // Compliant
} catch (NullPointerException e { /* .... */ }       // Compliant

```

## Try-catch blocks should not be nested

Nesting `try`/`catch` blocks severely impacts the readability of source code because it makes it too difficult to understand which block will catch which exception.

## "catch" clauses should do more than rethrow

 ****A `catch` clause that only rethrows the caught exception has the same effect as omitting the `catch` altogether and letting it bubble up automatically, but with more code and the additional detriment of leaving maintainers scratching their heads.

Such clauses should either be eliminated or populated with the appropriate logic.

#### Noncompliant Code Example

```java
public String readFile(File f) {
  StringBuilder sb = new StringBuilder();
  try {
    FileReader fileReader = new FileReader(fileName);
    BufferedReader bufferedReader = new BufferedReader(fileReader);

    while((line = bufferedReader.readLine()) != null) {
      //...
  }
  catch (IOException e) {  // Noncompliant
    throw e;
  }
  return sb.toString();
}

```

#### Compliant Solution

```java
public String readFile(File f) {
  StringBuilder sb = new StringBuilder();
  try {
    FileReader fileReader = new FileReader(fileName);
    BufferedReader bufferedReader = new BufferedReader(fileReader);

    while((line = bufferedReader.readLine()) != null) {
      //...
  }
  catch (IOException e) {
    logger.LogError(e);
    throw e;
  }
  return sb.toString();
}

```

##  Strings should not be concatenated using '+' in a loop

Strings are immutable objects, so concatenation doesn't simply add the new String to the end of the existing string. Instead, in each loop iteration, the first String is converted to an intermediate object type, the second string is appended, and then the intermediate object is converted back to a String. Further, performance of these intermediate operations degrades as the String gets longer. Therefore, the use of StringBuilder is preferred.

#### Noncompliant Code Example

```java
String str = "";
for (int i = 0; i < arrayOfStrings.length ; ++i) {
  str = str + arrayOfStrings[i];
}

```

#### Compliant Solution

```java
StringBuilder bld = new StringBuilder();
  for (int i = 0; i < arrayOfStrings.length; ++i) {
    bld.append(arrayOfStrings[i]);
  }
  String str = bld.toString();

```

## Lambdas should be replaced with method references

Method/constructor references are more compact and readable than using lambdas, and are therefore preferred. Similarly, `null` checks can be replaced with references to the `Objects::isNull` and `Objects::nonNull` methods.

#### Noncompliant Code Example

```java
class A {
  void process(List<A> list) {
    list.stream()
      .map(a -> a.<String>getObject())
      .forEach(a -> { System.out.println(a); });
  }

  <T> T getObject() {
    return null;
  }
}

```

#### Compliant Solution

```java
class A {
  void process(List<A> list) {
    list.stream()
      .map(A::<String>getObject)
      .forEach(System.out::println);
  }

  <T> T getObject() {
    return null;
  }
}

```

## URIs should not be hardcoded

Hard coding a URI makes it difficult to test a program: path literals are not always portable across operating systems, a given absolute path may not exist on a specific test environment, a specified Internet URL may not be available when executing the tests, production environment filesystems usually differ from the development environment, ...etc. For all those reasons, a URI should never be hard coded. Instead, it should be replaced by customizable parameter.

#### Noncompliant Code Example

```java
public class Foo {
  public Collection<User> listUsers() {
    File userList = new File("/home/mylogin/Dev/users.txt"); // Non-Compliant
    Collection<User> users = parse(userList);
    return users;
  }
}

```

#### Compliant Solution

```java
public class Foo {
  // Configuration is a class that returns customizable properties: it can be mocked to be injected during tests.
  private Configuration config;
  public Foo(Configuration myConfig) {
    this.config = myConfig;
  }
  public Collection<User> listUsers() {
    // Find here the way to get the correct folder, in this case using the Configuration object
    String listingFolder = config.getProperty("myApplication.listingFolder");
    // and use this parameter instead of the hard coded path
    File userList = new File(listingFolder, "users.txt"); // Compliant
    Collection<User> users = parse(userList);
    return users;
  }
}
```

