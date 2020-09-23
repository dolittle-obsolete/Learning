---
title: TypeScript
description: Get started with the Dolittle JavaScript SDK using TypeScript
keywords: Learning, Quickstart, setup, prerequisites, how to, guide, walkthrough, typescript, javascript
author: joel, jakob, sindre
weight: 2
---

Welcome to the Dolittle TypeScript tutorial for the JavaScript.SDK!

In this tutorial you learn how to write a microservice that commits an event and handles the committed event.

This tutorial expects you to have a basic understanding of TypeScript, npm and Docker.

### What you'll need
Install and configure the required prerequisites locally first:

* [Node.js >= 12](https://nodejs.org/en/download/)
* [Docker](https://www.docker.com/products/docker-desktop)

### Setup a TypeScript project
Setup a NodeJS project using your favorite package manager and add TypeScript and the Dolittle JavaScript SDK into your project:

```shell
$ npm -D install typescript ts-node
$ npm install @dolittle/sdk
```

### Create an event type
Create an event type and give it an EventType with a GUID.

```typescript
// MySpicyEvent.ts
import { eventType } from '@dolittle/sdk.events';

@eventType('1844473f-d714-4327-8b7f-5b3c2bdfc26a')
export class MySpicyEvent {
    myString: string;
    myNumber: number;
}
```

The `@eventType('1844473f-d714-4327-8b7f-5b3c2bdfc26a')` identifies this event so that it can be serialized.

### Create an event handler
Create an event handler to handle your event and give it a GUID.
```typescript
// MySpicyEventHandler.ts
import { EventContext } from '@dolittle/sdk.events';
import { eventHandler, handles } from '@dolittle/sdk.events.handling';
import { MySpicyEvent } from './MySpicyEvent';

@eventHandler('f2d366cf-c00a-4479-acc4-851e04b6fbba')
export class MySpicyEventHandler {

    @handles(MySpicyEvent)
    mySpicyEvent(event: MySpicyEvent, eventContext: EventContext) {
        console.log('MySpicyEvent was handled.', event);
    }
}
```

The `@eventHandler('f2d366cf-c00a-4479-acc4-851e04b6fbba')` identifies this event handler in the Runtime, and is used to keep track of which event it last processed, and retrying the handling of an event if it fails.

### Connect the client and commit an event
Create a client that connects to the Runtime for a Microservice with the id `'f39b1f61-d360-4675-b859-53c05c87c0e6'`. This Microservice is predefined in the Runtime.

```typescript
import { EventContext } from '@dolittle/sdk.events';
import { MySpicyEvent } from './MySpicyEvent';
import { MySpicyEventHandler } from './MySpicyEventHandler';

const client = Client
    .forMicroservice('f39b1f61-d360-4675-b859-53c05c87c0e6')
    .withEventTypes(eventTypes =>
        eventTypes.associate(MySpicyEvent))
    .withEventHandlers(builder => {
        builder.registerEventHandler(MySpicyEventHandler);
    })
    .build();

const mySpicyEvent: MySpicyEvent = {
    myString: 'hot kimchi bowl'
    myNumber: 15
}
client.EventStore
    .forTenant('445...')
    .commit(mySpicyEvent, 'bfe6f6e4-ada2-4344-8a3b-65a3e1fe16e9');
```

### Start the Dolittle Runtime
Start the Dolittle Runtime with all the necessary dependencies with the following command:

```shell
$ docker run -p 50053:50053 -p 27017:27017 dolittle/runtime:local
```

This command starts a local Dolittle Runtime on port 50053 and a MongoDB server on port 27017.

### Run your spicy microservice
Run your code with:

```shell
$ npx ts-node index.ts
```

Now you should see some cool console logs...
GLHF!
