+++ 
draft = false
date = 2019-12-23T16:22:54+07:00
title = "Scala Mongo example with akka-http and akka-stream"
description = "In this tutorial we shall create and CRUD web application in Scala. We will use MongoDB Scala Driver to communicate to the mongo database. It is officially supported Scala driver for MongoDB. Along with that we shall create a Rest API using akka-http module and akka-streams."
slug = "scala-mongo-example-with-akka-http-and-akka-stream"
tags = ["akka","akka-http","docker","sbt","scala","scala","mongo"]
categories = ["Technical"]
externalLink = ""
series = []
+++

**Introduction**

In this tutorial we shall create and CRUD web application in Scala. We will use [MongoDB Scala Driver](https://docs.mongodb.com/ecosystem/drivers/scala) to communicate to the mongo database. It is officially supported Scala driver for MongoDB. Along with that we shall create a Rest API using [akka-http](https://doc.akka.io/docs/akka-http/current/index.html) module and [akka-streams](https://doc.akka.io/docs/akka/current/stream/index.html).


**Prerequisites**

* Scala version: 2.13.1
* Database: Mongo:3.4.23-xenial

**Other dependencies**

{{<gist vikasontech 00a05c5c70722529d717f5908bb1d462 >}}


**About the service/project**

Application should be able to do CRUD operations on the in mongodb document. This  application should also provide Rest apis to access and modify the data. So it is an employee management. We have employee details in a document called `employee`. We will create reset services to create, update and search the employee data.


**Document Class**

Employee document contains employee name and date of birth.

{{< gist vikasontech d176859653575565b4870087c16cad22 >}}

***Mongo Configuration***

**Codec Registry**

Mongo documents required the codec information for marshal and un-marshal the data. This codecs are already written and available here. This repository have codec implementations for most of the available types. So first create the the java codec as below -
 
{{< gist vikasontech a68ec9ac38a65c38b6449347350dcec1 >}}

In the above code `Employee` is our mongo document class with the following details. 

**Document Class**

Employee document contains employee name and date of birth.

{{< gist vikasontech d176859653575565b4870087c16cad22 >}}
 
Database Details
 
`MongoClientSettings` is the companion object to configure the mongodb settings. 

{{< gist vikasontech ec33914a81f8f71d129cc9a16acf1e5b >}}
 
In the above snippet; A Mongo client settings object is created that contains the configuration details for the connecting mongo database.

**Collection Details**
 
Collection details can be obtained from the database representation we get the previous step
 
{{<gist vikasontech aff2861b3333375066064d92d0848d43 >}}

This above step required for every collection to get the data.
 
**JSON Support**

We need formatter to format the date type to and from JSON format. We used Spray JSON here for this. Spray JSON providing automatic to and from JSON marshalling/un-marshalling using an in-scope *spray-json* protocol.

{{<gist vikasontech e1b4b26a815a05e97f286d145ccce28c >}}
 
**Repository**

Repository contains the create, update, delete and search commands for the employee document. 

{{< gist vikasontech a98e92e6ec7f07794cbfeab48fae3d22 >}}
 
**Actors to get the user data**
Employee actor received messages to perform different operation on the employee document. Actor shall call the actual service and then send the responds to the sender.

{{<gist vikasontech 601add3fae9b2f44a93803da840f62a2 >}}
 
**Routing Configuration**

| Path                        | Details                   |
|-----------------------------|---------------------------|
| GET /api/employee/create    | To create employee data   |
| POST /api/employee/search   | To search employee data   |
| PUT /api/employee/update    | To update employee data   |
| DELETE /api/employee/delete | To delete user datail     |

The routing code should look like this-

{{< gist vikasontech 448b06903269273a7d6439726badad71 >}}

**Web Server**

This class is used to create http server and bind the endpoints with the http server. You can get more information in [my previous blog](http://bit.ly/scalaakkarest).


**Run Application**

Start the mongodb docker container 

{{< highlight html >}}
docker-compose up -d
{{< /highlight >}}

Compile the code using below command


{{< highlight html >}}
sbt compile
{{< /highlight >}}

Run the application

{{< highlight html >}}
sbt run 
{{< /highlight >}}

Now go to terminal, and run below [httpie](https://httpie.org/) commands 


**Create Employee Data**

![create employee](/images/20200119/create-employee-data.png)

**Search Employee Data**
![search employee](/images/20200119/searchEmployeeData.png)

**Update Employee Data**

Check current data in mongodb

![data in database](/images/20200119/currentDataInMongo.png)

Update the data by id 

![update data by id](/images/20200119/updateDataById.png)

Search data after update 

![search data in database](/images/20200119/searchDataAfterUpdate.png)
 
**Delete Employee data**

Search employee data

![search data in database](/images/20200119/searchDeleteEmployeeData.png)

Delete employee data using id

![delete data in database](/images/20200119/deleteEmployeeDataById.png)

Search data after delete

![search data in database](/images/20200119/searchDataAfterDelete.png)

**Github links**

[Full code is available here](http://bit.ly/scala-mongo-crud) to to explore and fork. Feel free to do whatever you want. ;)

**Conclusion**

We have seen here how to use akka http and streams to create rest services in scala. You can get more information here - 

* [akka-http](https://doc.akka.io/docs/akka-http/current/introduction.html)

* [akka-stram](https://doc.akka.io/docs/akka/current/stream/index.html)

* [scala mongo driver](https://docs.mongodb.com/ecosystem/drivers/scala/)
