---
title: Add a command
description: Learn to add a command
keywords: Learning, tutorial, command
author: Joel Hoisko
weight: 2
---

In this part you learn to write a `Command` to open a debit account and understand how Dolittle is built on CQRS.

Dolittle is built on the principles of CQRS, Event Driven Architecture and Domain Driven Design.

## CQRS

### Command

{{<mermaid align="left">}}
graph TD;
    A[OpenDebitAccount : ICommand] -->B[OpenDebitAccountInputValidation : CommandInputValidatorFor< OpenDebitAccount >]
    B --> C[DebitAccountsCommandHandlers : ICanHandleCommands]
    C -->D[DebitAccounts : AggregateRoot]
{{< /mermaid >}}

## Add a Command to our web app
Inside `Banking/Domain/` create a new folder called `Accounts`

```console
cd Banking/Domain/
mkdir Accounts
```

Inside `Accounts` create a new file called `OpenDebitAccount.cs` with the following content:

```csharp
using Concepts.Accounts;
using Dolittle.Commands;

namespace Domain.Accounts
{
    public class OpenDebitAccount : ICommand
    {
        public CustomerId CustomerId { get; set; }
        public string Name { get; set; }
    }
}
```

This defined a simple `command` with the intent of opening a debit account and with 2 properties, `CustomerId` and `Name`. A `command` has to implement the `ICommand` interface to get invoked up by the DI system.

`Commands` should be named in the imperative form in accordance to the inten of the user. For example `UpdateDebitAccounts` is a poor command name. It focuses on what will happen, rather than why the user wants o perform the action.

### Add a Concept
Create a folder `Concepts/Accounts/` 
```console
cd Banking/Concepts/
mkdir Accounts
```

And within it create a file called `CustomerId.cs` with the following content:

```csharp
using System;
using Dolittle.Concepts;
using Dolittle.Runtime.Events;

namespace Concepts.Accounts
{
    public class CustomerId : ConceptAs<Guid>
    {
        public static implicit operator CustomerId(Guid customerId) => new CustomerId { Value = customerId };
        public static implicit operator EventSourceId(CustomerId accountId) => accountId.Value;
        public static implicit operator CustomerId(EventSourceId eventSourceId) => new CustomerId { Value = eventSourceId.Value };
    }
}
```
Use `concepts` to create meaningful types within your code with their own custom handling. 

This `concept` declares our own custom type `CustomerId` and how it can be casted from various different types like `EventSourceId` and `Guid`.



### Input validation
Within `Domain/Accounts/` create a file called `OpenDebitAccountInputValidation.cs` with the following content:

```csharp
using System;
using Concepts.Accounts;
using Dolittle.Commands.Validation;
using FluentValidation;

namespace Domain.Accounts
{
    public class OpenDebitAccountInputValidation : CommandInputValidatorFor<OpenDebitAccount>
    {
        public OpenDebitAccountInputValidation()
        {
            RuleFor(_ => _.CustomerId)
                .NotNull()
                .NotEmpty()
                .NotEqual((CustomerId)Guid.Empty).WithMessage("Customer identifier is required");

            RuleFor(_ => _.Name).NotEmpty().WithMessage("Name is required");
        }
    }
}
```

With `CommandInputValidatorFor` you can create input validators that run on the `commands` parameters before being created.

In the [next](./command_handler) part of the tutorial you learn how to create a `CommandHandler`.

