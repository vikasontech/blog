---
title: "Ratelimiter With Resilience4j Spring Boot2"
date: 2020-06-28T00:55:12+07:00
description: "Ratelimiter example with spring-boot and Resilience4j"
tags: ["spring-boot","resilience4j","ratelimiter","example"]
categories: ["Technical"]
draft: false 
---

***Introduction***

Rate limiting is technique to help to limit the number of requests or type of request received by a server. It help to scale and increase the reliability of the system. As per resilience4j doc    
> Rate limiting is an imperative technique to prepare your API for scale and establish high availability and reliability of your service. But also, this technique comes with a whole bunch of different options of how to handle a detected limits surplus, or what type of requests you want to limit. You can simply decline this over limit request, or build a queue to execute them later or combine these two approaches in some way.

---

***Version Details***

* **spring-boot:2.2.6.RELEASE**
* **spring-cloud:Hoxton.SR4**
* **Resilience4j:1.1.0**
* **Java:11** 
* **Kotlin:1.3.71**
 
---

***Dependencies***

We need to add the following dependencies in the `pom.xml` file - 

* resilience4j-reactor
* resilience4j-circuitbreaker
* resilience4j-spring-boot2
* spring-boot-starter-actuator
* spring-boot-starter-aop

---

***Configure rate limiter in the application.yml file***

Open `application.yml` and add the following configuration for the `rate limiter`- 

{{< highlight yml >}}
resilience4j.ratelimiter:
  instances:
    processService:
      limitForPeriod: 10
      limitRefreshPeriod: 1s
      timeoutDuration: 0

{{< /highlight >}}


The details of the configuration is as below -  

| Config property	| Default value	| Description
|-----------------|---------------|------------
timeoutDuration	| 5 [s]	|The default wait time a thread waits for a permission
limitRefreshPeriod	| 500 [ns]	| The period of a limit refresh. After each period the rate limiter sets its permissions to count back to the `limitForPeriod` value
limitForPeriod | 50	| The number of permissions available during one limit refresh period

---

***Add rate limiter to the service***

I created a simple service that takes no arguments, and return some string mono. We will add the `@RateLimiter` annotation, and pass the config name, fallback method name that gett calls in case of request denied by the rate limiter. 

{{< highlight java >}}

@RateLimiter(name="processService", fallbackMethod = "processFallback")
fun process(): Mono<String> {
  return Mono.just("Hello World ...")
}

{{< /highlight >}}
  
The fallback method name is `processFallback`. The method should be in the same class with the same signature but with an extra parameter for the `Throwable` class for the exception handling.

So fallback method should look like this 

{{< highlight java >}}
fun processFallback(exp: Throwable): Mono<String> {
  log.error("eh!!! this is the error ${exp.localizedMessage}")
  return Mono.just("inside from fallback method because `${exp.localizedMessage}`")
}
{{< /highlight >}}

---

***Create controller class***

The controller class should accept a get request and return the response Mono -

{{< highlight java >}}
@GetMapping("/process", produces = [MediaType.TEXT_EVENT_STREAM_VALUE])
fun processFallback(exp: Throwable): Mono<String> {
  log.error("eh!!! this is the error ${exp.localizedMessage}")
  return Mono.just("inside from fallback method because `${exp.localizedMessage}`")
}
{{< /highlight >}}

---

***Run Application***

Let's check the status of the available permission for this service. 

![health status](https://l6w3wq.ch.files.1drv.com/y4mtm5nBYEJygGMutdZoW1Ra-_A5iimrNJTt1ba6n2HPdjMmL8D-fO-e1McOQPuZyCxX1Qh20E8XXQljhGSFQUO8pN12REYNqD9AhC_ROe7LrpxFPMNdM-4qmc_O65vX3bqmHVKMfO97gzPKW2a5S19X3LPaG4Mojcu7_KdypNsxRo-JntupLYM9dE9iFjBYQ-GELQljBzMvS43jvH72nPqZw?width=660&height=363&cropmode=none)

We have only one permission, now run the following script on the terminal 

{{< highlight bash >}}
http :8080/process
{{< /highlight >}}

**Output**

{{< highlight bash >}}
$ http :8080/process
HTTP/1.1 200 OK
Content-Type: text/event-stream;charset=UTF-8
transfer-encoding: chunked

data:ah what do you want ...

{{< /highlight >}}

**Rate limiter permission details**

![Rate limiter status](https://lqw3wq.ch.files.1drv.com/y4mwcmpdFIRYiNcVModlFZret7MP6gBxiX_y0Ckasblcb2Uey_VcRe-qSrFjrotF8ustGP80BfoOGwxjVdS9Ik36jCeNyBrfLooELHej4nJVtRnZkB_rqL2CpgZi1miurq9j59KmlD2o05aI5ro1yXo8BQ_AXV0thsydWkpsFvSXNS7aussMROXnLDPpPqiLhyg4bY_Hk52DrKzVeBzU3tTDQ?width=660&height=363&cropmode=none)

Now, this service has zero permission; now call it again and see, the call should reject now - 

{{< highlight bash >}}
http :8080/process
{{< /highlight >}}

**Output**

{{< highlight bash >}}

$ http :8080/process
HTTP/1.1 200 OK
Content-Type: text/event-stream;charset=UTF-8
transfer-encoding: chunked

data:inside from fallback method because `RateLimiter 'processService' does not permit further calls`
{{< /highlight >}}

---

***Source Code***

The full source code is available at [GitHub](https://github.com/vikasontech/spring-resilience4j-rate-limiter-demo.git)

---

***Reference***
- https://resilience4j.readme.io/docs/getting-started-3
- https://resilience4j.readme.io/docs/ratelimiter

