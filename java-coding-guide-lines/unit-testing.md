# Unit Testing

## Tests should include assertions

A test case without assertions ensures only that no exceptions are thrown. Beyond basic runnability, it ensures nothing about the behavior of the code under test.

- JUnit
- Fest 1.x
- Fest 2.x
- Rest-assured 2.0
- AssertJ
- Hamcrest
- Spring&#39;s org.springframework.test.web.servlet.ResultActions.andExpect()
- Eclipse Vert.x
- Truth Framework
- Mockito
- EasyMock
- JMock
- WireMock
- RxJava 1.x
- RxJava 2.x
- Selenide
- JMockit

## Tests should not be ignored

When a test fails due, for example, to infrastructure issues, you might want to ignore it temporarily. But without some kind of notation about why the test is being ignored, it may never be reactivated. Such tests are difficult to address without comprehensive knowledge of the project, and end up polluting their projects.

### Noncompliant Code Example
```java
@Ignore // Noncompliant

@Test

public void testDoTheThing() {

// ...
```
### Compliant Solution
```java
@Test

public void testDoTheThing() {

// ...
```
## Unit tests should throw exceptions

When the code under test in a unit test throws an exception, the test itself fails. Therefore, there is no need to surround the tested code with a try-catch structure to detect failure. Instead, you can simply move the exception type to the method signature.

This rule raises an issue when there is a fail assertion inside a catch block.

### Noncompliant Code Example
```java
@Test

public void testMethod() {

    try {

        // Some code

    } catch (MyException e) {

        Assert.fail(e.getMessage()); // Noncompliant

    }

}
```
### Compliant Solution
```java
@Test

public void testMethod() throws MyException {

    // Some code

}
```