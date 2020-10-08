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

While processing the request, kill server using below command - 

    kill ????

The Output is as below - 


# Run

# Try

# Source

# Reference

