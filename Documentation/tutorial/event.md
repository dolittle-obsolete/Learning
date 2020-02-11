---
title: Add an event
description: Learn to add an event
keywords: Learning, tutorial, event
author: Joel Hoisko
weight: 4
---

## Event Driven Architecture

### Add an event
Inside `Banking/Events/` create a new folder called `Accounts`

```console
cd Banking/Events/
mkdir Accounts
```

Inside `Accounts` create a file called `DebitAccountOpened.cs` with the following content:

```csharp
using System;
using Dolittle.Events;

namespace Events.Accounts
{
    public class DebitAccountOpened : IEvent
    {
        public DebitAccountOpened(Guid accountId, string name)
        {
            AccountId = accountId;
            Name = name;
        }

        public Guid AccountId { get; }
        public string Name { get; }
    }
}
```

### Why not Concepts?

### AggergateRoot on() method


In the [next](./read_model) part of the tutorial you learn how to create a `ReadModel`.
