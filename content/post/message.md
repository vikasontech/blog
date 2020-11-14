---
title: "What is Message, in Spring Integration"
date: 2020-11-11T10:10:37+07:00
draft: false 
description: "Details about the Message object in spring integration"
categories:
  - "Spring Integration"
---

# What is Message

Message content the actual data and it's meta data, that need to pass to other services. Message is having 2 parts 

<img src="https://i.ibb.co/bWffXHj/pencilsketchadjusted-5289224.png" alt="Message" style="width:400px;vertical-align:middle;"/>

 - **Header**

    Header contains the metadata about the message like 

 - **Payload**

    It contain the data that need to pass in the message.

It consists of a payload and headers. The payload is actual data of any type , and the headers required information such as ID, timestamp, correlation ID, and return address. 

## Implementation of Message 

### GenericMessage

The commonly used implementation of the `Message` class is `GenericMessage` class. It provide two constructors first one except only payload as parameter and another payload with header as parameter.

    new GenericMessage<T>(T payload);

    new GenericMessage<T>(T payload, Map<String, Object> headers)


### MessageBuilder

Message builder is a helper class that are used to create message. If you see `Message` Interface you will see there are not setter method. Message builder used to create new message object. If you want create new message from the old existing message.
you can also create a message with new payload info and header message from another message using `copyHeader` message. In case you do not want to override the existing header information while coping you can use `copyHeaderIfAbsent` method.

<img src="https://i.ibb.co/ngFG812/Screen-Shot-2563-11-14-at-10-53-34.png" alt="MessagreBuilder" style="width:800px;vertical-align:middle;"/>

### Error message

This is a special time of message implementation, to communicate error information. The implementation takes only `throwable` object as payload. 

You can create `ErrorMessage` object as below - 

    message message = new ErrorMessage(throwable);

## What's next?

Keep following this series of blogs have many topics need to cover in subsequent blogs.



