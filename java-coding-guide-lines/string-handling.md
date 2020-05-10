# String Handling

## Strings and Boxed types should be compared using &quot;equals()&quot;

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

if (firstName != null &amp;&amp; firstName.equals(lastName)) { ... };
```
## &quot;hashCode&quot; and &quot;toString&quot; should not be called on array instances

While hashCode and toString are available on arrays, they are largely useless. hashCode returns the array&#39;s &quot;identity hash code&quot;, and toString returns nearly the same value. Neither method&#39;s output actually reflects the array&#39;s contents. Instead, you should pass the array to the relevant static Arrays method.
```java
String argStr = Arrays.toString(args);

int argHash = Arrays.hashCode(args);
```
## Strings should not be concatenated using &#39;+&#39; in a loop

Strings are immutable objects, so concatenation doesn&#39;t simply add the new String to the end of the existing string. Instead, in each loop iteration, the first String is converted to an intermediate object type, the second string is appended, and then the intermediate object is converted back to a String. Further, performance of these intermediate operations degrades as the String gets longer. Therefore, the use of StringBuilder is preferred.

### Noncompliant Code Example
```java
String str = &quot;&quot;;

for (int i = 0; i \&lt; arrayOfStrings.length ; ++i) {

    str = str + arrayOfStrings[i];

}
```
### Compliant Solution
```java
StringBuilder bld = new StringBuilder();

for (int i = 0; i \&lt; arrayOfStrings.length; ++i) {

    bld.append(arrayOfStrings[i]);

}
```

## &quot;toString()&quot; should never be called on a String object

### Noncompliant Code Example
```java
String message = &quot;hello world&quot;;

System.out.println(message.toString()); // Noncompliant;
```
### Compliant Solution
```java
String message = &quot;hello world&quot;;

System.out.println(message);
```
## Strings literals should be placed on the left side when checking for equality

It is preferable to place string literals on the left-hand side of an equals() or equalsIgnoreCase() method call.

This prevents null pointer exceptions from being raised, as a string literal can never be null by definition.

### Noncompliant Code Example
```java
String myString = null;

System.out.println(&quot;Equal? &quot; + myString.equals(&quot;foo&quot;)); // Noncompliant; will raise a NPE

System.out.println(&quot;Equal? &quot; + (myString != null &amp;&amp; myString.equals(&quot;foo&quot;))); // Noncompliant; null check could be removed
```
### Compliant Solution
```java
System.out.println(&quot;Equal?&quot; + &quot;foo&quot;.equals(myString)); // properly deals with the
```
## &quot;toString()&quot; and &quot;clone()&quot; methods should not return null

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

    return &quot;&quot;;

    } else {

// ...
```





