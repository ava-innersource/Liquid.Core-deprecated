Liquid Application Framework - Core
===================================

This repository is part of the [Liquid Application Framework](https://github.com/Avanade/Liquid-Application-Framework), a modern Dotnet Core Application Framework for building cloud native microservices.

Liquid.Core
-----------

[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=Avanade_Liquid.Core&metric=alert_status)](https://sonarcloud.io/dashboard?id=Avanade_Liquid.Core)

This package contains the core of Liquid Application Framework, it's a common package used in all other packages.

### **Main responsabilities:**
|| |
|---|---|
| **Configuration**:        |Provides configuration to other modules|
| **Context**:              |Provides a generic execution context that is shared between execution layers in order to provide shared data|
| **Telemetry**:            |Provides logging and tracing capabilities.|
| **Exception**:            |Provides base exception class, with custom codes for consistency|
| **Dependency Injection**: |Provides an extension for .Net Dependency Injection Mechanisms and Automapper to improve developer experience|
| **Utils**:                |Provide several base utilities for .Net Core classes to improver developer produtivity.|


Getting Started
===


Configuration
---
To configure any liquid component, you just need to call the configuration injection method as exemplified below, and it will get the settings declared in any dotnet configuration provider (environment variables, json file, in-memory, secret manager, command line, etc). 

```C#
using Liquid.Core.Extensions.DependencyInjection;
```
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddConfigurations();
}
```

#### Custom configuration 
You can also implement custom configurations for your application and inject them using LiquidConfiguration as below:

```C#
using Liquid.Core.Attributes;
```
```C#
//The class must be annotated with section name that you will declare in your configuration provider
[LiquidSectionName("MyCustomSettings")]
public class MyCustomSettings
{
    public bool Prop1 { get; set; }
    public string Prop2 { get; set; }
}
```
```C#
public class MySample
{
    public MySample(ILightConfiguration<MyCustomSettings> configuration)
    {
        //some code
    }
}

```

Startup.cs
```C#
using Liquid.Core.Extensions.DependencyInjection;
```
```C#
public void ConfigureServices(IServiceCollection services)
{
    //Inject all implemented configurations in project, including default settings implemented on Liquid components.
    services.AddConfigurations();
}
```
appsettings.json (file provider sample)
```Json
{
  //sample of liquid default culture settings.
  "liquid": {
    "culture": {
      "defaultCulture": "pt-BR"
    }
  },
  //custom settings implemented above.
  "myCustomSettings": {
    "prop1": true,
    "prop2": "prop"
  }
}
```

Context
---
Liquid.Core provides a scoped context service that can be used to propagate specific keys and their values within the context of a request.
This service has a defined settings object that should be used to configure keys to obtain from request/message headers and included in context service. 
Its usage examples can be found in the [Liquid.WebApi](https://github.com/Avanade/Liquid.WebApi#readme) and [Liquid.Messaging](https://github.com/Avanade/Liquid.Messaging#readme) port components documentation.

Telemetry
--

Liquid's telemetry was implemented based on Castle.DynamicProxy.AsyncInterceptorBase, and has pre and post execution methods, as well as exception handling.
You can use LiquidTelemetry interceptor by registering your service as shown below.

>Watch out: All Liquid components have extension methods for injecting their services that already include the registration of LiquidTelemetryInterceptor by default

```C#
using Liquid.Core.Extensions.DependencyInjection;
```
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddLiquidTelemetryInterceptor<ICustomService, CustomService>();
}
```