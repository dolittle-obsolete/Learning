---
title: Setup
description: Get started with the Dolittle JavaScript SDK
keywords: Learning, Quickstart, CLI, setup, prerequisites, how to, guide, walkthrough, typescript, javascript
author: joel
weight: 2
aliases:
    - /getting-started/typescript-tutorial/quickstart/
    - /getting-started/typescript-tutorial/tutorial/
    - /getting-started/typescript-tutorial/tutorial/setup/
---

Welcome to the Dolittle TypeScript tutorial for the JavaScript.SDK!

In this tutorial you learn how to:

* Setup up the runtime and client locally
* Create events
* Handle events

This tutorial expects you to have a basic understanding of TypeScript, npm and the terminal.

### Prerequisites
Install the required prerequisites locally first:

* [Node.JS >=12](https://nodejs.org/en/download/)
* [Docker](https://www.docker.com/products/docker-desktop)

Download and install TypeScipt and the Dolittle JavaScript.SDK into your project:
```
npm install typescript
npm install @dolittle/sdk
```

### Connect the client
Create a simple client that connects to the microservice with an id of `'f39b1f61-d360-4675-b859-53c05c87c0e6'` and set the current tenant to the default id of `'445f8ea8-1a6f-40d7-b2fc-796dba92dc44'`:
```typescript
import { Client } from '@dolittle/sdk';

const client = Client
    .forMicroservice('f39b1f61-d360-4675-b859-53c05c87c0e6')
    .build();

client.executionContextManager.currentFor('445f8ea8-1a6f-40d7-b2fc-796dba92dc44');
```

### Run the runtime
The runtime is a docker image which you can run with the following command:
```
docker run dolittle/runtime
```

Now you have a client that can start interacting with the runtime.

In the [next](./events) part of the tutorial you learn how to create `Event`'s and `EventHandler`'s
