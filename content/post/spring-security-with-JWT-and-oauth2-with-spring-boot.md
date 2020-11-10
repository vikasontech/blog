+++
date = 2018-02-23T16:23:39+07:00
draft = false 
title = "Spring Security With JWT and Oauth2 With Spring Boot"
description = ""
tags = ["spring-boot","jwt","oauth2","security"]
categories = ["Technical"]
externalLink = "In this article we shall see example of how to secure a spring boot rest application with Spring Boot2, Spring Security, Oauth2, and JWT token."
+++

***Introduction***

In this article we shall see example of how to secure a spring boot rest application with Spring Boot2, Spring Security, Oauth2, and JWT token.

***1. Source Code Repository***

The code used in this article is available in this repository GitHub.

***2. Dependencies***

{{< gist vikasontech d53cb08fbcec47830327e46053de4e00 >}}

***2.1. Versions***
- Spring Boot: 2.1.3.RELEASE
- Java: 1.8

***3. Entity Class***

We create two Entity Classes, one is User and another is Role. User entity class contains user details like name, password, roles details, and role entity contains the role details. One User may have many roles hence we to create many-to-many relationship between User and Role Entity.

***3.1. User Entity.java***

Entity class that contains the user details.

{{< gist vikasontech a14f3b0a4d3be3126da1d19481435fb7 >}}

***3.2. Role.java***

Entity class that contains the user role data.

{{< gist vikasontech   a620566440266c007388e3b6fbcbcf26 >}}

***4. Create CRUD Repository***

We create spring CRUD repository class, to access user credentials from the database.

Note: we create repository only for User entity class, because in User entity class we specify Many to Many relationship between User and Role entity, and ```@ManyToMany(fetch = FetchType.EAGER, cascade = CascadeType.ALL)``` at line number 35 in User.java, will ensure that when new record save in User entity it will also save in Role entity.

***4.1. UserRepository.java***

{{< gist vikasontech  3d6e10e550ed42b0ae172ce000d087ac >}}

***5. Create a Spring boot Initialize class***

This will boot the spring application.

{{< gist vikasontech 02552a6cd2a36663a2ec98a4803ceef7 >}}

Now we need to create initial data for users create data.sql file and put it into resource directory.

{{< gist vikasontech  901253919807eb5ba4a509240cf9db0c >}}

***6. Controller class***

Now create controller class to access the resources.

{{< gist vikasontech f29e2db7338f234672a8c8e1af1b7210 >}}

Now our basic application is created, just the start the application server to see the output.

use ```mvn spring-boot:run``` to run the application

After the server start just curl the below URL to check if everything is working correct or not

```curl http://localhost:8080/app/listAll```

Response -
{{< highlight json >}}

[
  {
    "id": 1,
    "username": "vikas",
    "password": "$2a$04$lZj8KgBFkcPwgRWjH8DwBeCIR7HE6AsIZqTXu2VyeEw5sYLySNAGe",
    "firstName": "vikas",
    "lastName": "verma",
    "roles": [
      {
        "id": 1,
        "roleName": "ADMIN",
        "description": "admin role"
      }
    ]
  },
  {
    "id": 2,
    "username": "james",
    "password": "$2a$04$P2GbxPDh1MYNYyNn/bj.4.QxwDC2jze0xPQF4u6/cNpdkrPq3OdPy",
    "firstName": "james",
    "lastName": "james",
    "roles": [
      {
        "id": 1,
        "roleName": "ADMIN",
        "description": "admin role"
      },
      {
        "id": 2,
        "roleName": "USER",
        "description": "user role"
      }
    ]
  }
]
{{< /highlight >}}

***7. Authentication and Authorization***

Now since our basic application is working, it’s time to add authentication and authorization to our application.

Add Dependencies for the Oauth2 Security

{{< gist vikasontech 
74561a889aad8462750f131b3610ad1e >}}

***7.1. Set the properties related to security***

{{< gist vikasontech bd69a732d39dc30fc6e86410dc8d578e >}}

The detail of the properties is as below -

1. `security.encoding-strength=256` size of the encoding.

2. `security.signing-key` is the sign key used to encode the token.

3. `security.security-realm` realm of the authentication. see

4. `security.jwt.client-id=client` Client Id.

5. `security.jwt.client-secret` It should be BCrypt format you can use this tool to encode any string to BCrypt

6. `security.jwt.grant-type=password` Grant type, it can be "password", "refresh-token".

7. `security.jwt.scope-read=read` read scope.

8. `security.jwt.scope-write=write` write scope.

9. `security.jwt.resource-ids=resource` resource id.


***7.3. JWTConfigProperties.java***

Create a class to access the properties set in application property file.

{{< gist vikasontech 479c8059b01f841a4a69ec50bf011cc5 >}}

***7.4. UserDetailServiceImpl.java***

To access the user credentials from database, we need to implement interface UserDetailsService. It is used throughout the framework as a user DAO. The interface requires only one read-only method, which simplifies support for new data-access strategies.

{{< gist vikasontech d7229f63f811140852e4c3bf9eb46ddb >}}

***7.5. WebSecurityConfig.java***

Configure the web security by extending the WebSecurityConfigurerAdapter class. It provides a convenient base class for creating a WebSecurityConfigurer instance. The implementation allows customization by overriding methods.

{{<gist vikasontech 
08690cc7e65e0a2bd5ea03906febf166 >}}

`Step#1` Configure UserDetailsService to AuthenticationManager class, and add a password encoder.

*Autowire the userDetailsService instance.

{{<gist vikasontech 874b011e971cb656b86b42cf2010486c >}}

`Step#2` Now create AuthenticationManager bean

{{< gist vikasontech 874b011e971cb656b86b42cf2010486c>}}

Since we are using password as grant-type we need to provide the AuthenticationManager implementation.

`Step#3` Configure the JWT Token related beans -

{{< gist vikasontech 0a0334c8eb576a6a5a2ce1a17f850d9a>}}

```tokenStore()``` Create a new ```JwtTokenStore``` with this token enhancer(```accessTokenConverter```.

```accessTokenConverter()``` Configure JWT signing key. It can be either a simple MAC key or an RSA key. RSA keys should be in OpenSSH format, as produced by ssh-keygen.

```tokenServices()``` it is default implimentation of the tokenServices, it used to configure the resource related properties. In the tokenServices we used the default implementation of the TokeService interface, and set persistence strategy for token storage. the tokenStore, and configure to support the refresh token.

Now enable Web Security in the application, and Global Method security-

{{< gist vikasontech 5ca2c8c69baf0a7b44663c2de070624a>}}

`@EnableWebSecurity` : It allow the Spring Security configuration defined in any WebSecurityConfigurer or more likely by extending the WebSecurityConfigurerAdapter base class and overriding individual methods.

`@EnableGlobalMethodSecurity(prePostEnabled = true)` : Enables Spring Security global method security.

`@Order` : Define the order the security filter chain. The priority of the WebSecurityConfigurerAdapter is more than the resourceServerConfigurationAdapter, hence we re-define the order WebSecurityConfigurerAdapter

***8. Define Authentication Manager***

Create a class ```AuthenticationServerConfig.java``` that extends ```AuthorizationServerConfigurerAdapter.java``` to provides the default implementation for the AuthorizationServer. It is used to register the clients that can access the resource of the application, and also endpoints of the authorization server.

***8.1. Configure Clients of the application***



the code is self-explanatory, we jest configure the client details that are stored in memory. All the clients’ details are store in the application.property file.

*Autowire the jwtConfigProperties instance.


***8.2. Configure the Authorization endpoints***

{{< gist vikasontech 5b3fc5f82c2bbab35f318cacaa47b89e>}}

*Autowire the jwtConfigProperties, jwtAccessTokenConverter, tokenStore and authenticationManager instance.

Configure the tokenstore, and tokenEnhancer with the AuthorizationServerEndpointsConfigurer

Configure the TokenStore, authenticationManager, and tokenEnhancerChain in the AuthorizationServerEndpointsConfigurer class.

***8.3. Enable Authorization Server in the current application context***

{{< gist vikasontech f8e4ee7f0d74fb2697d016d110350ef7>}}

***9. Resource Server Configuration***

Create a class ```ResourceServerConfig.java``` that extends ```ResourceServerConfigurerAdapter.java```   

This class is used to configure the resourceIds, and the http request URLs that are allowed to access the application, and the URLs that need to be authenticated.

***9.1. Configure Resource Id***

{{< gist vikasontech 5bf685541e49b572e7ed89002562c335>}}

*Autowire the jwtConfigProperties, defaultTokenService bean instances.

Resource id is configured in ```application.property ```file. ```defaultTokenService``` is created in class ```WebSecurityConfig```.

***9.2. Configure HttpSecurity***

{{< gist vikasontech 0c234848552c269dd9b0d5004f267dee>}}

This configuration says, authenticate all request that contains ```"/app/**"```in there URL.

***9.3. Enable the resource server configuration***

{{< gist vikasontech 03776f00af6b45c7c5a04035085c4b09>}}

***10. Modify the controller class***

Finally set the roles that can access the application using ```@PostAuthorize``` annotation

{{< gist vikasontech 5e5c6405df88b355aaed86742214afef>}}

***11. Run The application***

***11.1 Run MySQL Database***

Run below command to run the mysql db in docker

```docker-compose -f docker/docker-compose up```

Now all the configurations are done, now we can run the application using below maven command in terminal

```mvn spring-boot:run```

***11.1 Access Resource with User credentials***

After server started, We can get the access token. Run below command to get the access token-

***11.1.1. Get Access Token***

{{< gist vikasontech 6d9af9361b72403a5685f3613c0bddac>}}

Result-

{{< highlight json >}}
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsicmVzb3VyY2UiXSwidXNlcl9uYW1lIjoidmlrYXMiLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNTUyMDA5ODM2LCJhdXRob3JpdGllcyI6WyJBRE1JTiJdLCJqdGkiOiI3YWJkOThhNC1hNjM4LTRmYmQtOWYzMC0zZWJiNDQ0M2FhMTciLCJjbGllbnRfaWQiOiJjbGllbnQifQ.P0UgLpAuswCs8iHsxT4q23TI1infsIMqZ1YtMlbfWe8",
  "token_type": "bearer",
  "expires_in": 43199,
  "scope": "read write",
  "jti": "7abd98a4-a638-4fbd-9f30-3ebb4443aa17"
}

{{< /highlight >}}

***11.2 Access Resource***

***11.2.1. Get the access token***

{{< gist vikasontech aa0fe6aa21e969c6a8e4133ed1f616a5>}}

** "Y2xpZW50OnNlY3JldA==" is Base64 encoded client-id:secret. you can encode and decode in base64 format using this site. The format of client-id and client-secret should be 'client-id:secret'.

Result-

{{< highlight json >}}
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsicmVzb3VyY2UiXSwidXNlcl9uYW1lIjoidmlrYXMiLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNTUyMDA5ODM2LCJhdXRob3JpdGllcyI6WyJBRE1JTiJdLCJqdGkiOiI3YWJkOThhNC1hNjM4LTRmYmQtOWYzMC0zZWJiNDQ0M2FhMTciLCJjbGllbnRfaWQiOiJjbGllbnQifQ.P0UgLpAuswCs8iHsxT4q23TI1infsIMqZ1YtMlbfWe8"
  "token_type": "bearer",
  "expires_in": 43199,
  "scope": "read write",
  "jti": "7abd98a4-a638-4fbd-9f30-3ebb4443aa17"
}
{{< /highlight >}}

***11.2.2. Access Admin Resources***

To access the resource that required admin privilege you need to get the access token with user james/james, because this user have admin privilege

{{< gist vikasontech eb15055068f2984efbca36ce60002998>}}

Result-

Access admin resources -

Hello Admin!!!

Access user resources -

Hello User!!!


***11.2.3. Access the User resources***

{{< gist vikasontech 6246993ca66c85b8f0773ce7351b38a2>}}

Result-
Hello User!!!

That's all for the Authentication and Authorization with Oauth2. You can access the resource from [GitHub](https://github.com/vikasontech/SpringSecurityWithOauth).
