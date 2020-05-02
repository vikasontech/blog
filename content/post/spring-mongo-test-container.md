+++ 
draft = false
date = 2020-01-18T07:15:08+07:00
title = "Spring+kotlin+mongo+testcontainer"
description = "Example of using test container with kotlin, testcontainer and mongodb"
slug = "spring+kotlin+mongo+testcontainer"
tags = ["testcontainer","spring","kotlin","mongo"]
categories = ["Technical"]
externalLink = ""
series = []
+++

**Introduction**


In this project we shall see how to use [testcontainer](https://www.testcontainers.org/) to test the repository created in [mongodb](https://www.mongodb.com/) As per their document Testcontainers is a Java library that supports JUnit tests, providing lightweight, throwaway instances of common databases, Selenium web browsers, or anything else that can run in a Docker container.


**Prerequisit**

* Docker

**Create sample project**


Go to the [spring initializer](http://start.spring.io) and create project with language as kotlin and dependencies required are `webflux` & `reactive mongo db`


Here we shall create a loan details application, where we will save loan details.

**Dependency for testcontainer**


We need following depencies to add testconainer in the appliation
{{< gist vikasontech 9870bb368b01f5a7ae07148efd2276f8 >}}


**Container Configuration**


Create docker compose file for the test container we shall use the same docker-compose.yml file as we are using for the application, except we shall change the port for the test docker container.
it should look like this-

{{< gist vikasontech 3d2b48ee2c50a390c36a9d57cedb5386 >}}

Note: here we are mapping docker container port at ``1234`` some random port it can be any number that are available.


The file path should be ``src/test/resources/docker-compose-test.yml``

Create a container class that extends docker compose container class be used as mongo container


{{< gist vikasontech f4bc3ac99aa38cdfe130ff572c55f478 >}}


The code is very simple and self explanatory, at ``line:2`` we create a ``DockerComposeContainer`` of type `SELF` ``MongoContainer`` and provide path of test docker compose file.


We created it as a singleton repository and use ``@PostConstruct`` to start the container once the dependency injection is done.


That's all we need to start with testconatainer.




**Create basic structure of the application**



Create a `document` class to save in mongo

{{< gist vikasontech 538fe51780ebe36103dd5f395b5b561d >}}


Create an `repository` to access data from the DB.

{{< gist vikasontech 434440bacb6ae4c8b65d81024274e08f >}}


Create an `service` class to access the loan details.

{{< gist vikasontech e0970e10dfc16f24195a8f932537a0dc >}}


**Create first test case**


Let's create our first test case as below

{{< gist vikasontech a9bd4b673a792f94ca426d1ad988d981 >}}

The test case is preety normal and same we write. There is nothing special we need to do for testcontainers here.


**Run Test**

Before run local test do ensure that the docker is running on your machine.


**Repository**

Create repository and link [here](https://github.com/vikasontech/KotlinWebFluxMongoTestContainer.git/).

