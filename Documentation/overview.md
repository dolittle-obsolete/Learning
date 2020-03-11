---
title: Overview
description: Learn about the Dolittle framework
keywords: overview, dolittle, why dolittle, what is dolittle, 
author: Dolittle
weight: 1
type: "space"
#icon: "ti-blackboard"
#icon: "ti-book"
icon: "ti-info-alt"
repository: https://github.com/dolittle/Learning
aliases:
    - /getting-started/
---

Dolittle is an open-source, decentralized, distributed and event-driven microservice framework. The framework has been designed to build Line of Business applications without sacrificing architectural quality, code quality, maintainability or scalability.

{{% notice note %}}
The framework is a collection of C# Nuget packages. Find them from our [Nuget profile.](https://www.nuget.org/profiles/Dolittle)
{{% /notice %}}


### Event Driven Architecture
Dolittle is based on an [Event Drive Architecture](https://en.wikipedia.org/wiki/Event-driven_architecture) (EDA). This is an approach to system/application design that focusses on handling, sending, processing and persisting events. An event is defined as “something interesting that has happened in a system” and most usually represents a state change or a notification.

EDA promotes loose coupling because the producers of events do not know about subscribers that are listening to this event. This makes an Event-Driven Architecture more suited to today’s distributed applications than the traditional request-response model.

### Microservice
At the heart of Dolittle sits the notion of decoupling. This makes it possible to take a system and break it into small focused components
that can be assembled together in any way one wants to. This is at the core of what is referred to as
[Microservices](https://en.wikipedia.org/wiki/Microservices). The ability to break up the software into smaller more digestable components, makes software written with Dolittle easier to understand and maintain. When writing software in a decoupled manner, one gets the
opportunity of composing it back together however one sees fit. You could compose it back in one application running inside a single
process, or you could spread it across a cluster. It really is a deployment choice once the software is giving you this freedom.
When it is broken up you get the benefit of scaling each individual piece on its own, rather than scaling the monolith
equally across a number of machines. This gives a higher density, better resource utilization and ultimately better cost
control.


[**Learn how to develop using Dolittle.**]({{< ref setup >}})

