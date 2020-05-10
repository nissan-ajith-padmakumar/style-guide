# Exception Handling

## Exception should not be created without being thrown

Creating a new Throwable without actually throwing it is useless and is probably due to a mistake.

## Exception types should not be tested using "instanceof" in catch blocks

Multiple catch blocks of the appropriate type should be used instead of catching a general exception, and then testing on the type.

### Noncompliant Code Example
```java
try {

    /\* ... \*/

} catch (Exception e) {

if(e instanceof IOException) { /\* ... \*/ } // Noncompliant

if(e instanceof NullPointerException{ /\* ... \*/ } // Noncompliant

}
```
### Compliant Solution
```java
try {

    /\* ... \*/

} catch (IOException e) { /\* ... \*/ } // Compliant


```
## Try-catch blocks should not be nested

Nesting try/catch blocks severely impacts the readability of source code because it makes it too difficult to understand which block will catch which exception.

## "catch" clauses should do more than rethrow

A catch clause that only rethrows the caught exception has the same effect as omitting the catch altogether and letting it bubble up automatically, but with more code and the additional detriment of leaving maintainers scratching their heads.

Such clauses should either be eliminated or populated with the appropriate logic.

### Noncompliant Code Example
```java
public String readFile(File f) {

    StringBuilder sb = new StringBuilder();

    try {

        FileReader fileReader = new FileReader(fileName);

        BufferedReader bufferedReader = new BufferedReader(fileReader);

        while((line = bufferedReader.readLine()) != null) {

    //...

    }

    catch (IOException e) { // Noncompliant

        throw e;

    }

    return sb.toString();

}
```
### Compliant Solution
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
## Resources should be closed

Connections, streams, files, and other classes that implement the Closeable interface or its super-interface, AutoCloseable, needs to be closed after use. Further, that close call must be made in a finally block otherwise an exception could keep the call from being made. Preferably, when class implements AutoCloseable, resource should be created using "try-with-resources" pattern and will be closed automatically.

### Noncompliant Code Example
```java
private void readTheFile() throws IOException {

    Path path = Paths.get(this.fileName);

    BufferedReader reader = Files.newBufferedReader(path, this.charset);

    // ...

    reader.close(); // Noncompliant

    // ...

    Files.lines("input.txt").forEach(System.out::println); // Noncompliant: The stream needs to be closed

}

private void doSomething() {

    OutputStream stream = null;

    try {

        for (String property : propertyList) {

            stream = new FileOutputStream("myfile.txt"); // Noncompliant

            // ...

        }

    } catch (Exception e) {

    // ...

    } finally {

        stream.close(); // Multiple streams were opened. Only the last is closed.

    }

}
```
### Compliant Solution
```java
private void readTheFile(String fileName) throws IOException {

    Path path = Paths.get(fileName);

    try (BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF\_8)) {

    reader.readLine();

    // ...

    }

    // ..

    try (Stream<String> input = Files.lines("input.txt")) {

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
## Exception handlers should preserve the original exceptions

When handling a caught exception, the original exception&#39;s message and stack trace should be logged or passed forward.

### Noncompliant Code Example
```java
try {

    /\* ... \*/

} catch (Exception e) { // Noncompliant - exception is lost

    LOGGER.info("context");

}

try {

    /\* ... \*/

} catch (Exception e) { // Noncompliant - exception is lost (only message is preserved)

    LOGGER.info(e.getMessage());

}

try {

    /\* ... \*/

} catch (Exception e) { // Noncompliant - original exception is lost

    throw new RuntimeException("context");

}
```
### Compliant Solution
```java
try {

    /\* ... \*/

} catch (Exception e) {

    LOGGER.info(e); // exception is logged

}

try {

    /\* ... \*/

} catch (Exception e) {

    throw new RuntimeException(e); // exception stack trace is propagated

}

try {

    /\* ... \*/

} catch (RuntimeException e) {

    doSomething();

    throw e; // original exception passed forward

} catch (Exception e) {

    throw new RuntimeException(e); // Conversion into unchecked exception is also allowed

}
```
## Exceptions

InterruptedException, NumberFormatException, DateTimeParseException, ParseException and MalformedURLException exceptions are arguably used to indicate nonexceptional outcomes. Similarly, handling NoSuchMethodException is often required when dealing with the Java reflection API.

Because they are part of Java, developers have no choice but to deal with them. This rule does not verify that those particular exceptions are correctly handled.
```java
int myInteger;

try {

    myInteger = Integer.parseInt(myString);

} catch (NumberFormatException e) {

    // It is perfectly acceptable to not handle "e" here

    myInteger = 0;

}
```
## Generic exceptions should never be thrown

Using such generic exceptions as Error, RuntimeException, Throwable, and Exception prevents calling methods from handling true, system-generated exceptions differently than application-generated errors.

### Noncompliant Code Example
```java
public void foo(String bar) throws Throwable { // Noncompliant

    throw new RuntimeException("My Message"); // Noncompliant

}
```
### Compliant Solution
```java
public void foo(String bar) {

    throw new MyOwnRuntimeException("My Message");

}
```