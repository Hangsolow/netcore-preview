## This repository is being archived. The official release can now be found at Optimizely World, see the summary page on .NET 5 here https://world.optimizely.com/net5

---

This repository is a fork of https://github.com/episerver/netcore-preview intended to be a sample for dotnet 5 for use in blogposts and the like

## The solution

Use your user directory on Windows, or add Everyone to the folder where you created the repository. The Alloy and Quicksilver sample sites are both using localdb, so you need to have the correct permissions.

`NOTE: Quicksilver has a default username and password of admin@example.com / Episerver123!`

---
## Create empty CMS site

```
dotnet new epicmsempty --name ProjectName
cd projectname
dotnet-episerver create-cms-database ProjectName.csproj -S . -E 

```
### Notes

-S stands for the server you want to connect to.  Do not use . if you dont have a local sql server installed.  You could also use MahcineName\SQLEXRESSS or (LocalDb)\MSSqlLocalDB instead of ".".
Right now there is no command to create the admin user, we plan to add in the future.  You can check Quicksilver\EPiServer.Reference.Commerce.Site\Infrastructure\UsersInstaller.cs if you want to automate in the short term.

---

## Create empty Commerce site

```
dotnet new epicommerceempty --name ProjectName
cd projectname
dotnet-episerver create-cms-database ProjectName.csproj -S . -E
dotnet-episerver create-commerce-database ProjectName.csproj -S . -E --reuse-cms-user
```
### Notes

-S stands for the server you want to connect to.  Do not use . if you dont have a local sql server installed.  You could also use MahcineName\SQLEXRESSS or (LocalDb)\MSSqlLocalDB instead of ".".
Right now there is no command to create the admin user, we plan to add in the future.  You can check Quicksilver\EPiServer.Reference.Commerce.Site\Infrastructure\UsersInstaller.cs if you want to automate in the short term.

---


## Configuration

Most of the configuration has been moved to options classes. The options classes can be configured through code or the appsettings.json configuration file. For option classes to be automatically configured from `appsettings.json`, use the `EPiServer.ServiceLocation.OptionsAttribute`. There is a configuration section which maps to the leaf node in the JSON.

To utilize legacy configuration sections you can install the `EPiServer.Cms.AspNetCore.Migration` package. This is available to ease migration, however we encourage to update the use options or `appsettings.json` if possible.

---

## Startup extensibility
### Program.cs
CMS 12 will by default use the built-in Dependency Injection framework (DI) in .NET 5. To connect the DI framework with EPiServer you need to call extension method `IHostBuilder.ConfigureCmsDefault()` in Program.cs. <br/>
To configure the application (including EPiServer) to use another DI framework you should call the extension method `IHostBuilder.UseServiceProviderFactory`. The example below shows how to configure the application to use Autofac:

```
host.UseServiceProviderFactory(context => new  ServiceLocatorProviderFactoryFacade<ContainerBuilder>(context,
    new AutofacServiceProviderFactory()));
```

### Startup.cs
There are some added extensibility points when interacting with the Startup class.
  1.  `services.AddCms();` - This configures than CMS and needs to be called to function properly.
  2.  `endpoints.MapContent();` - This registers EPiServer content routing with the endpoint routing feature.
  3.  `IEndpointRoutingExtension` - Access to the `IEndpointRouteBuilder` to register routes. Convience method `services.AddEndpointRoutingExtension<T>()`
  4.  `IStartupFilter` - Access to IApplicationBuilder if you need to register middleware for instance.  Convience method `services.AddStartupFilter<T>()`
  5.  `IBlockingFirstRequestInitializer` - Use this if you need to do something before the first request
  6.  `IRedirectingFirstRequestInitializer` - Use this if you need to redirect to a page until some type of initialization takes place.

---


## Search & Navigation (formerly Find) preview

To use the updated Find packages you will need to configure the .NET 5 version in the Startup file.

```
services.Configure<FindUIOptions>(x => x.ClientSideResourceBaseUrl = "https://stage.dl.episerver.net/$version$/");
```

---

## documentation

For  the documentation for CMS 12, Commerce 14, and Search & Navigation 14, see the [.NET 5.0 documentation](https://world.optimizely.com/documentation/upgrading/optimizely-cms/cms-12/) on Optimizely World.
