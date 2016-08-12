# OpenChatOps Specification

**OpenChatOps** (OCO) is a protocol and set of APIs for chat bots that are agnostic to programming language and chat service.
An OCO-compliant chat bot is a program that serves as a central communication hub for a number of user-authored programs.
These programs provide functionality to the robot by registering interest for chat messages and other events, then executing callbacks in response to data received from the robot.

This repository contains the OpenChatOps specification, which details the communication protocol and the the APIs that a chat bot must provide to support OpenChatOps programs, as well as the details of callback program authoring and operation.

OpenChatOps is in a very early stage of development.
Partcipation in the form of ideas and discussion is welcome and encouraged!

## Table of contents

1. [Motivation](#motivation)
2. [Architecture](#architecture)
3. [Communication protocol](#communication-protocol)
4. [API](#api)

## Motivation

ChatOps, a term coined by [GitHub](https://github.com/) and pioneered with [Hubot](https://hubot.github.com/), has become a popular pattern for people driving automation through online chat.
New chat services such as [Slack](https://slack.com/) and [HipChat](https://www.hipchat.com/) have driven popular interest in group chat for teams, and there is now a large demand for chat services to integrate with automated tools and third-party services.
Hubot is widely deployed, and chat bot frameworks for other languages such as [Lita](https://www.lita.io/) and [Err](http://errbot.io/en/latest/) have also emerged and become popular.
There are also commercial services that use or promote ChatOps, such as [VictorOps](https://victorops.com/), [StackStorm](https://stackstorm.com/), and [Cog](https://operable.io/).

The competition between tools has been beneficial to drive innovation, but there is a lot that could be gained from interoperability between tools.
OpenChatOps aims to provide a system where the popular combination of chat bot core framework + adapter for each chat service + user-authored plugins for functionality is unified across different chat bot frameworks and programming languages.
The goal is that any executable chat bot program can run compliant plugins (referred to as "callback programs" in this spec) regardless of the framework they were built with or the programming language used.

Additional goals of OpenChatOps are to provide increased capacity for the scalability of a chat bot deployment and to provide some degree of a sandboxing mechanism for the robot and callback programs, ensuring that they only communicate with each other and are given access to data based on established APIs.
This is achieved through an architecture that promotes plugins that have traditionally run as part of the chat bot process itself to separate programs that run in isolation and communicate over a network.
The ability to orchestrate and deploy an architecture like this is significantly easier today than it would have been when Hubot was first released, due to tools like [Docker](https://www.docker.com/), [rkt](https://coreos.com/rkt/), and [Kubernetes](http://kubernetes.io/).

## Architecture

The OpenChatOps architecture is made up of multiple programs that run as independent processes:

1. One chat bot
2. One core data storage backend
3. Zero or more specific data stores
4. Zero or more callbacks programs

### Chat bot

The chat bot is the primary program.
It implements APIs for receiving incoming messages, sending outgoing messages, managing chat users and chat rooms, and persistent data storage.
The chat bot runs an RPC server that exposes its APIs to other programs over a network.
Chat bots may be specific to one chat service or may support multiple services.
If multiple services are supported, the user is able to select which they want to use via configuration.

### Core data storage

A chat bot requires a data store for persisting data such as user accounts, chat room information, and authorization rules.
The interface to this core data storage is a very simple key-value store.
A chat bot may require specific database software or may support multiple database.
If multiple databases are supported, the user is able to select which they want to use via configuration.

### Specific data storage

Certain callback programs may require the use of particular databases.
For example, a callback program may require relational database capabilities via a database like [PostgreSQL](https://www.postgresql.org/), or unique features provided by [Redis](http://redis.io/) like sorted sets.
For these situations, the necessary databases are declared by callback programs as required dependencies, and the callback programs can only run with a bot that provides an API to use those specific databases.
The details of these APIs are not yet designed.

### Callback program

A callback program is an program that provides arbitrary runtime functionality for the chat bot.
The callback program registers interest with the chat bot for patterns in incoming chat messages and other data, and execute code in response.
Callback programs communicate with the chat bot using the RPC protocol.
Callback plugins are not required to run the chat bot, though they are necessary to provide additional functionality and user customization.
There is no limit to how many can be run and registered with the bot imposed by the specification, but system resources may impose a natural limit.

## Communication protocol

OpenChatOps bots and callback programs communicate schema-based, strongly-typed data with each other over the network using Google's [gRPC](http://www.grpc.io/).
While gRPC provides the RPC protocol, it leans on Google's [Protocol Buffers](https://developers.google.com/protocol-buffers/) to define the data to be transmitted over the RPC protocol and the interfaces of the services that expose RPC API endpoints.
Protocol Buffers uses a programming language-agnostic interface description language (IDL) for schema definition.
gRPC's RPC system is built on top of [HTTP/2](https://http2.github.io/), which uses a binary format for efficient network transfer.
Each callback program has a unique secret key that is known only to itself and the chat bot, to ensure other entities that can reach the chat bot over the network cannot impersonate the callback program.

In short:

* The OpenChatOps API's services (endpoints) and messages (data types) are defined in the Protocol Buffers IDL.
* When authoring an OCO-compliant chat bot or callback program for a particular language, supporting code for that language is compiled from the Protocol Buffers IDL files.
* This generated code includes all of the data types, scaffolding for implementing the main RPC server's API, and client code to make requests to the API in a way that feels natural for that language and hides the details of the protocol.

## API

Note: The API is in very early stages. It is incomplete and does not currently represent the full capabilities planned.

The OpenChatOps API is expressed in the Protocol Buffers interface description language in the `proto` directory.
All services, service methods, messages, and message fields are documented with inline comments.
A higher level overview of the API will follow in this section.

## Reference implementation

[Rustin](https://github.com/openchatops/rustin) is a prototype for OpenChatOps being developed as a reference implementation.

## Community

To participate in the development of OpenChatOps, please feel free to join our chat.
It is available through:

* [#openchatops:matrix.org](https://vector.im/beta/#/room/#openchatops:matrix.org) on the Matrix network
* [#openchatops](https://webchat.freenode.net/?channels=openchatops) on the Freenode IRC network

Issues and pull requests to any OpenChatOps repository on GitHub are also welcome.

## License

The OpenChatOps specification is available under the [MIT License](http://opensource.org/licenses/MIT). See the attached LICENSE file.
