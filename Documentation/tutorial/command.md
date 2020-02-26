---
title: Add a command
description: Learn to add a command
keywords: Learning, tutorial, command
author: Joel Hoisko
weight: 2
---

In this part you learn to write a [`Command`]() to create a new item on the todo list. The `Command` will use [`Concepts`]() and [`Values`]() to have meaningful types as it's properties.

## CQRS
Dolittle is built on the principles of [CQRS](), [Event Driven Architecture]() and [Domain Driven Design]().

### Command

TODO: think of a nicer graph

{{<mermaid align="left">}}
graph TD;
    A[OpenDebitAccount : ICommand] -->B[OpenDebitAccountInputValidation : CommandInputValidatorFor< OpenDebitAccount >]
    B --> C[DebitAccountsCommandHandlers : ICanHandleCommands]
    C -->D[DebitAccounts : AggregateRoot]
{{< /mermaid >}}


### Add a Command to our web app
Inside `ToDo/Domain/` create a new folder called `TodoItem`

```shell
cd ToDo/Domain/
mkdir TodoItem
```

Inside `TodoItem` create a new file called `CreateItem.cs` with the following content:

```csharp
using Concepts.TodoItem;
using Dolittle.Commands;

namespace Domain.TodoItem
{
    public class CreateItem : ICommand
    {
        public TodoText Text { get; set; }
        public ListId List { get; set; }
    }
}
```

This defines a simple `command` with the intent of creating a new item with 2 properties, `ListId` and `TodoText`. A `command` has to implement the `ICommand` interface to get invoked up by the DI system.

`Commands` should be named in the imperative form in accordance to the inten of the user. For example `UpdateTodoItem` is a poor command name. It focuses on what will happen, rather than why the user wants to perform the action.

`Commands` don't use primitive's in their properties, rather they use `Values` and `Concepts`.

#### Add TodoText Value
Create a folder `Concepts/TodoItem/` 
```shell
cd ToDo/Concepts/
mkdir TodoItem
```

And within it create a file called `TodoItem.cs` with the following content:

```csharp
using Dolittle.Concepts;

namespace Concepts.TodoItem
{
    public class TodoTask : Value<TodoTask>
    {
        public TodoText Text { get; set; }
        public TaskStatus Status { get; set; } = TaskStatus.NotDone;
    }
}
```

[`Values`]({{< ref "concepts_and_value_objects.md#value-objects" >}}) hold within them [`Concepts`]({{< ref "concepts_and_value_objects.md#conceptas-t" >}}). These are used to create meaningful types within your code with their own custom handling. 

`Value` objects don't have any key that identifies them for they are identified by the properties that compromise it.

Next create the required `concepts` that lay inside the `TodoTask`. Within `Concepts/TodoItem` folder create a file called `TodoText.cs` with the following content:

```csharp
using Dolittle.Concepts;

namespace Concepts.TodoItem
{
    public class TodoText : ConceptAs<string>
    {
        public static implicit operator TodoText(string value)
        {
            return new TodoText {Value = value};
        }
    }
}
```

This `concept` declares our own custom type `TodoText`. This type is not that interesting as it only converts from a `string` to a `TodoText`.

Next create the concept of tracking a tasks status, wether it is done or not. Within `Concepts/TodoItem` folder create a file called `TaskStatus.cs` with the following content:

```csharp
using System;
using Dolittle.Concepts;

namespace Concepts.TodoItem
{
    public class TaskStatus : ConceptAs<string>
    {
        const string _done = "done";
        const string _not_done = "not done";
        const string _unknown = "unknown status";
        public static readonly TaskStatus Done = new TaskStatus { Value = _done };
        public static readonly TaskStatus NotDone = new TaskStatus { Value = _not_done };
        public static readonly TaskStatus Unknown = new TaskStatus { Value = _unknown };
    
        public static implicit operator TaskStatus(string value)
        {
            if (_done.Equals(value, StringComparison.InvariantCultureIgnoreCase))
            {
                return Done;
            }
            if (_not_done.Equals(value, StringComparison.InvariantCultureIgnoreCase))
            {
                return NotDone;
            }
            return Unknown;
        }
    }
}
```

These 2 concepts make up a single `TodoTask` `value`.

#### Add ListId Concept
Within `Concepts/TodoItem/` create a file called `ListId.cs` with the following content:

```csharp
using Dolittle.Concepts;
using System;

namespace Concepts.TodoItem
{
    public class ListId : ConceptAs<Guid>
    {
        public static ListId None = default(Guid);

        public static implicit operator ListId(Guid value)
        {
            return new ListId {Value = value};
        }
    }
}
```

Now the `CreateItem` command is complete.

### Input validation
Each `command` goes through it's own [input validator]() before being created.

Within `Domain/TodoItem/` create a file called `CreateItemInputValidator.cs` with the following content:

```csharp
using Dolittle.Commands.Validation;
using FluentValidation;

namespace Domain.TodoItem
{
    public class CreateItemInputValidator : CommandInputValidatorFor<CreateItem>
    {
        public CreateItemInputValidator()
        {
            RuleFor(cmd => cmd.List)
                .NotEmpty()
                .WithMessage("Please set the list to add to");

            RuleFor(cmd => cmd.Text)
                .NotEmpty()
                .WithMessage("Need some text in the todo item");
        }
    }
}
```

In the [next](./aggregate_root) part of the tutorial you learn how to create a `AggregateRoot` and `CommandHandler`.

