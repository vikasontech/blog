---
title: "Reactive Programming"
date: 2021-10-28T09:40:51+07:00
draft: true
---

# Introduction

## What is Imperative Programming?

From many years we have write imperative style of coding. It is the oldest paradigm for the programmers. In Imperative style of language, you generatlly focus on the `how` to do certain task. The series of commands are given to the system. The system need to follows that command. 

There are many languages are available in the market with this style of coding like - 
* Java
* Python
* C 
* Ruby 




> What to do, how to do

### ~~example~~

## What is Async Programming?

### ~~example~~

## What is Reactive Programming? 
> Tells, What to do, not how to do 
> Operators 
#### Cons
1. Paradigm  shift
1. 

### example

####  Exception Handling 

```
package org.springreactive;

import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;
import org.springreactive.SamplePrograms.Person;
import reactor.core.publisher.Flux;

import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class SampleProgramsTest {


  final List<SamplePrograms.Person> peoples =
      Arrays.asList(
          SamplePrograms.Person.builder().age(20).name("sam").build(),
          SamplePrograms.Person.builder().age(35).build(),
          SamplePrograms.Person.builder().age(25).name("ved").build(),
          SamplePrograms.Person.builder().age(18).name("vik").build(),
          SamplePrograms.Person.builder().age(45).name("ong").build());
  /* functional way using streams */
  public void ageGreaterThanTake3(int age) {

    final Flux<String> map = Flux.fromIterable(peoples)
        .filter(it -> it.getAge() >= age)
        .checkpoint("got the data")
        .map(SamplePrograms.Person::getName)
        .checkpoint("got the name")
        .map(String::toUpperCase)
        .map(String::toLowerCase);

    Consumer<Throwable> errorHandler = e -> System.err.println(
        "ERROR: " + e.getMessage());

    final Runnable completer = () -> System.out.println("Done");

    map.subscribe(System.out::println, errorHandler, completer);

  }

  /* doesn't want max age */
  private void ageGreaterThan(int age) {
    if (peoples.isEmpty()) {
      System.out.println("no data found");
    }

    String name = null;
    for (Person p : peoples) {
      if (p.getAge() >= age) {
        name = p.getName();
        break;
      }
    }

    if (name != null) {
      System.out.println("Name of the Person is:  " + name);
    }
  }

  /* doesn't want max age */
  /* functional way using streams */
  private void ageGreaterThanTake2(int age) {

    String name = peoples.stream()
        .filter(it -> it.getAge() >= age)
        .map(Person::getName)
        .map(String::toUpperCase)
        .map(String::toLowerCase)
        .findFirst()
        .orElse("No data found");

    System.out.println(name);
  }


  @Test
  @Order(1)
  void testOlderFinder_Imperative() {
    ageGreaterThanTake2(32);
  }
  @Test
  @Order(2)
  void testOlderFinder_ImperativeWithStrams() {
    ageGreaterThanTake2(32);
  }

  @Test
  @Order(3)
  void testOlderFinder_Reactive() {
      ageGreaterThanTake3(32);
  }
}
```

testOlderFinder_Imperative

![Imperative](https://tinyimg.io/i/84GMJYQ.png_temp)

testOlderFinder_ImperativeWithStrams

![Imperative Streams](https://tinyimg.io/i/S8mt3sg.png_temp)

testOlderFinder_Reactive

![Imperative Reactive](https://tinyimg.io/i/pLiPGC1.png_temp)

---

### Pillars of Reactive Programming
* Response
* Reactive
* Elastic
* Message-Driven

## Why I need to do reactive programming? 

---

# DEBUGGING


# Notes

1. pros & cons
1. talk about the data streams
1. Exception handling
    1. Backpressure
    1. handle at downstream
1. blocking calls

---

The four pillars of object-oriented programming are:

* Abstraction
* Encapsulation
* Inheritance
* Polymorphism

--- 

# Reference
https://www.reactivemanifesto.org/

https://blog.redelastic.com/what-is-reactive-programming-bc9fa7f4a7fc

https://www.softwaretestinghelp.com/juni-test-execution-order/

