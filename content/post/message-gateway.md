---
title: "Message Gateway"
date: 2020-12-12T12:28:23+07:00
draft: false 
description: An walk through spring integration message gateway.
categories:
  - "Spring Integration"
---

# Message Gateway

## Introductions

A gateway is an interface that provides two-way communication between APIs and endpoints. It provides a simple interface that interacts with the business logic. The API does not that it interacting with the spring integration.
  
## Type of Gateways  

### Synchronous gateway

In synchronous gateway, the sender thread suspended until a reply is received or a timeout happens. After spring integration 4.3, a new asynchronous gateway was added to the spring gateway. 

### Asynchronous gateway

In Asynchronous gateway, that uses `AsyncRabbitTemplate` from `Spring AMQP`. The sender thread sends the message and does not wait for a reply, instead, it returns immediately for the next execution. When a reply is received, the reply sent to the template's container thread. 

## `@MessagingGageway` annotation 

`@MessagingGateway` Since 4.0, the gateway service interface can be marked by the `@MessagingGateway`. The spring integration scan that annotations, and creates a `proxy` using. The regular `@ComponentScan` does not scan the interfaces. You should use the `@IntegrationComponentScan` in the application context.  @IntegrationComponentScan scanned the @MessagingGateway interfaces.

You can also the `@Gateway` annotation to further configure the gateway like -  

{{< highlight java >}}
@MessagingGateway
interface CafeGateway {
    @Gateway(requestChannel = "order",
            replyChannel = "anotherSupplier",
            replyTimeout = 2000,
            requestTimeout = 1000 )
    fun coffeeSupplier(@Payload quantity: Int): Coffee
}
{{< /highlight >}}

The fields details are for this notification is as below  

| Fields | Required | Description |
|-|-|-|
|`requestChannel`| No | Channel to which the request will be sent. |
|`replyChannel`|No|Channel from which the reply will come. |
|`requestTimeout`|No| Timeout in `ms` when sending the request to the channel. |
|`replyTimeout`|No|Timeout in `ms`, when the thread that sending the request will wait for the reply |

## Implementation

### Dependency

Spring Integrations 
Kotlin ( I used in this article, you can use java as well. )
Any IDE

#### Versions

***spring-boot: 2.3.5.Release***

***spring-boot-starter-integration: 5.3.3 Release***

***kotlin: 1.3.72***

### Demo

We need to create a gateway that will accept some orders and then supply the Coffees in the required quantity.

#### Gateway 

{{< highlight java >}} 

@MessagingGateway
interface CafeGateway {
    @Gateway(requestChannel = "order",
            replyTimeout = 1,
            requestTimeout = 1 )
    fun cafeSupplier(@Payload quantity: Int): Cafe

}

{{< /highlight >}} 

### Consumer

Create and consumer class that will consume the messages that are sent to the order channel. The gateway will send the message to the order channel. We need some messaging endpoint that could receive the message for further processing. 

{{< highlight java >}} 
@ServiceActivator(inputChannel = "order", outputChannel = "supplier")
fun sendMessageToGateway(quantity: Int): Coffee {
    log.info("Order received for the quantity: $quantity")
    log.info("Coffees sent for the request")
    return Coffee(quantity = quantity)
}

{{< /highlight >}}

The `sendMessageToGateway` method will be invoked by the message channel whenever it has a new message. The value returned by the `serviceActivator` will be sent to the message channel `supplier`.  

Create another message endpoint that will subscribe to the message channel `supplier`.

{{< highlight java>}}
@ServiceActivator(inputChannel = "supplier")
fun receivedChannel(@Payload coffee: Coffee): Unit {
    log.info("Got the Coffees from the supplier: $coffee")

}
{{</highlight>}}

### Channel

We have to create the channel beans as well. Create a configuration bean class and create the below channels. 

{{< highlight java >}}

@Bean
fun order(): DirectChannel {
    return DirectChannel()
}

@Bean
fun supplier(): DirectChannel {
    return DirectChannel()
}

{{< /highlight >}}

### Run

Run the programme and send the message gateway quantity as 10. The output should be as below- 

{{< highlight java >}}
2020-12-12 18:09:44.152  INFO 10015 --- [           main ] o.s.i.h.s.MessagingMethodInvokerHelper   : Overriding default instance of MessageHandlerMethodFactory with provided one.
2020-12-12 18:09:44.160  INFO 10015 --- [           main ] c.e.s.MessageProcessor$Companion         : Order received for the quantity: 10
2020-12-12 18:09:44.161  INFO 10015 --- [           main ] c.e.s.MessageProcessor$Companion         : Cafe sent for the request
2020-12-12 18:09:44.164  INFO 10015 --- [           main ] o.s.i.h.s.MessagingMethodInvokerHelper   : Overriding default instance of MessageHandlerMethodFactory with provided one.
2020-12-12 18:09:44.165  INFO 10015 --- [           main ] c.e.s.MessageProcessor$Companion         : Got the cafes from the supplier: Cafe(quantity=10)
{{< /highlight >}}

### Source

The complete source code is available on the github. See the file and repo at this [link](https://github.com/vikasontech/spring-integration-demo/blob/master/src/test/kotlin/com/example/springintegrationdemo2/DefaultGatewayExample.kt)



