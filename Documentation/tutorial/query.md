---
title: Add a query
description: Learn to add a query
keywords: Learning, tutorial, ReadModel, Query
author: Joel Hoisko
weight: 6
---

## Query

### Add a Query

Within `Read/TodoItem/` create a file called `AllAvailableLists.cs` with the following content:

```csharp
using System;
using System.Linq;
using Dolittle.Queries;
using Dolittle.ReadModels;

namespace Read.TodoItem
{
    public class AllAvailableLists : IQueryFor<AvailableLists>
    {
        readonly IReadModelRepositoryFor<AvailableLists> _repositoryForAvailableLists;

        public AllAvailableLists(IReadModelRepositoryFor<AvailableLists> repositoryForAvailableLists)
        {
            _repositoryForAvailableLists = repositoryForAvailableLists;
        }

        // Todo:
        public IQueryable<AvailableLists> Query => _repositoryForAvailableLists.Query;
    }
}
```

In the [next](./aurelia) part of the tutorial you learn how to create the Aurelia front-end.

