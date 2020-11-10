+++ 
draft = false
date = 2018-11-18T11:49:10+07:00
title = "Setup Keycloak with Docker"
description = "Introduction of Keycloak AAA"
tags = ["keycloak","docker","setup"]
categories = ["Technical"]

+++

**Introduction of Keycloak**

Open Source Identity and Access Management For Modern Applications and
Services. It add authentication to applications and secure services with
minimum fuss. No need to deal with storing users or authenticating
users. It's all available out of the box.

You'll even get advanced features such as User Federation, Identity
Brokering and Social Login.

**Prerequisites:**

-   Docker:

Since we are using Docker to install Keycloak; it should be installed on
your machine. If it is not you can download and install it from the link
given below, it’s pretty straight forward.

-   [Mac/Linux](https://docs.docker.com/docker-for-mac/)
-   [Windows](https://docs.docker.com/docker-for-windows/install/)

**Create docker compose file**

We use keycloak with mysql database to persist the user data. Use the
below docker-compose.yml file to pull and start the docker server
docker-compose.yml

{{< gist vikasontech 24c4ca4ec051b0ad22a912f19807a06a >}}

In docker compose file we povide default user name and password for the
keycloak use and described in the docker hub for the keycloak image. Our
mysql and keycloak server are running on the docker network
“Keycloak-network”. Start keycloak and mysql server

Run docker-compose up command on the terminal, once the server up you
can see the output as below -

![image](https://1.bp.blogspot.com/-YjgKqAuohoQ/W_-ZIs5DHbI/AAAAAAAAkRo/ZLgbb5qaDrY0Pol1uuei7rrlqVUhXwjNACEwYBhgL/s320/Screen%2BShot%2B2561-11-29%2Bat%2B14.44.31.png)

Go the browser and type the url - http: //localhost:9001 Note: remember
we expose the port in the docker-compose file

![image](https://lh3.googleusercontent.com/XF8uOYI14dqFc6ns-PLgc_ewrxjIfryAwHofSAFKVA-dWe2FWzQ5Csu-24PAtvTszqOOXD_PZBSG5RtI4yH7JqAxvgCe-GhoGbnfV2b-xTZ0gpTd_KgDF1oAnw5UthP2WfLHfzOV)

You can now login keycloak from the username and password you provided
in docker-compose file. In our case we use username admin and password
also admin

![image](https://lh5.googleusercontent.com/fBLFQeZcVyS_khIBWcdkQQCS_XutZeIhoW8XfCQDpOEovXz63dqaHpqWF80l4HYi7BhhqtGgPv9bsGlIv3dXqG0b3LMDhV6hcRawFpWizRdMdYSdCNq2CKUl8MMbmYp7R3GDvhrm)

After login you will see the default realm master as below -

![image](https://lh3.googleusercontent.com/pzoH0ZM1x4QcdbP_MjoNYtOyCIWtrHHFg_WVBd3AwKFuSOxxakNMtbGFxPUX_k-Mb12VeCehREtpN1Bi9ME1Bp7_fs9pZbDt01_0k9S509CBTwumb5oo92eEcwjkzdNaQnbn3LOC)

**Core concept and terms of keycloak -**

**Realms:**

A realm manages a set of users, credentials, roles, and groups. A user
belongs to and logs into a realm. Realms are isolated from one another
and can only manage and authenticate the users that they control.

**Groups:**

Groups manage groups of users. Attributes can be defined for a group.
You can map roles to a group as well. Users that become members of a
group inherit the attributes and role mappings that group defines.

**Clients:**

Clients are entities that can request Keycloak to authenticate a user.
Most often, clients are applications and services that want to use
Keycloak to secure themselves and provide a single sign-on solution.
Clients can also be entities that just want to request identity
information or an access token so that they can securely invoke other
services on the network that are secured by Keycloak.

**Client scopes:**

When a client is registered, you must define protocol mappers and role
scope mappings for that client. It is often useful to store a client
scope, to make creating new clients easier by sharing some common
settings. This is also useful for requesting some claims or roles to be
conditionally based on the value of scope parameter. Keycloak provides
the concept of a client scope for this.

**Client role:**

Clients can define roles that are specific to them. This is basically a
role namespace dedicated to the client.

**Identity token:**

A token that provides identity information about the user. Part of the
OpenID Connect specification. You can get more details about this terms
here !

**Conclusion:**

In this blog we learn how to setup keycloak using docker, in next blog
we will create an application and secure it using keycloak.

**Reference https:**

<https://www.keycloak.org/docs/latest/getting_started/index.html>

<https://www.keycloak.org/docs/latest/server_admin/index.html>
