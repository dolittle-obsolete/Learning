---
title: Add an aggregate root
description: Learn to add an aggregate root
keywords: Learning, tutorial, aggregate root
author: Joel Hoisko
weight: 3
---


In this part you learn to write an [`AggregateRoot`]() and a [`CommandHandler`]() to apply business logic to your ToDo application.

### AggregateRoots, CommandHandler and Commands
TODO diagram or explanation on how they tie together etc

### Add a `CommandHandler`
Within `Domain/TodoItem/` create a file called `ItemHandlers.cs` with the following content:

```csharp
using Dolittle.Commands.Handling;
using Dolittle.Domain;

namespace Domain.TodoItem
{
    public class ItemHandler : ICanHandleCommands
    {
        // hold's a list of all the TodoList aggregate roots
        readonly IAggregateOf<TodoList>  _todoList;

        public ItemHandler(IAggregateOf<TodoList>  todoList)
        {
             _todoList =  todoList;
        }

        public void Handle(CreateItem cmd)
        {
            _todoList
                .Rehydrate(cmd.List.Value)
                .Perform(_ => _.Add(cmd.Text));
                
        }
    }
}
```

This `CommandHandler` handles the incoming `CreateItem` command and calls for the respective `AggregateRoot` with the commands parameters.

#### IAggegateOf

TODO: explain quicly Rehydrate and perform


### Add an `AggregateRoot`
Within `Domain/TodoItem/` create a file called `TodoList.cs` with the following content:

```csharp
using Concepts.TodoItem;
using Dolittle.Domain;
using Dolittle.Runtime.Events;
using Events.TodoItem;

namespace Domain.TodoItem
{
    public class TodoList : AggregateRoot
    {
        public TodoList(EventSourceId id) : base(id)
        { 
            
        }

        public void Add(TodoText text)
        {
            var createdEvent = new ItemCreated(
                EventSourceId,
                text
            );

            Apply(createdEvent);
        }
    }
}
```

An [`AggregateRoot`]({{< ref "/runtime/runtime/domain_driven_design/aggregate_root" >}}) is an object or collection of objects ([entities]({{< ref "concepts_and_value_objects.md#entities" >}}) and [value objects]({{< ref "concepts_and_value_objects.md#value-objects" >}}) that need to be treated as a single unit for business logic.

The `aggregate root` also takes care of instantiating and applying `Events`.

#### Add rules to AggregateRoot
TODO

In the [next](./event) part of the tutorial you learn how to create an `Event`.

