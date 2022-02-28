---
title: "Multitenancy using Spring data JPA and hibernate"
date: 2022-02-25T01:08:24+07:00
draft: false 
tags: ["Multitenancy", "Hibernate", "spring-boot", "JPA"]
categories: ["Technical"]
---

## Introduction

### What is multitenancy 

You can see the definition of the multitenancy from the wiki. As it says - 

> ***Software multitenancy is a software architecture in which a single instance of software runs on a server and serves multiple tenants. Systems designed in such manner are "shared" (rather than "dedicated" or "isolated"). A tenant is a group of users who share a common access with specific privileges to the software instance. With a multitenant architecture, a software application is designed to provide every tenant a dedicated share of the instance - including its data, configuration, user management, tenant individual functionality and non-functional properties. Multitenancy contrasts with multi-instance architectures, where separate software instances operate on behalf of different tenants. -- [wikipedia](https://en.wikipedia.org/wiki/Multitenancy)***

### Tenant Catagorization

We use the database to categorize the tenant. We have two databases, one is a master database, another is its replica used for the read operations.

## Versions and Dependencies

* org.springframework.boot:spring-boot:jar:2.6.4
* org.springframework.boot:spring-boot-starter-data-jpa:jar:2.6.4
* org.springframework.boot:spring-boot-starter-web:jar:2.6.4
* mysql:mysql-connector-java:jar:8.0.28
* org.projectlombok:lombok:jar:1.18.22:compile (optional) 

### Implementations 

We will create a request interceptor that will intercept the request and add the tenant identifier according to the business rule. Hibernate's `CurrentTenantIdentifierResolver` will identify the current tenant identifier and choose the datasource according to the tenant.

Using this design we will use two databases master and replica, one for write operations and another for read operations.

![Implement Multitenancy in spring-boot](https://tinyimg.io/i/KY4Q1hm.png)

### Create entity class and repository 

**CustomersEntity.java**

`CustomersEntity` is a simple entity class that we would need to access the table data from master database and read-replica database.

{{< highlight java >}}
import lombok.*;

import javax.persistence.*;
import java.io.Serializable;

@Data
@Entity
@Table(name = "customers")
@NoArgsConstructor
@AllArgsConstructor
public class CustomersEntity implements Serializable {

  private static final long serialVersionUID = 1L;

  @Id
  @Column(name = "id")
  @GeneratedValue
  private Long id;
  private String name;
  private String city;
}

{{< /highlight >}}

**CustomersRepo.java**

Create an `CrudRepository` class for the entity `CustomersEntity`.

{{< highlight java >}}

public interface CustomersRepo extends CrudRepository<CustomersEntity, Long> { }

{{< /highlight >}}

### Create Datasource for the database

**DataSourceConfig.java**

Create two separate data sources one for the default or main datasource and another is for our read-replica. We use [DataSourceBuilder](https://docs.spring.io/spring-boot/docs/1.5.3.RELEASE/api/org/springframework/boot/autoconfigure/jdbc/DataSourceBuilder.html) to create the datasource. Since we are using two beans of `javax.sql.DataSource`, it is required to define one bean as primary. So I use the `masterDataSource` that would be used for writing as the primary datasource.

{{< highlight java >}}

@Configuration
public class DataSourceConfig {

  @Bean(name = "masterDataSource")
  @Primary
  public DataSource masterDataSource() {

    return DataSourceBuilder
        .create()
        .driverClassName("com.mysql.jdbc.Driver")
        .username("vikas")
        .password("vikas")
        .url("jdbc:mysql://localhost:3306/db1?useSSL=false&characterEncoding=UTF-8")
        .build();
  }

  @Bean(name = "readDataSource" )
  public DataSource readDataSource() {

    return DataSourceBuilder
        .create()
        .driverClassName("com.mysql.jdbc.Driver")
        .username("vikas")
        .password("vikas")
        .url("jdbc:mysql://localhost:3306/db1?useSSL=false&characterEncoding=UTF-8")
        .build();

  }
}
{{< /highlight >}}

### Provide Tenant Connection Provider

**DataSourceBasedMultiTenantConnectionProviderImpl.java**

We have two datasource beans, one for read-write and one for read only. Now we need a class that would keep these connections and provide the basic support for the multi-tenant connections provider. Hibernate provides an abstract class [AbstractDataSourceBasedMultiTenantConnectionProviderImpl](https://docs.jboss.org/hibernate/orm/4.1/javadocs/org/hibernate/service/jdbc/connections/spi/AbstractDataSourceBasedMultiTenantConnectionProviderImpl.html) that provides this feature. We need to inject both of the databases and keep those connections in a `HashMap`. This class also provides a method `selectDataSource(String tenantIdentifier)` that would be used to choose the datasource based on the tenant name or identifier.

{{< highlight java >}}

@Component
public class DataSourceBasedMultiTenantConnectionProviderImpl extends AbstractDataSourceBasedMultiTenantConnectionProviderImpl {
  private Map<String, DataSource> map;

  @Autowired
  @Qualifier("masterDataSource")
  private DataSource masterDataSource;
  @Autowired
  @Qualifier("readDataSource")
  private DataSource readDataSource;

  @PostConstruct
  public void load() {
    map = new HashMap<>();
    map.put("master", masterDataSource);
    map.put("read", readDataSource);
  }

  @Override
  protected DataSource selectAnyDataSource() {
    return map.get(Constants.DEFAULT_TENANT_ID);
  }

  @Override
  protected DataSource selectDataSource(String tenantIdentifier) {
    return map.get(tenantIdentifier);
  }
}

{{< /highlight >}}

### Create Tenant Resolver 

**DefaultCurrentTenantIdentifierResolver.java**

After implementations for the `AbstractDataSourceBasedMultiTenantConnectionProviderImpl` you need to find out the current tenant and pass that tenant information so that the correct database can be used based on the request. To do so you can provide and implementations of [CurrentTenantIdentifierResolver](https://docs.jboss.org/hibernate/orm/4.1/javadocs/org/hibernate/context/spi/CurrentTenantIdentifierResolver.html) of the class provided by hibernate. This class is responsible to resolve the current tenant based on some conditions. It returns the tenant identifier as `String`. This class provides two methods `resolveCurrentTenantIdentifier()` that is used to resolve the current tenant, and another is `validateExistingCurrentSessions()` which provide and extra layer of validation that by validate that the tenant identifier on "current sessions" that already exist when `CurrentSessionContext.currentSession()` is called matches the value returned here from `resolveCurrentTenantIdentifier()`.

{{< highlight java >}}

@Component
public class DefaultCurrentTenantIdentifierResolver
    implements CurrentTenantIdentifierResolver {

  @Override
  public String resolveCurrentTenantIdentifier() {

    return Optional
        .ofNullable(RequestContextHolder.getRequestAttributes())
        .map(it -> it.getAttribute("TENANT_IDENTIFIER", RequestAttributes.SCOPE_REQUEST))
        .map(String.class::cast)
        .orElse("master");
  }

  @Override
  public boolean validateExistingCurrentSessions() {
    return true;
  }
}

{{< /highlight >}}

### Create JPA Entity Manager factory Bean 

**MultiTenantJpaConfiguration.java**

Now we need to configure the `Spring JPA Entity Manger factory`. This will be use to create [EntityManagerFactory](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManagerFactory.html?is-external=true). In this class we configure the hibernate properties for multi-tenant strategy, multi-tenant connection provider and the current tenant identifier. 

{{< highlight java >}}
@Configuration
@EnableConfigurationProperties(JpaProperties.class)
public class MultiTenantJpaConfiguration {

  @Autowired
  private DataSource dataSource;

  @Autowired
  private JpaProperties jpaProperties;

  @Autowired
  private MultiTenantConnectionProvider multiTenantConnectionProvider;

  @Autowired
  private CurrentTenantIdentifierResolver currentTenantIdentifierResolver;

  @Bean
  public LocalContainerEntityManagerFactoryBean entityManagerFactory(EntityManagerFactoryBuilder builder) {
    Map<String, Object> hibernateProperties = new LinkedHashMap<>(jpaProperties.getHibernateProperties(dataSource));

    hibernateProperties.put(Environment.MULTI_TENANT, MultiTenancyStrategy.DATABASE);
    hibernateProperties.put(Environment.MULTI_TENANT_IDENTIFIER_RESOLVER, currentTenantIdentifierResolver);
    hibernateProperties.put(Environment.MULTI_TENANT_CONNECTION_PROVIDER, multiTenantConnectionProvider);
    hibernateProperties.put(Environment.DIALECT, "org.hibernate.dialect.MySQLDialect");
		hibernateProps.put("spring.datasource.tomcat.testOnBorrow", true);
		hibernateProps.put("spring.datasource.tomcat.validationQuery", "select 1");

    return builder.dataSource(dataSource)
        .packages(CustomersEntity.class.getPackage().getName())
        .properties(hibernateProperties)
        .build();
  }

}

{{< /highlight >}}

###  Create an web request intercepter

**MultiTenancyInterceptor.java**

You need some way to identify the request that should be used to set the tenant from in the request. It is possible to pass the tenant information in the request header. You can also use an interceptor that would identify the request and based on the request set the tenant identifier in the request. I used the condition that the request should be `GET` and the request URI should be `/read`, you can use any condition that suits you. Or you can create a list of `Predicate` types and then evaluate its value to true and set the tenant identifier according to the conditions. 

Here I set the attribute `TENANT_IDENTIFIER` in the request with the tenant identified as `read`.

{{< highlight java>}}

public class MultiTenancyInterceptor  extends HandlerInterceptorAdapter {

  Predicate<String> isGet = it -> it.equals("GET");
  Predicate<String> isReadOnlyPath = it -> it.equals("/read");

  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    final String method = request.getMethod();
    final String requestURI = request.getRequestURI();
    if(isGet.test(method) && isReadOnlyPath.test(requestURI)) {
      request.setAttribute("TENANT_IDENTIFIER", "read");
    }
    return true;
  }
}

{{< /highlight >}}

### Add `Intercepter` to add tenant information

**WebConfig.java**

Configure the `MultiTenancyInterceptor` in web application configuration as interceptor.

{{< highlight java >}}
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new MultiTenancyInterceptor());
  }
}

{{< /highlight >}}

### Create a service class

**ReadWriteServiceImpl.java**

Create a service which would use the repo `CustomersRepo` to access the data from the repository. In this service I have created two methods `readOperations()` for read operations and another is `writeOperations()` for write operations.

{{< highlight java>}}

@Service
@RequiredArgsConstructor
public class ReadWriteServiceImpl implements ReadWriteService {

  private final CustomersRepo customersRepo;

  @Override
  public String readOperations() {
    final CustomersEntity one = customersRepo.findById(1L).get();
    System.out.println(one);
    return one.toString();
  }

  @Override
  public String writeOperations() {
    final CustomersEntity one = customersRepo.findById(1L).get();
    System.out.println(one);
    return one.toString();
  }

}

{{< /highlight >}}

### Create a rest controller 

**ReadWriteController.java**

Create controller class. 

{{< highlight java>}}

@RestController
public class ReadWriteController {
  @Autowired
  private ReadWriteService helloService;

  @GetMapping("/read")
  String read() {
    helloService.readOperations();
    return "Read Operation";
  }

  @GetMapping("/write")
  String write() {
    helloService.writeOperations();
    return "Write Operation";
  }
}

{{< /highlight >}}

## References
https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.multiple-modules
http://anakiou.blogspot.com/2015/08/multi-tenant-application-with-spring.html

## Github
https://github.com/vikasontech/spring-read-replica
