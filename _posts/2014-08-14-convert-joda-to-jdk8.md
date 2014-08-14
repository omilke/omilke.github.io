---
layout: post
title:  "Switching from Joda Time to JDK8 Date Time API"
date:   2014-08-14 13:30:30
categories: java jodatime jdk8 glassfish
---

#### Thanks Joda!
Joda-Time is great. Seriously. It has been a pleasure to work with from the beginning. It was amazing how valuable Jodas LocalDates are when you 
simply need exactly that: a date without any consideration of time zones. 

I even wrote a JPA 2.1 `AttributeConverter<LocalDate, Integer>` in order to use the LocalDate as attribute type for my JPA entities and keep mindful table rows 
(i. e. not with a binary blob), that are even sortable. However, this forced me to provide JodaTime as library to the GlassFish (I chose to use the common classloader). 
I cannot precisely recall what went wrong when deploying JodaTime as maven compile time dependency. In fact, I was rudely reminded of the need to put 
JodaTime into the library folder of my GlassFish when I set up my dev environment on a new system. It simply didn't work out of the box.

#### Time to leave
I decided that this incident was a good reason to switch from JodaTime to the new JDK8 Date Time API (JSR-310). With the JDK8 API I would not be forced to deploy a library into the common-classloader of GlassFish, which makes deploying my application easier. Always a good thing.

Knowing that Stephen Colebourne (author of JodaTime) is also part of the spec lead team for JSR-310, I assumed there might not be too many differences between JodaTime and JSR-310.

#### Stepping Over
Back then I found no guide for switching, therefore I tried myself - which wasn't any challenge anyway. But maybe this post helps other people in the same situation.

These are the steps I took to switching to JSR-310

- comment out JodaTime dependency in my pom
- check all syntax errors
  - use netbeans' 'organize imports' feature to import the like-named classes for JSR 310
    -  `org.joda.time.*` -----> `java.time.*`
  - perform following transformations

1. 
    - *old:*    `new LocalDate(year, month, day)` 
    - **new:**  `LocalDate.of(year, month, day)`  
2. 
    - *old:*   `new LocalDate()`
    - **new:** `LocalDate.now()`
3. 
    - *old:*   `LocalDate.fromDateFields(javautildate)`
    - **new:**  `LocalDate.from(javautildate.toInstant())`
4. 
    - *old:*   `localDate.getMonthOfYear()` 
    - **new:** `localDate.getMonthValue()`
4. 
    - *old:*   `DateTimeFormatter.parseLocalDate("formatted date")` 
    - **new:** `LocalDate.from(DateTimeFormatter.parse("formatted date"))` 
5. 
    - *old:*   `localdate.toString(dateTimeFormatter)`
    - **new:** `localDate.format(dateTimeFormatter)`
6. 
    - *old:*   `DateTimeFormat.forPattern("pattern")`
    - **new:** `DateTimeFormatter.ofPattern("pattern")`
7. 
    - *old:*   `DateTimeFormatter.print(localdate)`
    - **new:** `DateTimeFormatter.format(localdate)`
8. 
    - *old:*   `"MMMMM"` (5 M) (formatting pattern for full month)
    - **new:** `"MMMM"` (4 M)

- run tests + test run ;-)
- remove all (now) *legacy* references
    - Joda-Time in pom
    - Joda-Time in common cloasloader of GlassFish



The last one was tricky, but luckily I had a unit test to save me. Also note that I avoided using lambas since that is not possible out of the box with GlassFish 4.0 (though [Oracle stated][1] they will introduce Java 8 support with GlassFish 4.1 ([should be out by Java One 2014][2])).

Do you have any comments, corrections or additions? Please reply on [twitter][3]

#####PS
If any of you has an idea for a more readable transformation list, please share your thoughts as well. This is my first try with markdown.

[1]: https://blogs.oracle.com/theaquarium/entry/spotlight_on_glassfish_4_1
[2]: https://blogs.oracle.com/theaquarium/entry/glassfish_4_0_1_update
[3]: https://twitter.com/OliverMilke/status/499912971238518784