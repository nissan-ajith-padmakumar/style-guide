# Other points to consider

## Hibernate should not update database schemas

The use of any value but "validate" for hibernate.hbm2ddl.auto may cause the database schema used by your application to be changed, dropped, or cleaned of all data. In short, the use of this property is risky, and should only be used in production with the "validate" option, if it is used at all.

### Noncompliant Code Example
```xml
<session-factory>;

<property name="hibernate.hbm2ddl.auto">;update</property>; <!-- Noncompliant -->;

</session-factory>;
```
## Compliant Solution
```xml
<session-factory>;

<property name="hibernate.hbm2ddl.auto">;validate</property>; <!-- Compliant -->;

</session-factory>;
```
## Optional value should only be accessed after calling isPresent()

Optional value can hold either a value or not. The value held in the Optional can be accessed using the get() method, but it will throw a

NoSuchElementException if there is no value present. To avoid the exception, calling the isPresent() or ! isEmpty() method should always be done before any call to get().

Alternatively, note that other methods such as orElse(...), orElseGet(...) or orElseThrow(...) can be used to specify what to do with an empty Optional.

### Noncompliant Code Example
```java
Optional<String>; value = this.getOptionalValue();

// ...

String stringValue = value.get(); // Noncompliant
```
### Compliant Solution
```java
Optional<String>; value = this.getOptionalValue();

// ...

if (value.isPresent()) {

    String stringValue = value.get();

}
```
## Non-thread-safe fields should not be static

Not all classes in the standard Java library were written to be thread-safe. Using them in a multi-threaded manner is highly likely to cause data problems or exceptions at runtime.

This rule raises an issue when an instance of Calendar, DateFormat, javax.xml.xpath.XPath, or javax.xml.validation.SchemaFactory is marked static.

## Non-serializable objects should not be stored in "HttpSession" objects

If you have no intention of writting an HttpSession object to file, then storing non-serializable objects in it may not seem like a big deal. But whether or not you explicitly serialize the session, it may be written to disk anyway, as the server manages its memory use in a process called "passivation". Further, some servers automatically write their active sessions out to file at shutdown &amp; deserialize any such sessions at startup.

The point is, that even though HttpSession does not extend Serializable, you must nonetheless assume that it will be serialized, and understand that if you&#39;ve stored non-serializable objects in the session, errors will result.
```java
public class Address {

//...

}

//...

HttpSession session = request.getSession();

session.setAttribute("address", new Address()); // Noncompliant; Address isn&#39;t serializable
```
## Non-public methods should not be "@Transactional"

Marking a non-public method @Transactional is both useless and misleading because Spring doesn&#39;t "see" non-public methods, and so makes no provision for their proper invocation. Nor does Spring make provision for the methods invoked by the method it called.

Therefore marking a private method, for instance, @Transactional can only result in a runtime error or exception if the method is actually written to be @Transactional.

###Noncompliant Code Example
```java
@Transactional // Noncompliant

private void doTheThing(ArgClass arg) {

// ...

}
```

## Spring "@Controller" classes should not use "@Scope"

Spring @Controllers, @Services, and @Repositorys have singleton scope by default, meaning only one instance of the class is ever instantiated in the application. Defining any other scope for one of these class types will result in needless churn as new instances are created and destroyed. In a busy web application, this could cause a significant amount of needless additional load on the server.

This rule raises an issue when the @Scope annotation is applied to a @Controller, @Service, or @Repository with any value but "singleton". @Scope("singleton") is redundant, but ignored.

## Execution of the Garbage Collector should be triggered only by the JVM

Calling System.gc() or Runtime.getRuntime().gc() is a bad idea for a simple reason: there is no way to know exactly what will be done under the hood by the JVM

## "static" members should be accessed statically

While it is _possible_ to access static members from a class instance, it&#39;s bad form, and considered by most to be misleading because it implies to the readers of your code that there&#39;s an instance of the member per class instance.

### Noncompliant Code Example
```java
public class A {

    public static int counter = 0;

}

public class B {

    private A first = new A();

    private A second = new A();

    public void runUpTheCount() {

        first.counter ++; // Noncompliant

        second.counter ++; // Noncompliant. A.counter is now 2, which is perhaps contrary to expectations

    }

}
```
### Compliant Solution
```java
public class A {

    public static int counter = 0;

}

public class B {

    private A first = new A();

    private A second = new A();

    public void runUpTheCount() {

        A.counter ++; // Compliant

        A.counter ++; // Compliant

    }

}
```
## Lambdas should be replaced with method references

Method/constructor references are more compact and readable than using lambdas, and are therefore preferred. Similarly, null checks can be replaced with references to the Objects::isNull and Objects::nonNull methods.

### Noncompliant Code Example
```java
class A {

    void process(List<A>; list) {

    list.stream()

        .map(a ->; a.<String>;getObject())

        .forEach(a ->; { System.out.println(a); });

    }

    <T> T getObject() {

        return null;

    }

}
```
### Compliant Solution
```java
class A {

    void process(List<A>; list) {

        list.stream()

        .map(A::<String>;getObject)

        .forEach(System.out::println);

    }

    <T> T getObject() {

        return null;

    }

}
```
## URIs should not be hardcoded

Hard coding a URI makes it difficult to test a program: path literals are not always portable across operating systems, a given absolute path may not exist on a specific test environment, a specified Internet URL may not be available when executing the tests, production environment filesystems usually differ from the development environment, ...etc. For all those reasons, a URI should never be hard coded. Instead, it should be replaced by customizable parameter.

### Noncompliant Code Example
```java
public class Foo {

    public Collection<User>; listUsers() {

        File userList = new File("/home/mylogin/Dev/users.txt"); // Non-Compliant

        Collection<User>; users = parse(userList);

        return users;

    }

}
```
### Compliant Solution
```java
public class Foo {

    // Configuration is a class that returns customizable properties: it can be mocked to be injected during tests.

    private Configuration config;

    public Foo(Configuration myConfig) {

        this.config = myConfig;

    }

    public Collection<User>; listUsers() {

        // Find here the way to get the correct folder, in this case using the Configuration object

        String listingFolder = config.getProperty("myApplication.listingFolder");

        // and use this parameter instead of the hard coded path

        File userList = new File(listingFolder, "users.txt"); // Compliant

        Collection<User>; users = parse(userList);

        return users;

    }

}