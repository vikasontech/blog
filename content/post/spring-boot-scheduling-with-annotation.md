---
title: "Spring Boot Scheduling With Annotation"
date: 2017-04-19T19:40:17+07:00
draft: false
tags: [cron, EnableScheduling, scheduler, SchedulingConfigurer, spring-boot]
descriptions: "Spring Boot provides annotation support for task scheduling. It is easy way to develop and run a Task scheduler without using any xml and bean configurations."

---

***Introduction***

Spring Boot provides annotation support for task scheduling. It is easy way to develop and run a Task scheduler without using any xml and bean configurations Simply add the annotation ```@Scheduled``` on the task scheduler method with required interval time. In this example, we will see how to use Spring ```@Scheduled```annotation to schedule a task.

***Enable scheduling annotations***

To enable support for ```@Scheduled``` annotation add ```@EnableScheduling``` to one of your ```@Configuration``` classes:

{{< gist vikasontech d33da357a6c5f8d9e161a80215de5d22>}}

You are free to pick and choose the relevant annotations for your application. For example, for more fine-grained control you can additionally implement the ```SchedulingConfigurer``` interfaces.

***The ```@Scheduled``` annotation***

The ```@Scheduled``` annotation can be added to a method along with trigger metadata. 


**Important**: The simple rules that need to be followed to annotate a method with ```@Scheduled ```are:

- A method should not accept any parameters

- A method should have void return type

For example, the following method would be invoked every 5 seconds with a fixed delay, meaning that the period will be measured from the completion time of each preceding invocation.

{{< gist vikasontech a9b4688ee15607ef3ddeebe42c75e9c7>}}

If a fixed rate execution is desired, simply change the property name specified within the annotation.

The following would be executed every 5 seconds measured between the successive start times of each invocation.

{{< gist vikasontech 0013c2988770200c08fdec8ef455736c>}}

For fixed-delay and fixed-rate tasks, an initial delay may be specified indicating the number of milliseconds to wait before the first execution of the method.

{{< gist vikasontech 7b0a8390db8576a9585047dac7e9fe28>}}

If simple periodic scheduling is not expressive enough, then a cron expression may be provided. For example, the following will only execute on weekdays.

{{< gist vikasontech 0f5c67d6a839843cf968ef26e67f890d>}}
