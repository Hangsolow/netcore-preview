<a href="https://github.com/episerver/netcore-preview"><img src="http://ux.episerver.com/images/logo.png" title="netcore-preview" alt="netcore-preview"></a>

## Netcore Preview

This preview repository is early access to the latest Episerver packages targeting .NET 5. Here we will collect feedback through github issues regarding specific issues when upgrading your addons or projects.

---

## The Solution

Please use your user directory on windows or add everyone to the folder where you created the repo.  Alloy and Quicksilver are both using localdb so you need to have the correct permissions.

`Quicksilver has a default username and password of admin@example.com / Episerver123!`

---

## Nuget Package Location

This preview repository has a nuget.config with the location to the packages.  If you need to add your own nuget.config or update package sources, please use the following location.
  1.  https://pkgs.dev.azure.com/EpiserverEngineering/netCore/_packaging/beta-program/nuget/v3/index.json

---

## Template Installation

```
dotnet new -i EPiServer.Net.Templates::1.0.0-inte-017362 --nuget-source https://pkgs.dev.azure.com/EpiserverEngineering/netCore/_packaging/beta-program/nuget/v3/index.json --force
```
---

## CLI Installation

```
dotnet tool install EPiServer.Net.Cli --global --add-source https://pkgs.dev.azure.com/EpiserverEngineering/netCore/_packaging/beta-program/nuget/v3/index.json --version 1.0.0-inte-017362
```
---

## Create empty cms site

```
dotnet new epicmsempty --name ProjectName
cd projectname
dotnet-episerver create-cms-database ProjectName.csproj -S . -E 

```
### Notes

-S stands for the server you want to connect to.  Do not use . if you dont have a local sql server installed.  You could also use MahcineName\SQLEXRESSS or (LocalDb)\MSSqlLocalDB instead of ".".
Right now there is no command to create the admin user, we plan to add in the future.  You can check Quicksilver\EPiServer.Reference.Commerce.Site\Infrastructure\UsersInstaller.cs if you want to automate in the short term.

---

## Create empty commerce site

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

## Create alloy site

```
dotnet new epicmsalloy --name ProjectName
cd projectname
dotnet-episerver create-cms-database ProjectName.csproj -S . -E
```

### Notes

-S stands for the server you want to connect to.  Do not use . if you dont have a local sql server installed.  You could also use MahcineName\SQLEXRESSS or (LocalDb)\MSSqlLocalDB instead of ".".
Alloy has a middleware to create the administration user.

---

## Configuration

Most of the configuration has been moved to options classes.  The options classes can be configured through code or the appsettings.json configuration file.  For option classes to be automatically configured from `appsettings.json`, please use the `EPiServer.ServiceLocation.OptionsAttribute`.  There is a configuration section which maps to the leaf node in the JSON.

To utilize legacy configuration sections you can install the `EPiServer.Cms.AspNetCore.Migration` package. This is available to ease migration, however we encourage to update the use options or `appsettings.json` if possible.

---

## Startup extensibility
### Program.cs
EPiServer will by default use the built-in Dependency Injection framework (DI) in .NET 5. To connect the DI framework with EPiServer you need to call extension method `IHostBuilder.ConfigureCmsDefault()` in Program.cs. <br/>
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

## Compiled Views for Shell Modules

For addon developers, we have added a default location expander that will look for compiled views in a certain location or based on configuration value.
  1.  /{ShellModuleName}/Views/
  2.  The folder defined in the module.config viewFolder attribute on module element.

---

## Preview links to documentation

### CMS

* [CMS 12 Breaking changes](https://world.episerver.com/externalContentView/2f46e48e-19d5-4735-a0fa-f9b193a78eb7 "CMS 12 Breaking changes")
* [Configuration](https://world.episerver.com/externalContentView/91e3ad6f-ec40-44c4-a667-7d48e2f1c2f0 "Configuration")
* [Dependency injection](https://world.episerver.com/externalContentView/a798288a-90af-44ff-b495-68e827403903 "Dependency injection")
* [File Providers](https://world.episerver.com/externalContentView/c10509ac-40b4-4c0e-92d5-016b4e37081b "File Providers")
* [Logging](https://world.episerver.com/externalContentView/ac48d781-f9f6-4f16-8677-8281bacdaffa "Logging")
* [Routing](https://world.episerver.com/externalContentView/968f52c2-8a0f-4111-a34a-d51450d62b1e "Routing")
* [Security](https://world.episerver.com/externalContentView/bf63f0a1-da67-4a0c-8b71-d753099956d0 "Security")
* [Upgrade assistant - overview](https://world.episerver.com/externalContentView/01ad2880-18c2-4898-90d0-9fa99a6fdbe1 "Upgrade assistant - overview")
* [Upgrade assistant - installation and running](https://world.episerver.com/externalContentView/f5838f07-fc03-464a-9bff-724272e6bf1e "Upgrade assistant - installation and running")

### Commerce

* [Breaking changes Commerce 14](https://world.episerver.com/externalContentView/e324ac1b-aa08-4335-87e0-1f2abc6ab054 "Upgrading/breaking changes")
* [Commerce-specific configurations](https://world.episerver.com/externalContentView/615a8a17-150a-485f-88e0-4938f07db965 "Commerce-specific configurations")
* [Feature-specific configurations](https://world.episerver.com/externalContentView/60171983-89a5-4761-ad1e-d28124d5517b "Feature-specific configurations")
* [Caching](https://world.episerver.com/externalContentView/accb3c7b-bd04-4bfd-98b1-2737eeca7fcb "Caching")
* [Importing catalog data](https://world.episerver.com/externalContentView/a57e2a15-e429-450e-a3cb-387d1a70589c "Importing catalog data")
* [Catalog DTO and object models](https://world.episerver.com/externalContentView/2bd5fe5b-40a2-41de-836d-6c772e865479 "Catalog DTO and object models")
* [Catalog product search](https://world.episerver.com/externalContentView/8cf54f54-aa55-48b3-9975-fa599da6cd24 "Catalog product search")
* [Event-driven catalog indexing](https://world.episerver.com/externalContentView/02482dbc-542b-45c8-bfaa-44f46c0912f4 "Event-driven catalog indexing")
* [Low-level APIs](https://world.episerver.com/externalContentView/089492e2-a1e8-486d-a50b-1d75e3a5155d "Low-level APIs")
* [Globalization](https://world.episerver.com/externalContentView/fdaed320-44c8-486e-a4f8-9cc95e8484ed "Globalization")
* [Logging](https://world.episerver.com/externalContentView/9c75a5ac-bfa4-4903-8f6b-f6147264f9da "Logging")
* [Markets](https://world.episerver.com/externalContentView/52f78cf0-18b6-4a0f-af33-1274fc96b100 "Markets")
* [Collecting data for reports](https://world.episerver.com/externalContentView/26b681ab-37ba-4bdb-b5e4-0d13cd68bb6d "Collecting data for reports")
* [Collecting subscription data](https://world.episerver.com/externalContentView/f7b9e057-9039-47a7-a22e-e618bf884339 "Collecting subscription data")
* [Customers](https://world.episerver.com/externalContentView/5eef00df-3a49-452d-b6f5-58e30d2221a5 "Customers")
* [Customer groups](https://world.episerver.com/externalContentView/e5b6659a-3e06-4534-9813-2de6b4abe40e "Customer groups")
* [Order management](https://world.episerver.com/externalContentView/4265cd26-f6eb-4377-96b1-703ee6b88c65 "Order management")
* [Extending order classes](https://world.episerver.com/externalContentView/35a56df3-0a19-47c6-87ad-a654b2bbc614 "Extending order classes")
* [Serializable carts](https://world.episerver.com/externalContentView/7872356b-ff2a-46c5-926f-47720fab9d67 "Serializable carts")
* [Payments](https://world.episerver.com/externalContentView/e5eaac36-ced0-457b-b57f-015040405275 "Payments")
* [Payment gateways](https://world.episerver.com/externalContentView/9c545dfe-182f-4786-9d36-a713b86c7986 "Payment gateways")
* [Payment plugins](https://world.episerver.com/externalContentView/003b0605-1b3b-48f1-86ef-8e471ecff4a5 "Payment plugins")
* [Pricing](https://world.episerver.com/externalContentView/dbf2cf30-1aea-4805-bdae-19b985e71d56 "Pricing")
* [PriceType examples](https://world.episerver.com/externalContentView/d70ba314-3752-46f8-b0a5-1af780d8a993 "PriceType examples")
* [Shipping gateways and providers](https://world.episerver.com/externalContentView/d65374e8-c4b4-4c57-8e7d-a41580f7515b "Shipping gateways and providers")
* [Shipping methods](https://world.episerver.com/externalContentView/3ba865ed-8d9d-483b-b6b0-365ba12b87e4 "Shipping methods")
* [Scheduled jobs](https://world.episerver.com/externalContentView/c6931e2b-896f-4018-a526-68c94724a473 "Scheduled jobs")
* [Search](https://world.episerver.com/externalContentView/b6615bbc-d3a3-4fcf-801e-23a109cf66d8 "Search")
* [Customizing search](https://world.episerver.com/externalContentView/c77b3cfa-45fe-42fa-85bb-f828c0364d63 "Customizing search")
* [Disabling product update events](https://world.episerver.com/externalContentView/f71c4eec-22d7-40ec-b0a8-fe4c58e326b0 "Disabling product update events")
* [Creating a search provider](https://world.episerver.com/externalContentView/1b46cd43-2677-4796-827f-7e051ec2fa05 "Creating a search provider")
* [Customizing search](https://world.episerver.com/externalContentView/c77b3cfa-45fe-42fa-85bb-f828c0364d63 "Customizing search")
* [Indexing variants in a product document](https://world.episerver.com/externalContentView/4e0c3e6e-45c5-41fd-a2b4-e76f6af5cdc2 "Indexing variants in a product document")
* [MetaType class](https://world.episerver.com/externalContentView/9bf86d3e-3d5f-49fe-b17c-9b8e8cbb2afd "MetaType class")
* [Working with entity objects](https://world.episerver.com/externalContentView/5630b604-488e-4f57-ac9e-2876b93f1b26 "Working with entity objects")
* [Working with SQL records](https://world.episerver.com/externalContentView/1fcf2a7e-04f0-40d5-b394-dcf8d040910c "Working with SQL records")
* [Security](https://world.episerver.com/externalContentView/250587d7-1aa0-4ce1-b4a1-976fdb004024 "Security")
* [Authorization and authentication](https://world.episerver.com/externalContentView/74b63d8c-7b0e-45f6-b04a-0b0e4ea42145 "Authorization and authentication")
* [Support for OpenID Connect](https://world.episerver.com/externalContentView/7ea7be44-e5aa-4673-b9c2-ed92a116fa76 "Support for OpenID Connect")
* [Installing and configuring the native integration package](https://world.episerver.com/externalContentView/0836e74c-4175-4657-9e8b-bcc015b5a8d8 "Installing and configuring the native integration package")
* [Workflows and activities](https://world.episerver.com/externalContentView/c72e976b-ed92-454d-8830-cd87075209e4 "Workflows and activities")
* [Option configuration classes](https://world.episerver.com/externalContentView/6a58d1b5-7f9a-41c2-a4c8-0b63bf33a18d "Option configuration classes")
