---
title: "Message channel"
date: 2020-11-21T14:38:25+07:00
draft: true
description: "Intro to Spring integration Message Channels "
tags: ["spring-boot","spring-integration"]
categories: ["Technical"]
---
# Message Channel 

A message channel is used to transport the message from one messaging endpoint to another. It integrate the messaging endpoints. Message channel used to decouple the message producer and consumer.

There are 3 interface provided for message - 
 
## 1. Message Channel Interface

It is the base interface they provide two methods to send the messages

		public interface MessageChannel {
            boolean send(Message message);
            boolean send(Message message, long timeout);
		}

The return value `true` means successfully send the message or else if sending interrupted then `false`.

## 2. Pollable Channel Interface

This message channel allow to pole the message using any poller. This is buffered channel that use queues[^1] to buffer the message. 

    public interface PollableChannel extends MessageChannel {
        Message<?> receive();
        Message<?> receive(long timeout);
    }	

## 3. Subscribable Channel Interface

A non-buffering message channel is used for the messages broadcasting  . The consumers that are subscribe to that channel will receive that message.

# Message Channel Implementation

## DirectChannel
		
A default message channel, which run on a single thread. Its a blocking channel. It is point-to-point channel. A DirectChannel implements `SubscribableChannel`, hence it dispatch messages to the subscriber.  It also provide load balencing with with default stretagy as round-robin, in case of more than one subscriber to the message channel. The load balancing is also work in conjuction with a `failover` properties. By default if the value of the property is `true`. In case of some exception occurs in one subscriber then the message will be sent to the next subscriber.

___todo: Add diagram.___
	
## PublishSubscribeChannel

This channel is used for broadcasting the messages to the many subscribers specially for notification purpose. This channel is used for sending only that is why it does not have the receive method. The subscriber must pole the data from the message channel. The return value of the sending method is true in case the minimum number of receiver receive the message and handle the message successfully. You can change the value of minimum subscriber by changing the property of the `minSubscribers`.

___todo: Add diagram.___

## QueueChannel

A QueueChannel is a  non-blocking message channel. It is backed by and `BlockedQueue` to save the messages. You can specify the minimum capacity of queue. The default capacity is `Integer.MAX_VALUE`. It is always recommended to provided some initial capacity in order to avoid the `OutOfMemoryErrors`. For a zero capacity version use the `RendezvousChannel`. 

___todo: Add diagram.___

## PriorityChannel

PriorityChannel extends the `QueueChannel`. It provide priority of the message by using comparator. Message header is the default comparator. You can also use the custom comparator using payload data.

___todo: Add diagram.___

## RendezvousChannel

RendezvousChannel extends the `QueueChannel`. The only deifference between RendezvousChannel and QueueChannel is of the queue that it used to store the message. Instead of bounded `BlockingQueue` it uses the  zero-capacity `SynchronousQueue`. 

___todo: Add diagram.___

## ExecutorChannel

ExecutorChannel is same as `DirectChannel`. It also provide the `load-balancing` and `failover` properties. The only key difference is that it delegates to the `TaskExecutor` which means that the sender will not blocked until the receiver finished the processing. Since the sender and receiver will execute in different threads it does not support transactions.

___todo: Add diagram.___

## FluxMessageChannel

This is special type of message channel used with `ReactiveStreams`. It implements the `org.reactivestreams.Publisher` interface for `sinking` sent message into a `Flux`. This channel implementation `org.reactivestreams.Subscriber` instances can be used to consume from this channel. It also supports back-pressure and other reactive streams features.

## Scoped Channel

Scoped channel is used is used to set the boundries of the message channel. It allow to specify the scope of the channel where this channel can be used to send or receive the messages. You can use the `scope` attribute value to a channel. todo: rephrese

# Special Channels

In case of namespace support is enabled. Two special type of channels are created in the application context -

**errorChannel**

This channel is used to forward the error messages. This channel is configurable and you may provide the custom configuration for this.

**nullChannel**

The `nullChannel` is behave like `/dev/null`. You can send any message to it that you do not care to.


# Polar

Poller is used to poll the message from the pollable message channels. The `Poller` provide many configuration options. You can configure the polling rate. We would see poler in later chapters. 

# Channel Adapter

Channel adapters are used to connect your application to the messaging system. Channel adapter have the access to the application apis or data and it can publish the messages based on this data. Channels adapters are used for ___oneway communication___[^2]. Means you send a message to the adapter and then forget. If you want two way communcation you can consider the `MessageGateway`

There are two type of Channel adapters - 

* Inbound channel adapter

The channel adapters that receive the message from the external system are called ___inbound channel adapter___[^3] [^4].

___todo: Add diagram.___


* Outbound channel adapter

The channel adapters that sends the message from the external system are called ___outbout channel adapter___[^5] [^6].

___todo: Add diagram.___


# Messaging bridge

As the name suggests, A message bridges is used as a bridge between two messaging systems. It receive a message transform it and then re-send it to the another system. For example you might have and serving that receive REST format message. There is another service that accept the message in SOAP format. To connect these two service you can use the message bridge that could receive the message from both the systems, and send then one another.

___todo: Add diagram.___

[^1]: Need to confirm this point and add to references
[^2]: Need to confirm this point and add to references
[^3]: Need to confirm this point and add to references
[^4]: Check for examples and diagrams
[^5]: Need to confirm this point and add to references
[^6]: Check for examples and diagrams

[ref1](https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessagingBridge.html)

[ref2](https://docs.wso2.com/display/EIP/Messaging+Bridge)
