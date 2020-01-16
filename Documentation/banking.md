---
title: Get started with Dolittle
description: Create a banking web app with Dolittle
keywords: Learning, Quickstart, CLI, Tools
author: Joel Hoisko
weight: 20
---

# Getting started with Dolittle

This tutorial teaches the basics of using Dolittle to create a web-application. 

The app manages users in an online bank. You learn how to:

* Create a web app
* Use `Commands` to create `Events`
* Process `Events` into a database
* Create  `Queries` for `ReadModels`

This tutorial expects you to have a basic understanding of .NET, C# and npm.

At the end you have an app that can manage bank users and their accounts.

This example based off of a [Banking sample.](https://github.com/dolittle-samples/Bank)

## Prerequisites

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)
* [Node.js >=12](https://nodejs.org/en/download/)

### Install the CLI

The [Dolittle CLI tool]({{< relref "/tooling/cli/_index.md" >}}) makes developing Dolittle applications easier by providing boilerplate code to bootstrap your application.

1. Install the CLI tool
```console
npm install -g @dolittle/cli
```

{{% notice tip %}}
It is recommended to install the CLI tool globally so that you can use it anywhere to create new projects, the finished application does not depend on the CLI tool.
{{% /notice %}}


2. Test that the CLI tool works
Run the CLI tool in your command window, it will show the available commands for you to use:
```console
dolittle
```
![Dolittle CLI](../images/dolittleCLI.png)


## Create a web app
In this part you will create a blank Dolittle web application with the [Dolittle CLI tool]({{< relref "/tooling/cli/_index.md" >}}).

### 1) Create a new application
Use the CLI tool to create a new [application]().

The first thing you need is a folder where our application will live.
Create a new folder somewhere on your computer, name it Banking and change into that directory.
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

Run this command inside the _BankingApplication_ directory:
```console
dolittle create boundedcontext Banking
```

A bounded context is now added to the application, located inside the folder. This folder is based on the Bounded Context boilerplate and will contain everything you need to run the Dolittle template application.

#### 3) Open the application in Visual Studio code
Launch Visual Studio Code and open the ToDolittle folder as the project, or open it directly from the application folder from a Terminal.
```console
code .
```

#### 4) Build and Run the Template Application
To run the application, we will use the Dolittle CLI tool again. From the TodoTracking folder, run the following command.
```console
dolittle run
```

#### 5) The template application
You should see our template app running on localhost.
![Dolittle CLI](../images/templateApp.png)

## Using our Dolittle Extension in Visual Studio Code
We also have an extension for Visual Studio Code that allows you to add Dolittle components to you app without using the CLI tool, if you prefer that.
[Dolittle Extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=Dolittle.dolittle-vscode)

# Continue learning
We have created a Getting Started lesson that will take you through all the steps needed to build a ToDo application that has a Dolittle backend, and an Aurelia frontend.
The guide is soon available here: Link
