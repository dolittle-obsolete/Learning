---
title: C# & .NET
description: Get started with the Dolittle C# SDK using TypeScript
keywords: Learning, Quickstart, setup, prerequisites, how to, guide, walkthrough, csharp, c#, dotnet, .NET
author: joel, jakob, sindre
weight: 1
---

Welcome to the Dolittle C# tutorial for the [.NET SDK](https://github.com/dolittle/dotnet.sdk) where you learn how to write a microservice that keeps track of foods prepared by the chefs.

This tutorial expects you to have a basic understanding of C#, .NET and Docker.

After this tutorial you will have a:

* Running Dolittle Runtime and MongoDB
* Microservice that commits and handles events

### What you'll need
Install and configure the required prerequisites locally first:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com/products/docker-desktop)

### Setup a .NET Core console project
Setup a .NET Core console project:

```shell
$ dotnet new console
$ dotnet add Dolittle.SDK 
```

### Create an `EventType`
First we'll create an `EventType` that represents that a dish has been prepared. Events represents changes in the system, a _"fact that has happened"_. As the event is a _"fact"_ it's immutable by definition, and as the event _"has happened"_ we should name it in the past tense accordingly.

```csharp
namespace Head 
{
    // an example GUID, you should create your own
    [EventType("1844473f-d714-4327-8b7f-5b3c2bdfc26a")]
    public class DishPrepared
    {
        // wtf these are mutable??
        public string Dish { get; set; }
        public string Chef { get; set; }
    }
}
```
// acshkually this file isn't an EventType as that is our internal thing aarghhhh.
An `EventType` is a class that defines the properties of the event but it does not contain any calculations or behaviour. The unique GUID given in the `[EventType()]` attribute is used to identify and deserialize this type in other Microservices (more on that later).

### Create an `EventHandler`
We need something that can respond to when a dish has been prepared. `EventHandlers` react to events that have been committed succesfully.

```csharp
using System;
using System.Threading.Tasks;

namespace Head
{
    // an example GUID, you should create your own
    [EventHandler("f2d366cf-c00a-4479-acc4-851e04b6fbba")]
    public class DishHandler
    {
        public void Handle(DishPrepared @event, EventContext eventContext)
        {
            Console.WriteLine($"{@event.Chef} has prepared {@event.Dish}. Yummm!");
        }
    }
}
```

When an event is succesfully committed, the `Handle()` method will be called for all the `EventHandler`'s with the correct signature for that particular type.

The `[EventHandler("f2d366cf-c00a-4479-acc4-851e04b6fbba")]` attribute identifies this event handler in the Runtime, and is used to keep track of which event it last processed, and retrying the handling of an event if it fails.

### Connect the client and commit an event
Let's build a client that connects to the Runtime for a Microservice with the id `"f39b1f61-d360-4675-b859-53c05c87c0e6"`. This Microservice is predefined in the Runtime.

We then register our events and event handlers to the client before a delicious taco has been prepared.

```csharp
using Dolittle.SDK;

namespace Head
{
    class Program
    {
        public static void Main()
        {
            var client = Client
                .ForMicroservice("f39b1f61-d360-4675-b859-53c05c87c0e6")
                .WithEventTypes(eventTypes =>
                    eventTypes.Register<DishPrepared>())
                .WithEventHandlers(builder =>
                    builder.RegisterEventHandler<DishHandler>())
                .Build();

            var preparedTaco = new DishPrepared
            {
                Dish = "Bean Blaster Taco",
                Chef = "Mr. Taco"
            };

            client.EventStore
                .ForTenant("455...")
                .Commit(preparedTaco, "bfe6f6e4-ada2-4344-8a3b-65a3e1fe16e9");

            // blocks the thread to let all the event handlers to keep handling events
            client.Wait();
        }
    }
}
```

Here we create a new event by instantiating the event type with some content. This event is then committed aka saved to the MongoDB for the given tenant. The event handler will then try to handle the event if the commit was successfull.

### Start the Dolittle Runtime
Start the Dolittle Runtime with all the necessary dependencies with the following command:

```shell
$ docker run -p 50053:50053 -p 27017:27017 dolittle/runtime:local
```

This command starts a local Dolittle Runtime on port 50053 and a MongoDB server on port 27017.

### Run your microservice
Run your code with:

```shell
$ dotnet run
```

Now you should see some cool console logs...
GLHF!
