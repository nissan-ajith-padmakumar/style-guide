# Collections

## Collection methods with O(n) performance should be used carefully

The time complexity of method calls on collections is not always obvious. For instance, for most collections the size() method takes constant time, but the time required to execute ConcurrentLinkedQueue.size() is O(n), i.e. directly proportional to the number of elements in the collection. When the collection is large, this could therefore be an expensive operation.

This rule raises an issue when the following O(n) methods are called outside of constructors on class fields:

- ArrayList
  - contains
  - remove
- LinkedList
  - get
  - contains
- ConcurrentLinkedQueue
  - size
  - contains
- ConcurrentLinkedDeque
  - size
  - contains
- CopyOnWriteArrayList
  - add
  - contains
  - remove
- CopyOnWriteArraySet
  - add
  - contains
  - remove

### Noncompliant Code Example
```java
ConcurrentLinkedQueue queue = new ConcurrentLinkedQueue();

//...

log.info('Queue contains ' + queue.size() + 'elements'); // Noncompliant
```
## Declarations should use Java collection interfaces such as "List" rather than specific implementation classes such as "LinkedList"

The purpose of the Java Collections API is to provide a well defined hierarchy of interfaces in order to hide implementation details.

Implementing classes must be used to instantiate new collections, but the result of an instantiation should ideally be stored in a variable whose type is a Java Collection interface.

This rule raises an issue when an implementation class:

- is returned from a public method.
- is accepted as an argument to a public method.
- is exposed as a public member.

### Noncompliant Code Example
```java
public class Employees {

    private HashSet\<Employee> employees = new HashSet<Employee>(); // Noncompliant - "employees" should have type "Set" rather than "HashSet"

    public HashSet<Employee> getEmployees() { // Noncompliant

        return employees;

    }

}
```
### Compliant Solution
```java
public class Employees {

    private Set<Employee> employees = new HashSet<Employee>(); // Compliant

    public Set<Employee> getEmployees() { // Compliant

        return employees;

    }

}
```
## Synchronized classes Vector, Hashtable, Stack and StringBuffer should not be used

Early classes of the Java API, such as Vector, Hashtable and StringBuffer, were synchronized to make them thread-safe. Unfortunately, synchronization has a big negative impact on performance, even when using these collections from a single thread.

It is better to use their new unsynchronized replacements:

- ArrayList or LinkedList instead of Vector
- Deque instead of Stack
- HashMap instead of Hashtable
- StringBuilder instead of StringBuffer

### Noncompliant Code Example
```java
Vector cats = new Vector();
```
### Compliant Solution
```java
ArrayList cats = new ArrayList();
```
## Exceptions

Use of those synchronized classes is ignored in the signatures of overriding methods.
```java
@Override

public Vector getCats() {...}
```

## Arrays should not be copied using loops

Using a loop to copy an array or a subset of an array is simply wasted code when there are built-in functions to do it for you. Instead, use Arrays.copyOf to copy an entire array into another array, use System.arraycopy to copy only a subset of an array into another array, and use Arrays.asList to feed the constructor of a new list with an array.

Note that Arrays.asList simply puts a Collections wrapper around the original array, so further steps are required if a non-fixed-size List is desired.

### Noncompliant Code Example
```java
public void makeCopies(String[] source) {

    this.array = new String[source.length];

    this.list = new ArrayList(source.length);

    for (int i = 0; i < source.length; i++) {

        this.array[i] = source[i]; // Noncompliant

    }

    for (String s : source) {

        this.list.add(s); // Noncompliant

    }

}
```
### Compliant Solution
```java
public void makeCopies(String[] source) {

    this.array = Arrays.copyOf(source, source.length);

    Collections.addAll(this.list, source);

}
```
## Collection sizes and array length comparisons should make sense

The size of a collection and the length of an array are always greater than or equal to zero. So testing that a size or length is greater than or equal to zero doesn&#39;t make sense, since the result is always true. Similarly testing that it is less than zero will always return false. Perhaps the intent was to check the non-emptiness of the collection or array instead.

### Noncompliant Code Example
```java
if (myList.size() >= 0) { ... }

if (myList.size() < 0) { ... }

boolean result = myArray.length >= 0;

if (0 > myArray.length) { ... }
```
### Compliant Solution
```java
if (!myList.isEmpty()) { ... }

if (myArray.length >= 42) { ... }
```
## Empty arrays and collections should be returned instead of null

Returning null instead of an actual array or collection forces callers of the method to explicitly test for nullity, making them more complex and less readable.

Moreover, in many cases, null is used as a synonym for empty.

### Noncompliant Code Example
```java
public static List<Result> getResults() {

    return null; // Noncompliant

}

public static Result[] getResults() {

    return null; // Noncompliant

}

public static void main(String[] args) {

    Result[] results = getResults();

    if (results != null) { // Nullity test required to prevent NPE

        for (Result result: results) {

            /\* ... \*/

        }

    }

}
```
### Compliant Solution
```java
public static List<Result> getResults() {

    return Collections.emptyList(); // Compliant

}

public static Result[] getResults() {

    return new Result[0];

}

public static void main(String[] args) {

    for (Result result: getResults()) {

        /\* ... \*/

    }

}
```
## "collect" should be used with "Streams" instead of "list::add"

While you can use either forEach(list::add) or collect with a Stream, collect is by far the better choice because it&#39;s automatically thread-safe and parallellizable.

### Noncompliant Code Example
```java
List<String> bookNames = new ArrayList<>();

books.stream().filter(book -> book.getIsbn().startsWith("0"))

.map(Book::getTitle)

.forEach(bookNames::add); // Noncompliant
```
### Compliant Solution
```java
List<String> bookNames = books.stream().filter(book -> book.getIsbn().startsWith("0"))

.map(Book::getTitle)

.collect(Collectors.toList());
```
## Consumed Stream pipelines should not be reused

Stream operations are divided into intermediate and terminal operations, and are combined to form stream pipelines. After the terminal operation is performed, the stream pipeline is considered consumed, and cannot be used again. Such a reuse will yield unexpected results.