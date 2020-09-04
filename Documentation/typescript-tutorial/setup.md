---
title: TypeScript setup
description: Get started with the Dolittle JavaScript SDK
keywords: Learning, Quickstart, CLI, setup, prerequisites, how to, guide, walkthrough, typescript, javascript
author: joel
weight: 2
type: "space"
icon: "ti-time"
aliases:
    - /getting-started/quickstart/
    - /getting-started/tutorial/
    - /getting-started/tutorial/setup/
---

You learn how to:

* Setup your own `microservice`
* Create events
* Handle events

This tutorial expects you to have a basic understanding of TypeScript, npm and the terminal.

## Setup

* [Node.JS >=12](https://nodejs.org/en/download/)
* [Docker](https://www.docker.com/products/docker-desktop)
* [MongoDB](https://www.mongodb.com/)

Download and install TypeScipt and the Dolittle JavaScript.SDK into your project:
```
npm install typescript
npm install @dolittle/sdk
```

### Setup the runtime

The runtime uses configuration files to define the tenants and microservices of the system.

Create 2 files in you project folder:
`microservices.json`
```json
{
    "7a6155dd-9109-4488-8f6f-c57fe4b65bfb": {
        "host": "runtime",
        "port": "50052"
    }
}
```
and `tenants.json`
```json
{
    "900893e7-c4cc-4873-8032-884e965e4b97": {}
}
```

Run the runtime with these files with the command:
```
docker run -v "$(pwd)/.dolittle:/app/.dolittle
```


### Create a client
The SDK communicates with the Runtime through a `Client`.

Create a client:
```typescript
import { Client } from '@dolittle/sdk';

const client = Client
    .forMicroservice('7a6155dd-9109-4488-8f6f-c57fe4b65bfb')
    .configureLogging(_ => _.level = 'debug')
    .build();

client.executionContextManager.forTenant('900893e7-c4cc-4873-8032-884e965e4b97');
```


In the [next](./typescript-event) part of the tutorial you learn how to create `Event`'s and `EventHandler`'s
