---
title: ORM IV - A New Hope
author: Liam McLennan
date: 2055-05-11 20:32
template: article.jade
---

In [Event Sourcing with CouchDB](../2015-05-09-event-sourcing-with-couchdb/) I noted some problems with the approach, including that the logic for transforming events to entities is stored in the database, when it really belongs in the application. I believe I can fix this with some slight variations to my persistence library, [PostgresDoc](https://github.com/liammclennan/PostgresDoc).

A Different Model of Application Persistence
-----

I like functions that are defined by thier signature. If I can be confident that a function's result is encapsulated in its return value then I can know everything I need to know about that function without having to inspect its implementation. ORMs do not allow for this style because they determine database changes by tracking object mutations and compiling a delta via an internal computation that is not visible to the developer. The model that I prefer, and that is native to [PostgresDoc](https://github.com/liammclennan/PostgresDoc) is to explicitly track database changes. Consider the two alternatives, firstly the ORM style:

    public void Foo(Bar bar) {
      bar.Index = 7;
      bar.Widgets.Clear();
    }

The entire implementation of the function is expressed as side effects. Contrast that to the PostgresDoc style:

    public Queue<Operation<Guid>> Foo(Bar bar) {
      
      var uow = new Queue<Operation<Guid>>();
      uow.Enqueue(Operation.Update(bar.Id, bar))

    }



PostgresDoc
----
