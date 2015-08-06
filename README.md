# OpenChatOps Specification

**OpenChatOps** (OCO) is a protocol and set of APIs for programming language agnostic chat bots. An OCO-compliant chat bot is a program that serves as a central communication hub for a number of user-authored plugins. The plugins are responsible for connecting the bot to a chat service over a network and for providing various functionality to the bot by responding to incoming messages from users in the chat service.

This repository contains the OpenChatOps specification, which details the communication protocol and the the APIs that a chat bot must provide to support OpenChatOps plugins, as well as the details of plugin authoring and operation.

OpenChatOps is in a very early stage of development. Partcipation in the form of ideas and discussion is welcome and encouraged.

## Table of contents

1. [Architecture](#architecture)
2. [Communication protocol](#communication-protocol)
3. [API](#api)

## Architecture

The OpenChatOps architecture is made up of multiple programs that run as independent processes:

1. One chat bot
2. One chat service adapter
3. Zero or more handlers

### Chat bot

The chat bot is the primary program. It serves two separate roles:

1. When run as an executable, the chat bot implements APIs for receiving incoming messages, sending outgoing messages, managing chat users and chat rooms, and persistent data storage. The chat bot runs an RPC server that exposes its APIs to any program that can reach it over the network.
2. When loaded as a library, the chat bot provides data types and interfaces that make up the client side of the RPC protocol. This library is used to author plugins, both adapters and handlers.

### Adapter

An adapter is a plugin that connects the chat bot to a specific chat service. It is responsible for implementing the adapter interface provided by the chat bot library. Adapters communicate with the chat bot using the RPC protocol. In order for a chat bot to run, a single adapter plugin must be selected.

### Handler

A handler is a plugin that provides arbitrary runtime functionality for the chat bot. The handler registers routes with the bot which match patterns in incoming chat messages and execute code in response. Handlers communicate with the chat bot using the RPC protocol. Handler plugins are not required to run the chat bot, though they are necessary to provide additional functionality and user customization. There is no limit to how many can be run and registered with the bot.

## Communication protocol

OpenChatOps bots and their plugins communicate schema-based, strongly-typed data with each other over the network using Google's [gRPC](http://www.grpc.io/). While gRPC provides the RPC protocol, it leans on Google's [Protocol Buffers](https://developers.google.com/protocol-buffers/) to define the data to be transmitted over the RPC protocol and the interfaces of the services that expose RPC API endpoints. Protocol Buffers uses a programming language-agnostic interface description language (IDL) for schema definition. gRPC's RPC system is built on top of [HTTP/2](https://http2.github.io/), which uses a binary format for efficient network transfer.

In short:

* The OpenChatOps API's services (endpoints) and messages (data types) are defined in the Protocol Buffers IDL.
* When authoring an OCO-compliant chat bot for a particular language, supporting code for that language is compiled from the Protocol Buffers IDL files.
* This generated code includes of all the data types, a scaffold for implementing the main RPC server's API, and client code to make requests to the API in a way that feels natural for that language and hides the details of the protocol.

## API

The OpenChatOps API is expressed in the Protocol Buffers interface description language in the `proto` directory. All services, service methods, messages, and message fields are documented with inline comments. A higher level overview of the API will follow in this section.

## License

The OpenChatOps specification is available under the [MIT License](http://opensource.org/licenses/MIT). See the attached LICENSE file.
