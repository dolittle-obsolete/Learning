# Quickstart
Welcome to the world of Dolittle! In this guide we are going to get you all st up so you can build your own applications usuing the Dolittle Framework.

## Prerequisits
- Have Visual Studio Code installed
- Have .net SDK installed
- Have Docker installed

To get get started, we will install the Dolittle CLI tool. The CLI tool is often used extensively when creating new applications or adding Dolittle components to it.

## Installing the CLI
### 1) Install node on your computer. 
I usually stick with the LTS version.
https://nodejs.org/en/

### 2) Install the Dolittle CLI tool
```console
npm install -g @dolittle/cli
```


### 3) Test if the CLI tool works
Run dolittle in your command window, it should output the supported commands.
```console
dolittle
```
![Dolittle CLI](/dolittleCLI.png)

## Building your first application
### 1) Create a new application
We will use the cli tool to create a new application.

The first thing we need is a folder where our application will live.
Create a new folder somewhere on your computer, and name it DolittleQuickstart.
```console
mkdir DolittleQuickstart
```

Go into that folder:
```console
cd DolittleQuickstart
```

To create a ne application using the CLI tool, we will use the dolittle create application command.
```console
dolittle create application DolittleQuickstart
```

The application has now been created. 

### 2) Create a bounded context
Each application needs to have one more more Bounded Contexts. We will use the CLI tool to add a Bounded Context to our application.
```console
dolittle create boundedcontext QuickstartBC
```

A bounded context is now added to the application, located inside the QuickstartBC folder.

This folder is based on the Bounded Context boilerplate and will contain everything you need to run the Dolittle template application.

### 3) Open the application in Visual Studio code
Launch Visual Studio Code and open the DolittleQuickstart folder as the project, or open it directly from the application folder from a Terminal.
```console
code .
```

### 4) Build and Run the Template Application
To run the application, we will use the Dolittle CLI tool again. From the QuickstartBC folder, run the following command.
```console
dolittle run
```

### 5) The template application
You should see our template app running on localhost.
![Dolittle CLI](/templateApp.png)

## Using our Dolittle Extension in Visual Studio Code
We also have an extension for Visual Studio Code that allows you to add Dolittle components to you app without using the CLI tool, if you prefer that.
[Dolittle Extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=Dolittle.dolittle-vscode)
