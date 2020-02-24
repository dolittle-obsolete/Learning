---
title: Getting Started
description: Get started with the Dolittle framework
keywords: Learning, Quickstart, Walkthrough, Guide
author: Dolittle
weight: 1
type: "space"
icon: "ti-time"
repository: https://github.com/dolittle/Learning
---

Dolittle is an open-source, decentralized, distributed, event-driven microservice framework that helps you create [Line-of-Business](https://en.wikipedia.org/wiki/Line_of_business) applications.

The framework is a collection of [C# packages](https://www.nuget.org/profiles/Dolittle) and [Aurelia](https://aurelia.io/) front-end bindings that make it easy to create event-driven web applications using the [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) pattern.

[**Learn how to develop using Dolittle.**]({{< ref setup >}})

### Event Driven Architecture
Dolittle is based on [Event Drive Architecture](https://en.wikipedia.org/wiki/Event-driven_architecture) (EDA). This is an approach to system/application design that focusses on the handling, sending, processing and persistence of events. An event is defined as “something interesting that has happened in your system” and most usually represents a state change or a notification.

EDA promotes loose coupling because the producers of events do not know about subscribers that are listening to this event. This makes an Event-Driven Architecture more suited to today’s distributed applications than the traditional request-response model.

### Microservice
At the heart of Dolittle sits the notion of decoupling. This makes it possible to take a system and break it into small focused components
that can be assembled together in any way one wants to. This is at the core of what is referred to as
[Microservices](https://en.wikipedia.org/wiki/Microservices). The ability to break up the software into smaller more digestable components, makes software written with Dolittle.io easier to understand and maintain. When writing software in a decoupled manner, one gets the
opportunity of composing it back together however one sees fit. You could compose it back in one application running inside a single
process, or you could spread it across a cluster. It really is a deployment choice once the software is giving you this freedom.
When it is broken up you get the benefit of scaling each individual piece on its own, rather than scaling the monolith
equally across a number of machines. This gives a higher density, better resource utilization and ultimately better cost
control.

### Command Query Responsibility Segregation
Most systems have different requirements for the read and the write part of each bounded context. The requirements vary on what is
needed to be written in relation to what is being read and used. The performance characteristics are also for the most part different.
Most line-of-business applications tend to read a lot more than they write. [CQRS](https://en.wikipedia.org/wiki/Command–query_separation#Command_Query_Responsibility_Segregation)
talks about totally segregating the read from the write and treat them uniquely.
One finds [event sourcing]({{< relref event_sourcing >}}) often associated with CQRS, something that Dolittle has embraced and helps
bridge the two sides and stay completely decoupled. It is an optional part of Dolittle but hightly recommended together with an [event store]({{< relref event_store >}}).

![Simple CQRS Diagram](https://github.com/dolittle/home/raw/master/Documentation/images/cqrs.png)

