---
title: "Reactive Programming"
date: 2021-10-28T09:40:51+07:00
draft: true
---


# Introduction

## What is Imperative Programming?

For many years we have written an imperative style of coding. It is the oldest paradigm for programmers. In Imperative style of language, you generally focus on the how to do a certain task. The series of commands are given to the system. The system needs to follow that command. 

There are many languages available in the market with this style of coding like - 
* Java
* Python
* C 
* Ruby 

## What is Async Programming?

Async programming is like parallel execution of the functions. More than one thread can execute in an isolated manner and notify the main thread when the task is finished. Async programming helps to increase the utilization of the resources, and also reduce the wait time.  However, managing threads can be harder. 

## What is Reactive Programming? 

Reactive programming is the combination of declarative programming and functional programming. It provides the stream of the data that can be processed and forwarded to the downstream functions. Below are are the principles of the reactive streams - 

#### Principles Reactive Programming
* Responsive
* Reactive
* Elastic
* Message-Driven

#### Responsive 

The stream should be responsive, it should be able to respond in case of errors as well. In reactive programming the errors and exceptions are treated as the first class citizen. 
Streams provided three channels 

 * Data channel
 * Complete channel
 * Error Channel

Data channel used for the stream data, Complete channel is used for the stream data in case of the publisher completing the sending the data. The Error channel streams the throne during the processing of the streams.

Steams also helps to increase the resource utilization by removing the blocking code. 

