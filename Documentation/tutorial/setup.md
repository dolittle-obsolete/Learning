---
title: Setup
description: Initial Dolittle setup
keywords: Learning, Quickstart, CLI, Tools
author: Joel Hoisko
weight: 1
aliases:
    - /getting-started/quickstart/
    - /getting-started/
---

This tutorial teaches the basics of using Dolittle to create a simple banking web-application. 

You learn how to:

* Create a web app
* Use `Commands` to create `Events`
* Process `Events` into a database
* Create  `Queries` for `ReadModels`

This tutorial expects you to have a basic understanding of .NET, C#, TypeScript, npm and the terminal.

At the end you have an app that can manage bank users and tgeir accounts.

This example based off of a [Banking sample.](https://github.com/dolittle-samples/Bank)

## Prerequisites

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)
* [Node.js >=12](https://nodejs.org/en/download/)
* [Docker](https://www.docker.com/products/docker-desktop)

### Install the CLI

The [Dolittle CLI tool]({{< relref "/tooling/cli/_index.md" >}}) makes developing Dolittle applications easier by providing boilerplate code to bootstrap your application.

{{% notice tip %}}
There is also an [extension](https://marketplace.visualstudio.com/items?itemName=Dolittle.dolittle-vscode) for Visual Studio Code that allows you to add Dolittle components to you app without using the CLI tool. This tutorial recommends you to have the CLI tool for the examples.
{{% /notice %}}

1. Install the CLI tool
```console
npm install -g @dolittle/cli
```
{{% notice tip %}}
It is recommended to install the CLI tool globally so that you can use it anywhere to create new projects. The finished application does not depend on the CLI tool.
{{% /notice %}}
2. Test that the CLI tool works
Run the CLI tool in your command window, it shows the available commands for you to use:
```console
dolittle
```
![Dolittle CLI](../../images/dolittleCLI.png)


## Create a web app
This part teaches you how to create a blank Dolittle web application with the [Dolittle CLI tool]({{< relref "/tooling/cli/_index.md" >}}).

### 1) Create a new application
Use the CLI tool to create a new [application]().

The first thing you need is a folder for your application. Create a new folder on your computer, name it `BankingApplication` and change into that directory.
```console
mkdir BankingApplication
cd BankingApplication
```

To create a new application using the CLI tool, use the `dolittle create application` command.
```console
dolittle create application BankingApplication
```

{{% notice tip %}}
If you get an error saying:
```console
No application boilerplates found for language 'csharp'
```
You can fix it by running the command:
```console
dolittle boilerplates installed
```
{{% /notice %}}

The basic application scaffolding has now been created. The folder structure should look like this:
```
├── application.json
└── .dolittlerc
```

### 2) Create a bounded context
Each application needs to have one or more [**Bounded Contexts**](). Use the CLI tool to add a Bounded Context to our application.

Run this command inside the _BankingApplication_ directory and choose `MongoDB` for your read models/event store and `Javascript Aurelia` for your web interaction layer:
```console
dolittle create boundedcontext Banking
? Choose the resource implementation for read models MongoDB
? Choose the resource implementation for event store MongoDB
? Choose web interaction layer Javascript Aurelia Interaction Adornment
```

A bounded context is now added to the application, located inside the folder. This folder is based on the Bounded Context boilerplate and contains everything you need to run a Dolittle template application.

Your [project structure](./structure) should now look like this:
```
.
├── .dolittlerc
├── application.json
└── Banking
    ├── Banking.sln
    ├── bounded-context.json
    ├── Concepts
    ├── Core
    ├── Domain
    ├── Domain.Specs
    ├── Events
    ├── Policies
    ├── Policies.Specs
    ├── Read
    ├── Read.Specs
    ├── Rules
    ├── Rules.Specs
    └── Web
```

### 3) Setup and build the front-end
Navigate to the `Web` folder and install all the dependencies:
```console
cd Banking/Web
npm install
```

Then build the front-end:
```console
cd Banking/Web
npm run-script build
```

### 4) Setup a MongoDB database
Start a local docker container that contains a blank MongoDB 4.0.12 server running on port 27017:
```console
docker run -d -p 27017:27017 mongo:4.0.12
```

{{% notice tip %}}
You can also download a GUI for MongoDB like [Compass](https://www.mongodb.com/products/compass) or [Robot 3T](https://robomongo.org/) to look into the database.
{{% /notice %}}

### 5) Build and run the bounded context
Now you have basic Dolittle application [structure](). To test it out, run the following commands from the `BankingApplication` root.
```console
cd Banking/Core/
dotnet run
```

Last lines of the output should be something like this:

```console
Hosting environment: Development
Content root path: /home/joel/Dolittle/TutorialExample/BankingApplication/Banking/Core
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.

```

### 6) Open the web application
Navigate to http://localhost:5000 in your browser to see the running template application.

![Dolittle CLI](../../images/templateApp.png)

In the [next](./command) part of the tutorial you learn how to create a `Command`.

