---
title: "What are the Message Endpoints?"
date: 2020-11-14T12:35:51+07:00
draft: true
categories:
  - "Spring Integration"
---

# MessageEndpiont

Message endpoints connect the application to the messaging framework in a non-invasive manner. The application would not have any idea or details of the message usage. 
 
## Transformer

A transformer will consume the message and transform the message into another message. The transformer can change the content or structure of the message. It can also change the header or remove the header information.

## Filter

The message filter determines whether a message should be pass to an output channel or not. It checks the payload content type, a property value, or the presence of header or other conditions. Message filter is also used in conjunction with the pub-sub channel. In the pub-sub channel, many consumers may receive the same message. Message filters used to narrow down the set of messages.

## Router

The message router determines where to send the message. It chooses the channel based on the payload of the message, the header data, or header metadata. 

## Splitter

The splitter used to splits any message or any composite message., into a small group of messages. Later it can also send these small groups of messages to different output channels.

## Aggregator

In contrast to the splitters, it combines messages into a single message. You need to consider the timeout in case of the message not received. Also, consider whether to accept or reject the partial results. You can also decide where to send the error message or partial results.

## Service Activator

The endpoint that got activated in case of any message arrives to channel. This endpoint connects the services and invokes the operational logic. There must be some input channel defined. In case of the operational logic yields some response it can also specify the output channel.  The output channel is optional. You can define the output channel as the `Return Address` header in the message.

## Channel Adapter

A channel adapter can have access to application data. It sends the message to the channel based on these data. The channel adapter can be inbound or outbound.

An inbound channel adapter is usually used to create the flow. Whereas the outbound channel adapter stops the flow. 

## Endpoint Bean Names

The endpoint bean name uses to rename the bean as created in the XML configuration. This endpoint id uses to inject bean when using `@Autowire` annotation.

The endpoint bean name is also helpful when there are some conflicts in the bean name. For example, you may have created a MessageSource bean with the name `fileMessageSourceBean` for the `FileReadingMessageSource`.  A `SourcePollingChannelAdapter` created from the `InboutChannelAdaptor` might have using the same bean name. This may cause the Inbound channel adapter may never start. 


