---
title: Add an aggregate root
description: Learn to add an aggregate root
keywords: Learning, tutorial, aggregate root
author: Joel Hoisko
weight: 3
---


In this part you learn to write an `AggregateRoot` and a `CommandHandler` for the `OpenDebitAccount` `command`.

### AggregateRoots, CommandHandler and Commands
TODO diagram or explanation on how they tie together etc

### Add a `CommandHandler`
Within `Domain/Accounts/` create a file called `DebitAccountsCommandHandlers.cs` with the following content:

```csharp
using System;
using Dolittle.Commands.Handling;
using Dolittle.Domain;

namespace Domain.Accounts
{
    public class DebitAccountsCommandHandlers : ICanHandleCommands
    {
        private readonly IAggregateOf<DebitAccounts> _debitAccounts;

        public DebitAccountsCommandHandlers(IAggregateOf<DebitAccounts> debitAccounts)
        {
            _debitAccounts = debitAccounts;
        }

        public void Handle(OpenDebitAccount command)
        {
            _debitAccounts
                .Rehydrate(command.CustomerId)
                .Perform(_ => _.Open(Guid.NewGuid(), command.Name));
        }
    }
}
```
This `CommandHandler` handles the incoming `OpenDebitAccount` command and calls for the respective `AggregateRoot` with the commands parameters.

#### IAggegateOf



### Add an `AggregateRoot`
Within `Domain/Accounts/` create a file called `DebitAccounts.cs` with the following content:

```csharp
using System.Collections.Generic;
using Concepts.Accounts;
using Dolittle.Domain;
using Dolittle.Rules;
using Dolittle.Runtime.Events;
using Events.Accounts;

namespace Domain.Accounts
{
    public class DebitAccounts : AggregateRoot
    {
        static Reason AccountNameAlreadyExists = Reason.Create("cd13df2e-daad-4f5b-9bee-2fff38baec21", "Account with {Name} already exists");
        readonly List<string>    _accounts = new List<string>();

        public DebitAccounts(EventSourceId eventSourceId) : base(eventSourceId) { }

        public void Open(AccountId accountId, string name)
        {
            if( Evaluate(() => AccountNameShouldBeUnique(name)) )
                Apply(new DebitAccountOpened(accountId, name));
        }

        private RuleEvaluationResult AccountNameShouldBeUnique(string name)
        {
            if( _accounts.Contains(name) ) return RuleEvaluationResult.Fail(name, AccountNameAlreadyExists.WithArgs(new{Name=name}));
            return RuleEvaluationResult.Success;
        }

        void On(DebitAccountOpened @event)
        {
            _accounts.Add(@event.Name);
        }
    }
}
```

#### Add rules to AggregateRoot
In the `DebitAccounts` add the following :

#### Naming

#### Listening to events

#### AccountID Concept
Within `Concepts/Accounts/` create a file called `AccountId.cs` with the following content:

```csharp
using System;
using Dolittle.Concepts;
using Dolittle.Runtime.Events;

namespace Concepts.Accounts
{
    public class AccountId : ConceptAs<Guid>
    {
        public static implicit operator AccountId(Guid accountId) => new AccountId { Value = accountId };
        public static implicit operator EventSourceId(AccountId accountId) => accountId.Value;
        public static implicit operator AccountId(EventSourceId eventSourceId) => new AccountId { Value = eventSourceId.Value };
    }
}
```


In the [next](./event) part of the tutorial you learn how to create an `Event`.

