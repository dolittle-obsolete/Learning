---
title: Add an event
description: Learn to add an event
keywords: Learning, tutorial, event
author: Joel Hoisko
weight: 4
---

## Event Driven Architecture
TODO

### Add an event
Inside `ToDo/Events/` create a new folder called `TodoItem`

```console
cd ToDo/Events/
mkdir TodoItem
```

Inside `TodoItem` create a file called `ItemCreated.cs` with the following content:

```csharp
using System;
using Dolittle.Events;

namespace Events.TodoItem
{
    public class ItemCreated : IEvent
    {
        public ItemCreated(Guid listId, string text)
        {
            ListId = listId;
            Text = text;
        }

        public Guid ListId { get; }
        public string Text { get; }
    }
}
```

[`Events`]({{< ref domain_events >}}) are representations of facts that have happened within your [domain](). These events will propagate through your system to be listened by [`EventProcessors`]()

### Why not Concepts?

### AggergateRoot on() method


In the [next](./read_model) part of the tutorial you learn how to create a `ReadModel`.
