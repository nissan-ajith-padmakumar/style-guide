# Dates

## "java.time" classes should be used for dates and times


The old, much-derided Date and Calendar classes have always been confusing and difficult to use properly, particularly in a multi-threaded context. JodaTime has long been a popular alternative, but now an even better option is built-in. Java 8&#39;s JSR 310 implementation offers specific classes for:

| **Class** | **Use for** |
| --- | --- |
| LocalDate | a date, without time of day, offset, or zone |
| LocalTime | the time of day, without date, offset, or zone |
| LocalDateTime | the date and time, without offset, or zone |
| OffsetDate | a date with an offset such as +02:00, without time of day, or zone |
| OffsetTime | the time of day with an offset such as +02:00, without date, or zone |
| OffsetDateTime | the date and time with an offset such as +02:00, without a zone |
| ZonedDateTime | the date and time with a time zone and offset |
| YearMonth | a year and month |
| MonthDay | month and day |
| Year/MonthOfDay/DayOfWeek/... | classes for the important fields |
| DateTimeFields | stores a map of field-value pairs which may be invalid |
| Calendrical | access to the low-level API |
| Period | a descriptive amount of time, such as "2 months and 3 days" |

### Noncompliant Code Example
```java
Date now = new Date(); // Noncompliant

DateFormat df = new SimpleDateFormat("dd.MM.yyyy");

Calendar christmas = Calendar.getInstance(); // Noncompliant

christmas.setTime(df.parse("25.12.2020"));
```
### Compliant Solution
```java
LocalDate now = LocalDate.now(); // gets calendar date. no time component

LocalTime now2 = LocalTime.now(); // gets current time. no date component

LocalDate christmas = LocalDate.of(2020,12,25);
```

## Week Year ("YYYY") should not be used for date formatting


Few developers are aware of the difference between Y for "Week year" and y for Year when formatting and parsing a date with SimpleDateFormat.
That&#39;s likely because for most dates, Week year and Year are the same, so testing at any time other than the first or last week of the year will yield the same value for both y and Y. But in the last week of December and the first week of January, you may get unexpected results

### Noncompliant Code Example
```java
Date date = new SimpleDateFormat("yyyy/MM/dd").parse("2015/12/31");

String result = new SimpleDateFormat("YYYY/MM/dd").format(date); //Noncompliant; yields &#39;2016/12/31&#39;
```


### Compliant Solution
```java
Date date = new SimpleDateFormat("yyyy/MM/dd").parse("2015/12/31");

String result = new SimpleDateFormat("yyyy/MM/dd").format(date); //Yields &#39;2015/12/31&#39; as expected
```