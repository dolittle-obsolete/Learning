---
title: Add a query
description: Learn to add a query
keywords: Learning, tutorial, ReadModel, Query
author: Joel Hoisko
weight: 6
---

## Query

### Add a Query

Within `Read/Accounts/` create a file called `AllDebitAccounts.cs` with the following content:

```csharp
using System.Linq;
using Dolittle.Queries;
using MongoDB.Driver;

namespace Read.Accounts
{
    public class AllDebitAccounts : IQueryFor<Account>
    {
        public AllDebitAccounts(IMongoCollection<Account> collection) => Query = collection?.Find(_ => _.Type == AccountType.Debit).ToList().AsQueryable();

        public IQueryable<Account> Query { get; }
    }
}
```

In the [next](./aurelia) part of the tutorial you learn how to create the Aurelia front-end.

