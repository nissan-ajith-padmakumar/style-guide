# String Handling

## Strings and Boxed types should be compared using "equals()"

It&#39;s almost always a mistake to compare two instances of java.lang.String or boxed types like java.lang.Integer using reference equality == or !=, because it is not comparing actual value but locations in memory.

### Noncompliant Code Example
```java
String firstName = getFirstName(); // String overrides equals

String lastName = getLastName();

if (firstName == lastName) { ... }; // Non-compliant; false even if the strings have the same value
```
### Compliant Solution
```java
String firstName = getFirstName();

String lastName = getLastName();

if (firstName != null && firstName.equals(lastName)) { ... };
```
## "hashCode" and "toString" should not be called on array instances

While hashCode and toString are available on arrays, they are largely useless. hashCode returns the array&#39;s "identity hash code", and toString returns nearly the same value. Neither method&#39;s output actually reflects the array&#39;s contents. Instead, you should pass the array to the relevant static Arrays method.
```java
String argStr = Arrays.toString(args);

int argHash = Arrays.hashCode(args);
```
## Strings should not be concatenated using &#39;+&#39; in a loop

Strings are immutable objects, so concatenation doesn&#39;t simply add the new String to the end of the existing string. Instead, in each loop iteration, the first String is converted to an intermediate object type, the second string is appended, and then the intermediate object is converted back to a String. Further, performance of these intermediate operations degrades as the String gets longer. Therefore, the use of StringBuilder is preferred.

### Noncompliant Code Example
```java
String str = "";

for (int i = 0; i < arrayOfStrings.length ; ++i) {

    str = str + arrayOfStrings[i];

}
```
### Compliant Solution
```java
StringBuilder bld = new StringBuilder();

for (int i = 0; i < arrayOfStrings.length; ++i) {

    bld.append(arrayOfStrings[i]);

}
```

## "toString()" should never be called on a String object

### Noncompliant Code Example
```java
String message = "hello world";

System.out.println(message.toString()); // Noncompliant;
```
### Compliant Solution
```java
String message = "hello world";

System.out.println(message);
```
## Strings literals should be placed on the left side when checking for equality

It is preferable to place string literals on the left-hand side of an equals() or equalsIgnoreCase() method call.

This prevents null pointer exceptions from being raised, as a string literal can never be null by definition.

### Noncompliant Code Example
```java
String myString = null;

System.out.println("Equal? " + myString.equals("foo")); // Noncompliant; will raise a NPE

System.out.println("Equal? " + (myString != null && myString.equals("foo"))); // Noncompliant; null check could be removed
```
### Compliant Solution
```java
System.out.println("Equal?" + "foo".equals(myString)); // properly deals with the
```
## "toString()" and "clone()" methods should not return null

toString() or clone() on an object should always return a string or an object. Returning null instead contravenes the method&#39;s implicit contract.

### Noncompliant Code Example
```java
public String toString () {

    if (this.collection.isEmpty()) {

        return null; // Noncompliant

    } else {

// ...
```
### Compliant Solution
```java
public String toString () {

    if (this.collection.isEmpty()) {

    return "";

    } else {

// ...
```





