---
title: "Graceful Shutdown"
date: 2020-10-08T14:38:25+07:00
draft: true
---

# Introduction

Graceful shudown means, in case of system termination, stop receiving new requet and also wait before termiation for the inflight request to finish. Spring provide an shutdownhook with every spring application to achive it. 

# Version Details

* Spring Boot: 2.3.4.RELEASE

# Dependencies

* spring-boot-starter-web

# Implementation 
The implementation is very simple, just create an controller class with one get endpoint. To show long process I have used the sleep method, that will delayed the response by 30 seconds. 
 
{{< gist vikasontech a5e54ec339ddc674b0ddb5525c5014cf>}}

This process will wait till 60 Seconds. We can now shutdown the application now.
I use spring actuator shutdown api to kill the application. you can also kill the application by killing the process, or stop the server from the IDE.

so lets call the shutdown endpoint and see the output - 

```$ http POST :8080/actuator/shutdown
HTTP/1.1 200
Connection: keep-alive
Content-Type: application/vnd.spring-boot.actuator.v3+json
Date: Fri, 16 Oct 2020 10:26:34 GMT
Keep-Alive: timeout=60
Transfer-Encoding: chunked

{
    "message": "Shutting down, bye..."
}

```
Now wait for the application to gracefully shutdown. oh wait i got below null pointer exception - 

```
java.lang.NullPointerException: null
	at org.springframework.boot.actuate.metrics.web.servlet.LongTaskTimingHandlerInterceptor.stopLongTaskTimers(LongTaskTimingHandlerInterceptor.java:123) ~[spring-boot-actuator-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.boot.actuate.metrics.web.servlet.LongTaskTimingHandlerInterceptor.afterCompletion(LongTaskTimingHandlerInterceptor.java:79) ~[spring-boot-actuator-2.2.4.RELEASE.jar:2.2.4.RELEASE]
	at org.springframework.web.servlet.HandlerExecutionChain.triggerAfterCompletion(HandlerExecutionChain.java:179) ```
```

Ohh, now i got this. This is becuase tomcat shuting down while request is still in progress. you can see about this [issue here](https://github.com/spring-projects/spring-boot/issues/16407)

So we need to tell the tomcat to wait for certain time in case any request is still in progress, before shutdown. To do that we need to create a bean for `ServletWebServerFactory` and set the `unloadDelay` time in context like this - 

{{< gist vikasontech 96a941dc6dfeddf08743972695662b97>}}

I set the unload delay time to `2 minutes` here.

now let's re try call the logpass controller end point and then kill the process using actuator shutdown endpoint - 


# Output

The application is waiting to finish. after 60 seconds. I got the responce like this - 

  ## todo: response

The application logs look like this - 

  ## todo: application log 

# Source

# Reference

