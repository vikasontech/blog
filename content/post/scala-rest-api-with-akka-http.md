+++ 
draft = false
categories = ["Technical"]
externalLink = ""
series = []
title = "Scala Rest API with akka-http"
slug = "scala-rest-api-with-akka-http"
date = 2019-12-10T08:56:15+07:00
tags = ["akka","akka-http","rest service","sbt","scala","scala example","web service"]
description = "In this tutorial we shall explore how we can use akka-http to create service with scala. Here we create CRUD endpoints with akka actor and try to see how it work."
+++

**Introduction**

In this tutorial we shall explore how we can use akka-http to create service with scala. Here we create CRUD service with akka actor and try to see how it work.


**Versions**

- Scala version: 2.13.1
- SBT version: 1.3.4

**Other dependencies**

{{< gist vikasontech c6216874027f683731f5d61e2e65de25 >}}

**About the service/project**

We’ll create two actors that communicate with each other to get the user data and user activity data.

![output of the application](/images/20191210/aboutService.png)

**Data Classes**

We need two data classes that keeps the user data and user activity data.

{{<gist vikasontech dc9497796aa9855cf8fe089a810894e8 >}}

**Repository**

Repositories are used to fetch the data from the actual source of data. The actual source can be anything like another service or some database. We created `UserActivityRepository` that have only one method `queryHistoricalActivities` to fetch the user activity by `userId`.


{{<gist vikasontech fb883120dd64646f88f04561b9c7e10b >}}

**Actors to get user data**

We need two actor classes for `UserDataActor` and `UserActivityActor`. `UserDataActor` received 4 types of methods `Get`, `Post`,`Put` and `Delete` to retrieve, create, update and delete the user data respectively.  Once a message received, User data will be sent to the sender asynchronously.

{{<gist vikasontech 98e637ea820c6e696a67243fb84d5d14 >}}

`UserActivityActor` retrieve the user active once it receives the message `Get`


{{< gist vikasontech 50758577160dddd78cd34261e6c08723 >}}

**Routing Configuration**

Now, we need to create routing details, to access the user details from rest endpoints. We needs following endpoints

| Path                    | Detail                                  |
|-------------------------|-----------------------------------------|
| GET /api/users/activity | Retrieve user and its activity details. | 
| POST /api/user          | To create user data                     |
| PUT /api/user           | To update user data                     |
| DELETE /api/user        | To delete user data                     |

*Routing code looks like below -*

{{<gist vikasontech 5c464a8b0b5e278107267ba0d4d38308 >}}

We have two `implicit` parameters in the routing config `implicit val userDataActorRef: ActorRef` and `implicit val system: ActorSystem`. `userDataActorRef` is used to get the user data. Since user details is required to register `UserActivityActor` we use actor system and implicit parameter to register `UserActivityActor` when we have user information available.


**Web Server**

This class is used to create http server and bind the endpoints with the http server.

{{< gist vikasontech bbb78c7509bda0d3597eb3a8b135a954 >}}

***Run Application***

Compile the code using below command
{{< highlight html >}}
sbt compile
{{< /highlight >}}

Run the application

{{< highlight html >}}
sbt run 
{{< /highlight >}}

Now go to terminal, and run below httpie scripts
![output of the application](/images/20191210/output.png)

**Github links**

[Full code is available here](http://bit.ly/2rzPmYH) to explore and fork. Feel free to do whatever you want. ;)

**Conclusion**

We have seen here it’s easy to create rest services with akka and scala. You can get more information at
[akka-http official document](https://doc.akka.io/docs/akka-http/current/introduction.html/).

