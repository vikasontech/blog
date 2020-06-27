---
title: "Spring Reactive Transaction With Mongo"
date: 2020-05-08T21:09:20+07:00
draft: false 
description: "In this blog post we will use ReactiveMongoTransactionManager class to manage the transaction."
tags:  [spring-boot,mongo,reactive,transaction,kotlin]
---

***Introduction***

In this blog post we will use [ReactiveMongoTransactionManager](https://docs.spring.io/spring-data/data-mongodb/docs/current/api/org/springframework/data/mongodb/ReactiveMongoTransactionManager.html) class to manage the transaction. Along with that we shall use the mongo db.


***Version***

- Kotlin: 1.3.71
- Spring-boot: 2.2.6.RELEASEA
- Java: 11
- mongo db: 4.2.0-rc2-bionic

***Dependencies***

You can use [Spring Initializer](https://start.spring.io/) to initialize the project you need following dependencies - 

- Spring Reactive Web 
- Spring Data Reactive MongoDB

***Create `docker-compose.yml` file***

I use docker for running the mongo db the docker compose file looks like this -

{{< highlight yml >}}

version: '3.1'
services:
  mongo:
    container_name: spring-reactive
    image: mongo:4.2.0-rc2-bionic
    ports:
      - 27017:27017
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: example 

{{< /highlight >}}


***Configure MongoDb to the application***


Add the following configurartion in the `application.yml` file.

{{< highlight yml >}}
spring:
  data:
    mongodb:
      uri: mongodb://root:example@localhost:27017
      database: customer
      authentication-database: admin
{{< /highlight >}}


***Create a User document***

Create a User class that would have an `age` field. You shall not allow the user to create an account if the `age` of the user is below `18`.


{{< highlight java >}}

@Document
data class User(
    @Id
    val id: String,
    val age: Int 
)
{{< /highlight >}}



***Create a data repository for User document***


{{< highlight java >}}

interface CustomerRepo : ReactiveCrudRepository<User, String>

{{< /highlight >}}


***Create a service class***

Now create a service class that would have a method called `saveAll`. The `saveAll` method will accept the list of an Int array, create the user object, validate the age and the save data to the database.  


{{< highlight java >}}
fun saveAll(vararg ages: Int): Flux<User> {
  return Flux.fromIterable(ages.asIterable())
      .map {
        User(id = UUID.randomUUID().toString(),
            age = it)
      }
      .doOnNext { Assert.isTrue(it.age >= 18, "Age should be greater or equal to 18") }
      .flatMap { userRepo.save(it) }
}
{{< /highlight >}}


***Make the service transactional***

To add the transactional support to the application, provide two beans `transactionManager` and `transactionOperator` to enable the transactions. 


***Create a transaction manager***

Create a `ReactiveTransactionManager` that will manages the client sessions for the given `ReactiveMongoDatabaseFactory`


{{< highlight java >}}

@Bean
fun transactionManager(rdbf: ReactiveMongoDatabaseFactory): ReactiveMongoTransactionManager {
  return ReactiveMongoTransactionManager(rdbf)
}
{{< /highlight >}}



***Create a transaction operator***

Operator class that simplifies programmatic transaction demarcation and transaction exception handling. Create a new `TransactionalOperator` using `ReactiveTransactionManager`, using a default transaction.


{{< highlight java >}}
@Bean
fun transactionOperator(rtm: ReactiveTransactionManager): TransactionalOperator {
  return TransactionalOperator.create(rtm)
}
{{< /highlight >}}


***Add transaction to the service***

You can add the transaction by wrapping the callback in the `execute` method of `transactionOperator` like below - 


{{< highlight java >}}
val data = Flux.fromIterable(ages.asIterable())
  .map {
    User(id = UUID.randomUUID().toString(),
        age = it)
  }
  .doOnNext { Assert.isTrue(it.age >= 18, "Age should be greater or equal to 18") }
  .flatMap { userRepo.save(it) }

return transactionalOperator.execute{ data} 

{{< /highlight >}} 

OR 

You can use annotation `@Transactional` at the method, which you want to make transactional  


{{< highlight java >}}
@Transactional
fun saveAll(vararg ages: Int): Flux<User> {
{{< /highlight >}}

***Source Code***
Full source is available at [GitHub](https://github.com/vikasontech/-spring-reactive-transection.git)
