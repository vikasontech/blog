+++
title = "Spring Boot Resilience4j Circuitbreaker Annotation Example"
date = 2020-05-03T23:11:17+07:00
draft = false 
description = "Blog on how to use Resillien4j, with Kotlin, Spring boot and webflux with annotation"
slug = "spring-boot-resilience4j-circuitbreaker-annotation-example" 
tags = ["spring-boot","resilience4j","circuitbreaker","example"]
categories = ["Technical"]
externalLink = ""
+++

***Introduction***

This is continuation of my **[previous blog]({{< ref "spring-boog-resilience4j-circuitbreaker-example.md" >}})** on Resilience4j. In this blog, we shall try to use the annotation and external configuration for the circuit breaker implementation.

So let's start by creating a basic application. Our application would have one controller and one service class. 

We already saw how to make the reactive basic application in a  previous blog. Now we will continue from that.

---

***Version Details***

* **spring-boot:2.2.6.RELEASE**
* **spring-cloud:Hoxton.SR4**
* **Resilience4j:1.1.0**
* **Java:11** 
* **Kotlin:1.3.71**

---

***Adding dependencies***

We need to add the following dependencies in the project -

* resilience4j-reactor
* resilience4j-circuitbreaker
* resilience4j-spring-boot2
* spring-boot-starter-actuator
* spring-boot-starter-aop

---

***Add configuration for the circuit breaker***

Open `application.yml` and add the following configuration for the circuit breaker - 

{{< highlight yml >}}
resilience4j.circuitbreaker:
  instances:
    processService:
      slidingWindowSize: 50
      permittedNumberOfCallsInHalfOpenState: 3
      slidingWindowType: TIME_BASED
      minimumNumberOfCalls: 20
      waitDurationInOpenState: 50s
      failureRateThreshold: 50
{{< /highlight >}}

The detail of the configuration is as below -

`resilience4j.circuitbreaker` 

it is the header of the configuration, the `circuitbreaker` specify that this configuration contains all the configuration for the **circuit breaker**.

`processService`

It is the name of this configuration that would be applied to the service.

`slidingWindowSize`

Configures the size of the sliding window which is used to record the outcome of calls when the CircuitBreaker is closed.

`permittedNumberOfCallsInHalfOpenState`

Configures the number of permitted calls when the CircuitBreaker is half open.

`slidingWindowType`

Configures the type of the sliding window which is used to record the outcome of calls when the CircuitBreaker is closed.

`minimumNumberOfCalls`

Configures the minimum number of calls which are required (per sliding window period) before the CircuitBreaker can calculate the error rate or slow call rate.

`waitDurationInOpenState`

The time that the CircuitBreaker should wait before transitioning from open to half-open.


`failureRateThreshold`

Configures the failure rate threshold in percentage. When the failure rate is equal or greater than the threshold the CircuitBreaker transitions to open and starts short-circuiting calls.

---

***Add circuit broker to the service***

Now modify the service method to add the circuit breaker. For that we need to add the `@CircuitBreaker` annotation at the service method and provide the `callback` method name like this 

{{< highlight java >}}
@CircuitBreaker(name="processService", fallbackMethod = "fallbackProcess")
{{< /highlight >}}

The fallback method name is `fallbackProcess` it should be in the same class and it should have the same signature but with an extra parameter for the `Throwable` class for the exception handling.

So our service method and fallback method should look like this 
{{< highlight java >}}

@CircuitBreaker(name="processService", fallbackMethod = "fallbackProcess")
fun process(id: Int): Mono<String> {
  return if (id < 1) Mono.error{ IllegalArgumentException("oh!! invalid id") }
    else Mono.just("Id Found!")
}

fun fallbackProcess(id: Int, exp: Throwable): Mono<String> {
    log.error("eh!!! this is the error ${exp.localizedMessage}")
    return Mono.just("From fallback method: Id Not found ")
}
{{< /highlight >}}

---

***Run Application***

Let's start the application and run the following script on the terminal 

{{< highlight bash >}}
http :8080/process?id=1
{{< /highlight >}}

**Output**
{{< highlight bash >}}

$ http :8080/process?id=1
HTTP/1.1 200 OK
Content-Type: text/event-stream;charset=UTF-8
transfer-encoding: chunked

data:Id Found!
$ 
{{< /highlight >}}

Now let's try to fail the service sending the `id` param value as less than `1` as below

{{< highlight bash >}}
http :8080/process?id=0
{{< /highlight >}}

**Output**
{{< highlight bash >}}

$ http :8080/process?id=0
HTTP/1.1 200 OK
Content-Type: text/event-stream;charset=UTF-8
transfer-encoding: chunked

data:From fallback method: Id Not found
$ 

{{< /highlight >}}

---

***Source Code***

The full source code is available at [GitHub](https://github.com/vikasontech/spring-resillencerj-spring-boot-demo.git)

---

***Reference***
- https://resilience4j.readme.io/docs/getting-started-3
- https://resilience4j.readme.io/docs/circuitbreaker
