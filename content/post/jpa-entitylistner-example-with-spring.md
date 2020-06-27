---
title: "JPA EntityListner example with Spring Boot 1.5.1"
date: 2017-04-25T18:46:45+07:00
draft: false 
description: "To audit any transaction table you can use EntityListener Annotation of the JPA. JPA provide the EntityListener annotations that attach a listener class with the entity that you want to audit."
tags:  [ApplicationContext, Audit, JPA, Simple Spring Program, Spring, Spring Boot]
---

***Introduction***

To audit any transaction table you can use EntityListener Annotation of the JPA. JPA provide the EntityListener annotations that attach a listener class with the entity that you want to audit. 

**What is audit table ?**

Audit Tables are used to track transactions against a particular table or tables. They allow you to see an ongoing "log" for future use.  

There may be users and/or applications that have access to insert, update, and delete out of that table. If you may want to have a quick and easy way to track who is doing what on that table. 

**Scope**

We will create a trigger like functionality. We shall audit all the DML operations (i.e. Insert, update and delete) on a table. We shall log all details of the operation, time-stamp, and the user name details in the audit log table. 

***Dependencies***

**Create Application** 

First create User Entity class that contain the user details data.

**User.java**

{{< gist vikasontech 922f7609cc40fa747e388eb88345ccf8>}}

Create Spring data repository to create CRUD operations on the database.

**UserRepository.java**
{{< gist vikasontech fd79aa27ba8c3d9216728bc0fa6abe50>}}

Create a Controller class to handle the Request -

UserController.java

{{< gist vikasontech 9374bd993dbc9c5aa026c0a8650bc669>}}

Create Application class for spring boot configuration.

Application.java

{{< gist vikasontech 5fbc12a1f11df5640827344b4d35f256>}}

Project Structure -

![Project Structure](https://1.bp.blogspot.com/-FUS9PNR1kGg/WQAJkM41WsI/AAAAAAAAb8c/-cYowNtOJ68GGqpP5BkkuiqSR2ZWWZQxwCLcB/s400/Project_Structure.png)


Compile the application with below maven command -

```mvn clean install```  

Run the example by below command on the command line  - 

```mvn spring-boot:run``` 

You can now perform create and update operation on the user entity.

**Create AuditLog Entity and AuditLogRepository class** 

Create a AuditLog entity for audit log table, to keep the audit data -

AuditLog.java

{{< gist vikasontech f93c9bc12e89f44ee8cce15d2ac6ea0f>}}

Create a Spring repository for CRUD operation on the on database.

AuditLogRepository.java

{{< gist vikasontech a13a816638ddbf6cb1b7654a4dc966ae>}}

**Attach AuditLog entity with User Entity class**

First create a Entity Listener class that will listen the User table, and if any update operation performed on the User entity, the listener will call the call back methods.

UserAuditLogListener.java

{{< gist vikasontech eee81e198a67e2cb35feee5b9021d8d2>}}

The same callback method or entity listener method can be annotated with more than one callback annotation. For a given entity, you cannot have two methods being annotated by the same callback annotation whether it is a callback method or an entity listener method. A callback method is a no-arg method with no return type and any arbitrary name. An entity listener has the signature void <METHOD>(Object) where Object is of the actual entity type (note that Hibernate Entity Manager relaxed this constraint and allows Object of java.lang.Object type (allowing sharing of listeners across several entities.) 

A callback method can raise a RuntimeException. The current transaction, if any, must be rolled back.

The following callbacks can be used -

Type | Description
-----|-------------
@PrePersist | Executed before the entity manager persist operation is actually executed or cascaded. This call is synchronous with the persist operation. |
@PreRemove | Executed before the entity manager remove operation is actually executed or cascaded. This call is synchronous with the remove operation. 
@PostPersist | Executed after the entity manager persist operation is actually executed or cascaded. This call is invoked after the database INSERT is executed. 
@PostRemove | Executed after the entity manager remove operation is actually executed or cascaded. This call is synchronous with the remove operation. 
@PreUpdate | Executed before the database UPDATE operation. 
@PostUpdate | Executed after the database UPDATE operation. 
@PostLoad | Executed after an entity has been loaded into the current persistence context or an entity has been refreshed. 


For more information please refer [this link](https://docs.jboss.org/hibernate/entitymanager/3.5/reference/en/html/listeners.html).

Thing to keep in mind!!
You can define several entity listeners per entity at different level of the hierarchy. You can also define several callbacks at different level of the hierarchy. But you cannot define two listeners for the same event in the same entity or the same entity listener.

When an event is raised, the listeners are executed in this order: 
@EntityListeners for a given entity or super class in the array order 
Entity listeners for the super classes (highest first) 
Entity Listeners for the entity 
Callbacks of the super classes (highest first) 
Callbacks of the entity

**Modify User entity to attach EntityListener class**

User.java

{{< gist vikasontech 1e790079cfd1913241dc395edec9ed23>}}

Annotation ```@EntityListners``` specifies the callback listener classes to be used for an entity or mapped super class.```@EntityListeners (value = UserAuditLogListener.class)``` will attach the ```UserAuditLogListener.class``` with the User Entity class. 

JPA Entitylisteners are not entities, they are simply java classes in which JPA life cycle callback methods are implemented using annotations.

**Save data in the AuditLog table**

To inject ```AuditLogRepository``` into the ```UserAuditLogListener``` we can use an ```ApplicationContextAware``` Bean that will give the ```AuditRepository``` from Spirng context  to save the Audit data. So, Create a ```BeanUtility``` service that implement the application context.

BeanUtility.java

{{< gist vikasontech 1d2638c22c13b57d0ac3225894edf40b>}}

Create a controller class to handle the request for audit log data .

AuditorController.java

{{< gist vikasontech e5a6b14131619d263b47dd1164385a02>}}

***Updated Project Structure***

![updated project structure](https://1.bp.blogspot.com/-NHgKrrA4Mms/WQAcL7PsukI/AAAAAAAAb8s/NanCf5f7XUsiy7rUWcE5ODMqBQsn1j-hgCLcB/s400/Project_Structure_2.png)

Now build the program using below maven command on the command line  - 

```mvn clean install```  

Run the example by below command on the command line  - 

```mvn spring-boot:run```

You can download the sample code from below link - 
[Sample Program](https://github.com/vikasontech/springjapauditlog)
