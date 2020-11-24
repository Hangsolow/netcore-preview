<a href="https://github.com/episerver/netcore-preview"><img src="http://ux.episerver.com/images/logo.png" title="netcore-preview" alt="netcore-preview"></a>

## Netcore Preview

This preview repository is early access to the latest Episerver packages targeting Netcore 5. Here we will collect feedback through github issues regarding specific issues when upgrading your addons or projects.

---

## Nuget Package Location

This preview repository has a nuget.config with the location to the packages.  If you need to add your own nuget.config or update package sources, please use the following location.
  1.  https://pkgs.dev.azure.com/EpiserverEngineering/netCore/_packaging/beta-program/nuget/v3/index.json

---

## Database Installation

This sample alloy site is configured to use localdb by default.  LocalDB is only supported on windows machines.  If you want to install the database to a server, please use the sql script found in the prospective locations.  Nuget uses a global cache when not using packages.config.  Note you will need to install commerce.core to have the package download to the global cache.
  1.  C:\Users\{username}\.nuget\packages\episerver.cms.core\12.0.0-inte-014599\tools
  2.  C:\Users\{username}\.nuget\packages\episerver.commerce.core\12.0.0-inte-014599\tools

For upgrades automatic schema update should be configured by default.  

---

## Configuration

Most of the configuration has been moved to options classes.  The options classes can be configured through code or the appsettings.json configuration file.  For option classes to be automatically configured from appsettings.jsobn, please use the EPiServer.ServiceLocation.OptionsAttribute.  There is a configsection which maps to the leaf node in the JSON.

To utilize legacy configuration sections you can install the EPiServer.Cms.AspNetCore.Migration package.  This is available to ease migration, howeever we encourage to update the use options or appsettings.json if possible.

---

## Startup extensibility

There are some added extensibility points when interacting with the Startup class.
  1.  services.AddCms(); - This configures than CMS and needs to be called to function properly.
  2.  IEndpointRoutingExtension - Access to the IEndpointRouteBuilder to register routes. Convience method services.AddEndpointRoutingExtension<T>()
  3.  IStartupFilter - Access to IApplicationBuilder if you need to register middleware for instance.  Convience method services.AddStartupFilter<T>()
  4.  IBlockingFirstRequestInitializer - Use this if you need to do something before the first request
  5.  IRedirectingFirstRequestInitializer - Use this if you need to redirect to a page until some type of initialization takes place.

---

## Compiled Views for Shell Modules

For addon developers, we have added a default location expander that will look for compiled views in a certain location or based on configuration value.
  1.  /{AssemblyName}/Views/
  2.  The folder defined in the module.config viewFolder attribute on module element.

---

