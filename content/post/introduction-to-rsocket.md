  
---
title: "Introduction to RSocket"
date: 2021-11-03T14:04:08+07:00
draft: true
---

# What is RScoket?

RSocket is a communication protocol similar to other  RPC protocols for communication between client and server. It is built on the top of the Web socket. While a web socket is a network level protocol, RSOCKET is the application level protocol with framing, session resumption and back-pressure built in over the network. RSocket protocol supports the duplex connections which means that two or more systems can communicate to each other in both directions. 

# Difference with HTTP

The HTTP protocol is one directional, which means only the client can initiate the connection, while in RSocket the server can also initiate the connections. The RSocket provides many features on top of the request and response protocol.

# Features of RSocket

The top features or the RSockets is as follows - 
1. It is based on the Reactive Streams. 
1. It is an application layer protocol.
1. It supports back pressures, session resumptions and protocol with framing.
1. It also supports the fire and forget feature. The client does not have to wait for the response and it will continue their work after sending the request 

# Spring boot configurations for RSocket

[Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#application-properties.rsocket)

![Cofiguration](https://tinyimg.io/i/oVPukke.png)



