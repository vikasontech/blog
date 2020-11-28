---
title: "Message channels and Adapters"
date: 2020-11-21T14:38:25+07:00
draft: false 
description: "Intro to Spring integration Message Channels "
tags: ["spring-boot","spring-integration"]
categories: ["Technical","spring-integration"] 
---

# Message Channel 

A message channel is use to transport the message from one messaging endpoint to another. It integrate the messaging endpoints. Message channel used to decouple the message producer and consumer.

There are 3 interface provided for message - 

## 1. Message Channel Interface

It is the base interface they provide two methods to send the messages

		public interface MessageChannel {
           boolean send(Message message);
           boolean send(Message message, long timeout);
		}

The return value `true` means message sent successful. If sending interrupted then `false`.

## 2. Pollable Channel Interface

This message channel allow to pole the message using any poller. This is buffer channel that use queues to buffer the message. 

    public interface PollableChannel extends MessageChannel { 
        Message<?> receive();
        Message<?> receive(long timeout);
    }	

## 3. Subscribable Channel Interface

A non-buffering message channel is use for the messages broadcasting . The consumers that are subscribe to that channel will receive that message.

# Message Channel Implementation

## DirectChannel

A default message channel, which run on a single thread. It is a blocking channel. It is point-to-point channel. A DirectChannel implements `SubscribableChannel`, hence it dispatch messages to the subscriber.  
It also provide load balancing with default strategy as round-robin. It help to manage the load, in case of more than one subscriber. 

![Direct Channel](https://i.ibb.co/ctNhHPH/SI-BOOK-Direct-Channel.png)

The load balancing is also work in conjunction with a `failover` properties. By default if the value of the property is `true`. When one consumer throw some exception, then the message deliver to the next consumer.

![Direct Channel Failover](https://i.ibb.co/8KHZ2YC/SI-BOOK-Direct-Channel-Failover.png)	

## PublishSubscribeChannel

It broad cast the message to many subscribers. This channel primarily used for notification purpose. 
This channel is send only hence it does not have the receive method. 
The subscriber must `pole` the data from the message channel. 
When minimum number of consumers handle message, the sending method will return `true`.  You can use `minSubscribers` property to configure it.

![Publish subscribe channel](https://i.ibb.co/PF0ZGhm/SI-BOOK-Pub-sub-Channel.png)

## QueueChannel
A QueueChannel is a  non-blocking message channel. It backed by and `BlockedQueue` to save the messages. You can specify the minimum capacity of queue. The default capacity is `Integer.MAX_VALUE`. It is always recommended to provided some initial capacity to avoid the `OutOfMemoryErrors`. For a zero capacity version you can use the `RendezvousChannel`. 

## PriorityChannel

PriorityChannel extends the `QueueChannel`. It provide priority of the message by using comparator. Message header is the default comparator. You can also use the custom comparator using payload data.

## RendezvousChannel

RendezvousChannel extends the `QueueChannel`. The only difference between RendezvousChannel and QueueChannel use different queue. The QueueChannel use the `BlockingQueue` where as RendezvoudChannel use the  zero-capacity `SynchronousQueue`.

## ExecutorChannel

ExecutorChannel is same as `DirectChannel`. It also provide the `load-balancing` and `failover` properties. The only key difference is that it delegates to the `TaskExecutor` which means, that the sender will not blocked until the receiver finished the processing. Since the sender and receiver will execute in different threads it does not support transactions.

## FluxMessageChannel

This is special type of message channel used with `ReactiveStreams`. It implements the `org.reactivestreams.Publisher` interface for `sinking` sent message into a `Flux`. This channel implementation `org.reactivestreams.Subscriber` instances can be use to consume from this channel. It also supports back-pressure and other reactive streams features.

## Scoped Channel

Scoped channel is use to set the boundaries for the message channel. It allow to specify the scope of the channel where this channel can accessed.  

# Special Channels

In case of namespace support enabled. Two special type of channels created in the application context -

**errorChannel**

This channel is use to forward the error messages. This channel is configurable and you may provide the custom configuration for this.

**nullChannel**

The `nullChannel` is behave like `/dev/null`. You can send any message to it that you do not care to.

# Polar

Poller is use to poll the message from the Pollable message channels. The `Poller` provide many configuration options. You can configure the polling rate. We would see poler in later chapters. 

# Channel Adapter

Channel adapters are use to connect your application to the messaging system. Channel adapter have the access to the application APIs or data. It can publish the messages based on this data. Channels adapters are use for oneway communication. You send a message to the adapter and then forget. If you want two way communication you can consider the `MessageGateway`
There are two type of Channel adapters - 

* Inbound channel adapter

The channel adapters that receive the message from the external system called. It receive the message from some external system and then forward it for further business process..

* Outbound channel adapter

The channel adapters is use to send the message to the external system called  outbound channel adapter

![Channel Adapter](https://i.ibb.co/1MGBqcv/SI-BOOK-Channel-Adapter.png)

# Messaging bridge

As the name suggests, A message bridge is use as a bridge between two messaging systems. It receive a message transform it and then re-send it to the another system. For example you might have and serving that receive REST format message. There is another service that accept the message in SOAP format. To connect these two service you can use the message bridge. It could receive the message from both the systems, and send then to one another.

![Message Bridge](https://i.ibb.co/DkLRHHk/SI-BOOK-message-bridge.png)

