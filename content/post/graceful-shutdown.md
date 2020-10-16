---
title: "Graceful Shutdown"
date: 2020-10-08T14:38:25+07:00
draft: true
---

# Introduction

A graceful shutdown means, in case of system termination, stop receiving new requests and also wait before termination for the in-process requests to finish. Spring provides a shutdown hook with every spring application to achieve it.

# Version Details

* Spring Boot: 2.3.4.RELEASE
* Java: 1.8
* Kotlin: 1.3.72

# Dependencies

* spring-boot-starter-web
* spring-boot-starter-actuator (Optional)

# Implementation 

The implementation is simple, just create a controller class with one get endpoint. To show a long process I have used the sleep method, which will delay the response by 30 seconds. 

{{< gist vikasontech a5e54ec339ddc674b0ddb5525c5014cf>}}

To enable graceful shutdown you need to configure `server.shutdown` property, as shown in the following example:

```
server:
  shutdown: GRACEFUL
```

Also to configure the timeout period configure the `spring.lifecycle.timeout-per-shutdown-phase` property, as shown in the following example:

```
spring:
  lifecycle:
    timeout-per-shutdown-phase: 10s
```

See spring refrence document [here](https://docs.spring.io/spring-boot/docs/2.3.4.RELEASE/reference/htmlsingle/#boot-features-graceful-shutdown)

Now endpoint `/test` will take the 60s to finish. We can now shutdown the application now. I use the spring actuator shutdown API to kill the application. you can also kill the application by killing the process or stop the server from the IDE.

Call the shutdown endpoint and see the output - 

```
$ http POST :8080/actuator/shutdown -b
{
    "message": "Shutting down, bye..."
}

```

Now, wait for the application to gracefully shutdown. oh, wait I get null pointer exception -

```
java.lang.NullPointerException: null
	at org.springframework.boot.actuate.metrics.web.servlet.LongTaskTimingHandlerInterceptor.stopLongTaskTimers(LongTaskTimingHandlerInterceptor.java:123) ~[spring-boot-actuator-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.actuate.metrics.web.servlet.LongTaskTimingHandlerInterceptor.afterCompletion(LongTaskTimingHandlerInterceptor.java:79) ~[spring-boot-actuator-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.web.servlet.HandlerExecutionChain.triggerAfterCompletion(HandlerExecutionChain.java:179) ```
```

This is because tomcat shutting down while the request is still in progress. you can see about this [issue here](https://github.com/spring-projects/spring-boot/issues/16407)

So we need to tell the tomcat to wait for a certain time in case any request is still in progress, before shutdown. To do that we need to create a bean for `ServletWebServerFactory` and set the `unloadDelay` time in the context like this -

{{< gist vikasontech 96a941dc6dfeddf08743972695662b97>}}

I set the unload delay time to `2 minutes` here.

Now let's re-try call our test endpoint `/test` endpoint and then kill the process - 

```
$ http POST :8080/actuator/shutdown -b
{
    "message": "Shutting down, bye..."
}

```

# Output

The application is waiting to finish. after 60 seconds. I got the response like this - 

```
$ http :8080/test  --timeout 120
HTTP/1.1 200
Connection: keep-alive
Content-Length: 22
Content-Type: text/plain;charset=UTF-8
Date: Fri, 16 Oct 2020 11:04:19 GMT
Keep-Alive: timeout=60

Thank you for waiting.

$
```

The application logs look like this - 

```
2020-10-16 18:03:19.009  INFO 10997 --- [nio-8080-exec-1] c.e.g.LongPauseController$Companion      : I AM STILL PROCESSING WILL TAKE AROUND 60 SECS. TO FINISH. PLEASE WAIT ....
2020-10-16 18:03:31.188  INFO 10997 --- [       Thread-2] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'
2020-10-16 18:04:19.012  INFO 10997 --- [nio-8080-exec-1] c.e.g.LongPauseController$Companion      : FINALLY! I AM DONE. YOU CAN SHUTDOWN NOW. :)
Disconnected from the target VM, address: '127.0.0.1:54431', transport: 'socket'

Process finished with exit code 0
```

# Source

You can see full source [code here](https://github.com/vikasontech/gracefullshutdown.git).

# Reference

- https://docs.spring.io/spring-boot/docs/2.3.4.RELEASE/reference/htmlsingle/
- https://github.com/spring-projects/spring-boot/issues/16407
