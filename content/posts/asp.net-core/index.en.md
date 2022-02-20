---
weight: 4
title: "ASP.NET Core Introduce"
date: 2022-01-03T19:34:11+08:00
lastmod: 2022-01-03T19:34:11+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["ASP.NET Core", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

.Net Core is Microsoft's new generation of development technology, which not only implements .NET cross-platform development and execution, but also greatly transforms the framework.

<!--more-->

## Introduce

.Net Core is Microsoft's new generation of development technology, which not only implements .NET cross-platform development and execution, but also greatly transforms the framework.

* Advantage
	* Cross-platform (runs on Windows, Mac OS X, Ubuntu Linux) is the first Microsoft web development framework with cross-platform capability and supports cloud.
	* Cross-architecture consistency (executes on different processor architectures of x64, x86, ARM, ARM64, and still remains the same）
	* Flexible deployment (IIS, Apache, Docker available on the host).
	* A unified programming model for MVC and Web API.
	* Open source (open source licensed using MIS and Apache2).
	* Unit testing (testability) and modularity.


##Compare .NET Core / ASP.NET Code / ASP.NET Core MVC

| Compare | .NET Core | ASP.NET Code | ASP.NET Core MVC |
| :-: | :-: | :-: | :-: |
|Represent|.NET Framework | ASP.NET | ASP.NET MVC|
|Illustrate|Platform framework, the broadest technical sense|Web Technology| MVC development framework in ASP.NET Core web page technology|

## MVC

It is a design style, representing the three parts of Model, View and Controller


* Model handles logic and data planes (database access)
* View is responsible for handling UI interface (HTML, Javascript, CSS)
* The Controller is responsible for receiving the Request request, coordinating the Model and View, and sending the response result to the user
 
The advantages of this division of labor are that it can achieve separation of concerns (SoC), a better layered architecture, reduce complexity, and facilitate program maintenance in the future.

## ASP.NET Core MVC?

It is a framework that supports MVC design.

## MVC Implementation Process

1. After the user enters the URL in the browser, a Request will be sent to the server
2. In the middle, it will first go through the Routing routing mechanism to find the corresponding Controller and Action Method
3. Action will call Model to read or update data
4. Model will first perform logical calculation and database retrieval, and then return it to Action
5. Action will throw Model data to View for web page presentation
6. The View Engine writes the final HTML result to the Reponse output data stream and responds to the user's browser.

## MVC Project Folder Function Description

* Properties>launchSettings.json
(The environment configuration file of the local development computer)
* wwwroot folder (public static resource file directory, such as css, js, images, etc.)
* Controller folder (the directory where the Controller control item category is located)
* Model folder (the directory where the Model model category is located)
* View folder (with Home, Shared, _ViewImports.cshtml, _ViewStart.cshtml)
There are two cshtml in the Home directory: Index and Provacy
Shared directory contains _Layout.cshtml, _ValidationScriptsPartial.cshtml, Error.cshtml
* appsettings.json (configuration settings for application use)
* Program.cs (program entry point, mainly responsible for creating Host, environment and configuration settings)
* Startup.cs (responsible for DI Container and Middleware component settings)
