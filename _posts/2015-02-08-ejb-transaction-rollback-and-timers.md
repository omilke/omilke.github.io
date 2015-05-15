---
layout: post
title:  "EJB Transaction Rollback and Timers"
date:   2015-02-08 08:30:00
categories: Java JavaEE EJB Glassfish
---

### Introduction
Today I was a little confused when a colleague of mine tried to explain to me the transaction control directives our application depends on. The tough part was to consider all the
    possible rollback scenarios. To get more insight into rollback behaviour, I created a simple demo project.

The project can be found on [github][1]. It demonstrates all possible cases I could think of with respect to transaction rollback
    and programmatic timer manipulation. I have added TODO markers in places you choose between different scenarios. I also added a comment on what is expected to happen and why.

It works with glassfish 3.2 and 4.1. Feel free to try it out and please share your thoughts. Please keep in mind, however, this project
    was primarily designed to run on both versions of glassfish.

The specification (JSR-318 - EJB 3.1) can found at the [JCP site][2]. I added references to specification, which relate to this document.

### EJB Transaction Rollback - When does it happen?
My primitive assumption was, that if the method that spans a transaction (e. g. by declaring `TransactionAttribute.REQUIRES_NEW`) terminates with an exception,
    the associated transaction must be rolled back. However, this is not necessarily true.

Actually it depends on the type of the exception. Transactions only rollback if the exception being thrown is marked for rollback (cf. chapter 14.3.1, Table 15).
By the default, all checked exceptions are marked as `rollback=false`, whereas the unchecked exception are marked as `rollback=true`.
    You can explicitly specify rollback behaviour on exceptions with `@ApplicationException(rollback = true / false)`.
If you have an EJB that uses another EJB injected via @EJB / @Inject and the invoked method executes of the same transaction (e. g. because of
    `TransactionAttribut.REQUIRED)`, this method can also induce a rollback of the transaction if it throws an appropriate exception.
This means a business method may throw an exception and have the transaction committed at the same time. Conversely, by using another EJB a transaction can be
    rolled back even if you catch its exception. Both of these cases are shown in the demo project.

### EJB Transaction Rollback and timers

Transaction rollback also influences timeout methods (i. e. methods annotated with `@Schedule` / `@Timeout`). If the transaction used for a timeout method shall
    be rolled back, it is required by the specification, that the method invocation will be retried at least once (cf. chapter 18.4.3). Glassfish retries the invocation 5 seconds later. However,
    if the second invocation induces a rollback as well, the timer will be expunged. That means you will loose all future invocation of that timer.

The specification actually disallows timeout methods to throw application exceptions (cf. chapter 18.2.5.3), however glassfish can cope with that. Timeout method invocation is retried if either
    the method throws an exception or the associated transaction is marked for rollback. A timeout method can also have a committed transaction even though it throws an exception.
    However, since this is not allowed by the spec, its behaviour is specific to the application server in use.
For glassfish, retrying timeouts and expunging timer can be configured in the admin interface ([screenshot][3]).

Programmatic manipulations of timers with the help of `@Resource TimerService` are also subject to transaction rollback (cf. chapter 18.2.8). Therefore registering a new
    `@Timeout` or cancelling and an existing `Timer` will also be reverted of the corresponding transaction is rolled back.

[1]: https://github.com/omilke/ta-demo
[2]: https://jcp.org/aboutJava/communityprocess/final/jsr318/index.html
[3]: /img/glassfish/timerconfig.png
