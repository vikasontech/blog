+++ 
draft = false 
date = 2020-05-01T14:45:44+07:00
title = "Spring boot reactive and resilience4j circuit breaker example"
description = "Blog on how to use Resillien4j, with Kotlin, Spring boot and webflux"
slug = "spring-boot-resilience4j-circuitbreaker-example" 
tags = ["spring-boot","resilience4j","circuitbreaker","example"]
categories = ["Technical"]
externalLink = ""
series = ["resilience4j"]
+++

***Introduction***

Resilience4j is a lightweight, easy-to-use fault tolerance library inspired by
Netflix Hystrix, but designed for Java 8 and functional programming. Lightweight, because the library only uses [Vavr](https://www.vavr.io/vavr-docs/), which does not have any other external library dependencies. 

Resilience4j provides higher-order functions (decorators) to enhance any functional interface, lambda expression or method reference with a Circuit Breaker, Rate Limiter, Retry or Bulkhead. You can stack more than one decorator on any functional interface, lambda expression or method reference. The advantage is that you have the choice to select the decorators you need and nothing else.

With Resilience4j you don’t have to go all-in, you can pick what you need.

***Spring boot application configuration***

Today we will create a demo application using `spring boot`, `webflux` and of course `resilience4j` . We will try to add resilience in our application. In case of any exception occured in the target service, we should get a response from the fallback method.

To create demo application we can use [spring boot application initializer](https://start.spring.io/)

We add following dependency in the demo application - 
* Spring Reactive Web
* Spring Boot Actuator 
* Resilience4J 

---

***Create reactive web application***

**Service layer**

Let's start, create a service class called  `SomeService` that have method `SomeService#process` as below 

{{< gist vikasontech 27ab9678ffac6dd33b17eed8b6e1b439>}}

If the `id` value is `1` or more then return `Id found!` else if value is less than `1` then throw `IllegalArgumentException`.

---

**Controller**

Create a controller class called  `SomeController` that has an API endpoint `/process?id=`. It accepts the `id` value in the request param. 

{{< gist vikasontech      
2ede68be98e439714381437b77f3f218 >}}

---

***Run application***

To run the application go to the terminal and run the blow command

`http :8080/process?id=1`

I use *[httpie](https://httpie.org/)* to call the rest api, you can use your choice.

The output should be as below - 

![run application](https://m6w3wq.ch.files.1drv.com/y4mICXx6Qtix6Eh0yHL4B6io9DA9IIT2suajmVd3mL8kqxldg7M3J6krf6W1hn_4M9oy-fw6aXA5sBeHTaZD5HrPUNSpkL9kWjFiheNpCws3pOTYMR7vEQEI_ub8Pl7stS8R75DFUrEa2fVLyFqugLadFH3ze9MZGM4My0__-qcWgCo_iPm7dow1kiI0UxLpiIhjEHOZATA4w8bGl43hUbU2w?width=1954&height=718&cropmode=none)

If you pass `id` value below `1` then the api should give error as below - 

`http :8080/process?id=0`

The output should be as below - 

![run application](https://naw3wq.ch.files.1drv.com/y4my51JPVjGWC0CmZvosfSdoV7TAxqBW8qN_eAILp9YSaR5TpRCPz6Qq6dzNokjs5R8RWYXcma7n_H3hZIXC4RvpeKjQbt-mFxbqCoKishlqVBu9Tlbe5c6zeauwqcw04DY-L0CVe2TUQNU7oLy2LUHSmJdQGnqe9kLDT75xbll5y2mrA9ZiOxAEK7qGmjOG8AKhM2uFsovgfq5GBvoxaWQFQ?width=1954&height=718&cropmode=none)


Now our application is ready, next we need to add Resilience4j in our application. 

In case of any error occured in a third party Api call then we need to return the result from the fallback method.

---

***Add dependency for resilience4j***

Add dependency for resilience4j. We need to add `spring-cloud-starter-circuitbreaker-reactor-resilience4j` for reactive resilience.

{{< highlight xml >}} 
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-circuitbreaker-reactor-resilience4j</artifactId>
</dependency>
{{< /highlight >}}

---

***Configure the Reactive Resilience Circuit breaker factory***

To provide a default configuration for all of your circuit breakers create a `Customize` bean that is passed a `Resilience4JCircuitBreakerFactory` or `ReactiveResilience4JCircuitBreakerFactory`. The `configureDefault` method can be used to provide a default configuration.

{{< gist vikasontech 850ff000a738550e1855ed12bd5f4f53 >}}

---

***Wrap the service call in circuit breaker factory***

Wrap the service call with the circuit breaker factory method like below - 

{{< gist vikasontech 5656517796cd30c23b45c1baae6f0cf0 >}}

***Run application***

Success case 

`http :8080/process?id=1`

![run application](https://m6w3wq.ch.files.1drv.com/y4mICXx6Qtix6Eh0yHL4B6io9DA9IIT2suajmVd3mL8kqxldg7M3J6krf6W1hn_4M9oy-fw6aXA5sBeHTaZD5HrPUNSpkL9kWjFiheNpCws3pOTYMR7vEQEI_ub8Pl7stS8R75DFUrEa2fVLyFqugLadFH3ze9MZGM4My0__-qcWgCo_iPm7dow1kiI0UxLpiIhjEHOZATA4w8bGl43hUbU2w?width=1954&height=718&cropmode=none)

Application fail 

`http :8080/process?id=0`

![run application](https://mqw3wq.ch.files.1drv.com/y4mB5scpkl2oPPlryG_FDk3c53XtJr-uq7Vjq0_FuZiEXA_iWS79DdaSjYlvgeQ-Nhk-i6pUbenWE6x_7R_LbXn0UAsz8xEajyJgXGAVuuEG4lYoMkF1ic5gE2RFRiabKFsZGiEMxQ4eVe4DFIGBW1VWVPWI--R7oRQB6KNrWxpoRorqNHkeZ6zBCfUjtnbxhdZcqdiIKZX8VUTXA3vRZskIg?width=1954&height=718&cropmode=none)

---

***Source code***

The full source code is available at [GitHub](https://github.com/vikasontech/spring-resillencerj-demo.git) 

