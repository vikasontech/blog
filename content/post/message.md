---
title: "What is Message, in Spring Integration"
date: 2020-11-11T10:10:37+07:00
draft: false 
description: "Details about the Message object in spring integration"
categories:
  - "Spring Integration"
---

# What is Message

Message content the actual data and its metadata, that need to pass to other services. The message is having 2 parts 

<img src="https://i.ibb.co/bWffXHj/pencilsketchadjusted-5289224.png" alt="Message" style="width:400px;vertical-align:middle;"/>

 - **Header**

    The header contains the metadata about the message like 

 - **Payload**

    It contains the data that needs to pass in the message.

It consists of a payload and headers. The payload is actual data of any type. The headers can contain information such as correlation Id, timestamp, and return address. 

## Implementation of Message 

### GenericMessage

The GenericMessage is an implementation of the `Message` interface. It provides two constructors. The first one accepts the only payload as a parameter and another payload with a header as a parameter.

    new GenericMessage<T>(T payload);

    new GenericMessage<T>(T payload, Map<String, Object> headers)


### MessageBuilder

The `Message` Interface does not have setter methods. Message builder used to create a new message object. You can use the `MessageBuilder` class if you want to create a new message from the old existing message. You can create a message with a new payload. or create a message with that header from another message using `copyHeader` message.

You can use the `copyHeaderIfAbsent` method when you do not want to override the existing header information.

<img src="https://i.ibb.co/ngFG812/Screen-Shot-2563-11-14-at-10-53-34.png" alt="MessagreBuilder" style="width:800px;vertical-align:middle;"/>

### Error message

This is a special time of message implementation, to communicate error information. The implementation takes only a `throwable` object as payload. 

You can create an `ErrorMessage` object as below - 

    Message message = new ErrorMessage(throwable);

## What's next?

Keep following this series of blogs that have many topics that need to cover in later blogs.



