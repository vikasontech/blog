+++ 
draft = false
date = 2018-11-30T11:55:00+07:00
title = "Setup keycloak sercurity for spring boot application"
description = "Setup keycloak in spring boot application"
slug = "" 
tags = ["keycloak","security","spring-boot"]
categories = ["Technical"]
externalLink = ""
+++

**Setup Realm and clients in keycloak**

This blog is the second part of the series of security spring boot
application with keycloak. In first part we install setup in keycloak
with docker you can see [first part here]({{< ref "../post/setup-keycloak-with-docker.md" >}})

**Version:**

-   Keycloak: 4.5.0.Final
-   Pre-requisites
-   Keycloak should be installed in the

**Create realm** A realm secures and manages security metadata for a set
of users, applications, and registered oauth clients. Users can be
created within a specific realm within the Administration console. Click
on Add realm button

![image](https://lh4.googleusercontent.com/D-cerVc3PCbzADRwbvUsMaILQVLYIfqKJ4bZlh_XFa56ifmG1qWvEFditp8M4gtM6Pv6QZUG50uX23fLBfRvzLtdIPkX5mHjmCb9VEL4O_ONCWb6WkFZhPu8OLXAsSuZVJuq9Wu5)

Input the realm name

![image](https://lh6.googleusercontent.com/azCQLitfsOaZfomF02gVfJ1ZCc9Gfao7O7M1ah6Y_kovcvzyYDgh69uKjhOEsP1A3b5ATyDbgyQq3_psoTmt-Cr1eWINDjlCczLjSOF6PSPhUYgiHwMv6sgLLKTJDL57r8DDYTVu)

Once realm created you will see this screen

![image](https://lh6.googleusercontent.com/qwKNyTDTv3THw_BowCu0YzeDq9TXhTUnhk0Of4juiGxo5xgZDbz03_-7N18XL8xISb6qbN9iQL5U92dVDUTD-BYHEEtLzoM_Z1azypv1WGaOPy5inFceOelpkbhutH5FVGEcaRO8)

**Create Client** We need 2 clients one is for bearer-only client for
the application, and another one is public client to get the access
token

![image](https://lh4.googleusercontent.com/D-cerVc3PCbzADRwbvUsMaILQVLYIfqKJ4bZlh_XFa56ifmG1qWvEFditp8M4gtM6Pv6QZUG50uX23fLBfRvzLtdIPkX5mHjmCb9VEL4O_ONCWb6WkFZhPu8OLXAsSuZVJuq9Wu5)

Create Public client

![image](https://lh6.googleusercontent.com/8a4t3_kpYchQIZDBCaFr7E7WItGyIMAj-kAjJtVrpyVCMmSH0j41M01TeZrBwjXGJHXr-W_RxC28L5HLvTSyO9u7no8_vTUgSIyY05W37jODHirye8f45YHCYXnUNRHMdu52HkRk)

Once public client is created you will see the below screen

Create Bearer only client and set the access type to beare-only

![image](https://lh3.googleusercontent.com/UM20ae8FKtq17f_RCzet5nTuG42dUrgyl9gmYhi_a-jtx1XhLIypXszd7z2kbatyNSZodFfP6wdZ5QxjCjuxCAUUPaUdyp26tgxnCnmXprWkA4rUOtvPeukuQvcsaDWmoNkiBHIb)

Add new Admin role for the application client

![image](https://lh4.googleusercontent.com/D-cerVc3PCbzADRwbvUsMaILQVLYIfqKJ4bZlh_XFa56ifmG1qWvEFditp8M4gtM6Pv6QZUG50uX23fLBfRvzLtdIPkX5mHjmCb9VEL4O_ONCWb6WkFZhPu8OLXAsSuZVJuq9Wu5)

Create Admin Client

![image](https://lh6.googleusercontent.com/QDzHU9q-1dlYf7pAc8A9OtrPzDcAolc-yweSjKPvMic4EvwuHrQMxD3UyGrXzZugH1fPa8Ke4FyvJz0s51MEYCNXsig18RPQBbxq4nprkqmkOV39sLiuFzfrWhUNqo8-a4swXOud)

Create User Client

![image](https://lh4.googleusercontent.com/5BfFEIYI98ndYFjp5MHrJ0PBpVNQaMVXewfowYRXKUpld6ySYhTdUXtRk_W5gPPXz4NStjji_ud4Z49oIzVhco9gJw838aGLJh4w8M9d-U3Ip80x_tJbceljy2gaPw04kLsNW7Vj)

**Create User for the application** Create two user with role admin with
and user.

![image](https://lh3.googleusercontent.com/9r6HttwYY9Y9e1RNYBnbK1iEdBGYlDyni1Df49GB0ILNKvtn2OKdEPAacZxgAjNv612nBLosnMXdoDE1rhvW0f71bcYEUkbMnj0czgzY91fAbEi0ZbQBNbdeukQFBGR69YLP0skg)

Create admin user

![image](https://lh4.googleusercontent.com/48JZs2zmgmH3QuVi6MzQLRzmScnK04n8Gihns5mxzZe4s7IN21Y8PO_3UactVVzz4vkom4N3HFfj39o44RTui_YyRHNIALQaepXogo3R8F3gMoSdLVAuo2f0GZ58ebzuGxFj8IsH)

Set password

![image](https://lh6.googleusercontent.com/IV5zzA0Et3iqJ120u69i_FKs5N-4goRgNJCkgRin4TeLPNOxldmN2G7CNVC5gOVnkM8ZNSXfDe4WlLfQ6IvpZHuk5XGWtLcgoVEZVik7mTIM6aN1uYiy2rD_-5-nbbDURPLGMB6T)

Assign admin role to user vik-admin

![image](https://lh3.googleusercontent.com/VbHS9yiqkju_iz9Inp18WkywhBIS-x9GQrcTZ-RS3l7dE4OlBpbWmyLwRkDqCgw0MD-D5gy0g9arDpUoWog55hCz46mIzZp9yNdEKTnuU_K4KYkYrh1J2ejcRzJrAX4Mwy_BHIzg)

Similarly create another user account vik-user with user role

![image](https://lh6.googleusercontent.com/te3Nr_no_S9iFvTvkYFHJBA_iWuGA72vXLC08EikmbVBTbYw8byetuFnvifn8yMV1mEHfQntsw-Bu99j6SwcSgBVdN9qr2S7hajWad2jUWKEXzls5aUO95ItXr7COvkg_m8L9FbT)

![image](https://lh5.googleusercontent.com/DcX4ETQKTKxaUFn_hQeEuA3i5wBjt2JFTJ9-qp-ziUIhnUaUaGHIdSTlliN7h6_t9UpwceTFMHUvjdFn0-hRD8QX8sou8qtuTEnIrfyPZmQI8GgXbJf_cad3tFXDMgPJLfSMiwUg)

**Get access and refresh token**

User below curl to get the access token

{{< gist vikasontech ee0e26e7df9e89f7bc59377024531348 >}}

**Response**

![image](https://lh5.googleusercontent.com/-B8_F-3ei2OGuI_yTQT8ETG4_vmlFZPO5jJPqt_YvIqWIEOEFVj288LtpM6ccBfZanVSVM7j9CCN2xOtBxPIbQq3rH_u4k6DTUpoKS4E4_red7dt3eEg3d3MXMAeCIeGcMEFHA2c)

In the next blog we will se how to secure spring boot application. [You can see the blog here]({{< ref "../post/secure-spring-boot-application-with-keycloak.md" >}}).
