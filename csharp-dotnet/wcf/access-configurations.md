# ConfigurationManager

This class provides access to configuration files for client applications. It can also be used in web applications to access some configuration like app settings.

```c#
string username = ConfigurationManager.AppSettings["Username"];
string password = ConfigurationManager.AppSettings["Password"];
```

# WebConfigurationManager

This class provides access to configuration files as they apply to Web applications. Using `WebConfigurationManager` is the preferred way to work with configuration files related to Web applications. For client applications, use the `ConfigurationManager` class.

```c#
// Get the connectionStrings section.
ConnectionStringsSection connectionStringsSection = WebConfigurationManager.GetSection("connectionStrings") as ConnectionStringsSection;

// Get the connectionStrings key,value pairs collection.
ConnectionStringSettingsCollection connectionStrings = connectionStringsSection.ConnectionStrings;

// Get the collection enumerator.
IEnumerator connectionStringsEnum = connectionStrings.GetEnumerator();
```

---

Source:
- https://learn.microsoft.com/en-us/dotnet/api/system.configuration.configurationmanager
- https://learn.microsoft.com/en-us/dotnet/api/system.web.configuration.webconfigurationmanager