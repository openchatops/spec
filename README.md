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

OpenChatOps bots and their plugins communicate with each other over the network using an RPC protocol. The protocol has not yet been decided upon, but is likely to be Google's gRPC with Protocol Buffers, Cap'n Proto, or Apache Thrift. These systems all provide a schema-based, strongly-typed serialization format and wire protocol for transmitting data over the network. The schemas are authored in a programming language-agnostic interface definition language (IDL) and a compiler is used to generate source code for a number of different programming languages.

## API

The API will be defined in separate files using the IDL for the RPC system. An explanation of all the types of interfaces will follow in this section.

## License

The OpenChatOps specification is available under the [MIT License](http://opensource.org/licenses/MIT). See the attached LICENSE file.
