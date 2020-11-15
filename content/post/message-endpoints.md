---
title: "What are the Message Endpoints?"
date: 2020-11-14T12:35:51+07:00
draft: true
categories:
  - "Spring Integration"
---

# MessageEndpiont

Message endpoints is how an application to the messaging framework, in a non-invasive manner. The application would not have any idea or details of the message usage. 

todo: need to re-think and re-phrase it. 
 
## Transformer

A transfer will consume the message and transform the message into another message. The transformer can change the content or structure of the message. Similarly it can also change the header or remove the header information.

## Filter

Message filter determine whether a message should be passed to an output channel or not. It checked the payload content type, a property value or presence of header or other condition. It is also used with conjunction with the pub-sub channel where multiple consumers may receive the same message and use some criteria of the filter to narrow down the set of messages.

## Router

Message router is determine where to send the message. It choose the channel dynamicly based on the content of the message or the header data. 

## splitter

It splits any message or composit message content into group of messsages and send it to the different output channels.

## Aggregator

In contrast of the splitters, it combine messages into a single message.The things to consider is that when the all the messages are received, when to timeout in case of message not received, whether to accept or reject the partial results and where to send the error message or partial results etc.

## Service Activator

The endpoint that got activated in case of any message is arrived to channel. This endpoint connect the services, and invoke the operational logic. There must be some input channel defined. In case of the operational logic yield some response it can also specify the output channel. but defining the output channel is optional because one can define it in the `"Return Address"` header.

## Channel Adapter

## Endpoint Bean Names


