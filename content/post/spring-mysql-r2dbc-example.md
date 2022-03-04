---
title: "Spring Mysql R2DBC Example"
date: 2022-03-04T20:19:22+07:00
draft: false 
tags: ["spring-boot", "R2DBC", "MySQL"]
categories: ["Technical"]
---

## Introduction

You can find the details about R2DBC from the [official site](https://r2dbc.io/) of spring and R2DBC. 

### What is R2DBC?

> ***R2DBC stands for Reactive Relational Database Connectivity, a specification to integrate SQL databases using reactive drivers. Spring Data R2DBC applies familiar Spring abstractions and repository support for R2DBC. [--Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc#:~:text=R2DBC%20stands%20for%20Reactive%20Relational,and%20repository%20support%20for%20R2DBC.)***

The official site for R2DBC is [here](https://r2dbc.io/)

## Versions and Dependencies

* org.springframework.boot:2.5.6
* org.projectlombok:lombok:1.18.22
* org.springframework.boot:spring-boot-starter-data-r2dbc:2.5.6
* org.springframework.boot:spring-boot-starter-webflux:2.5.6
* dev.miku:r2dbc-mysql:0.8.2.RELEASE
* mysql:mysql-connector-java:8.0.27

###  Maven Repositories -

* `https://oss.jfrog.org/artifactory/oss-snapshot-local/`
* `https://repo1.maven.org/maven2`

### Implementations 

### Technical Stack

* Spring-Boot
* Spring-Webflux
* R2DBC
* MySQL
* Gradle

### Configure the dependencies 

**build.gradle**

We will use MySQL as the database for our application. For that, we need to use a database driver. You can get the list of the available drivers [here](https://spring.io/projects/spring-data-r2dbc).

Along with that, we need the `mysql-connect-java` driver.

```
dev.miku:r2dbc-mysql
mysql:mysql-connector-java
```

### Configure the database connection details

**application.properties**

The spring boot configuration for the r2dbc are as follows - 

{{< highlight java >}}
spring.r2dbc.url=r2dbc:mysql://localhost:3306
spring.r2dbc.name=testdb
spring.r2dbc.username=vikas
spring.r2dbc.password=vikas
{{< /highlight >}}

### Enable the R2DBC Repositories

***SpringReactiveApplication.java**

You need to activate reactive relational repositories using R2DBC by using the annotation [EnableR2dbcRepositories](https://docs.spring.io/spring-data/r2dbc/docs/current/api/org/springframework/data/r2dbc/repository/config/EnableR2dbcRepositories.html).

{{< highlight java >}}
@SpringBootApplication
@EnableR2dbcRepositories
public class SpringReactiveApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringReactiveApplication.class, args);
	}
}
{{< /highlight >}}

That's all that required to connect the application to database using R2DBC. 

Now you can create controller classes and services class to access the data as below -  

### Create and Entity class

**ProductEntity.java**

{{< highlight java >}}

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Table("product")
public class ProductEntity {
  @Id
  private long id;
  private String name;
  private String description;
}

{{< /highlight >}}


### Create an repository class

**ProductRepo.java**

{{< highlight java >}}
interface ProductRepo extends ReactiveCrudRepository<ProductEntity, Long> { }
{{< /highlight >}}

### Create Controller class

**WebFluxDemoController.java**

{{< highlight java >}}
  @GetMapping("/product/{id}")
  public Mono<ProductEntity> findProduct(@PathVariable(name = "id") long id) {
    return productRepo.findById(id);
  }
{{< /highlight >}}

## References
https://spring.io/projects/spring-data-r2dbc

## Github
https://github.com/vikasontech/spring-reactive
