# Introduction

If you like to get a custom binding defined in `web.config` file or other configuration, you can do it in C# as below.

```cs
Binding binding = null;
Configuration config = WebConfigurationManager.OpenWebConfiguration(HttpContext.Current.Request.ApplicationPath);

// "system.seviceModel" is a section group, unlike "appSettings" is a section
var serviceModel = config.GetSectionGroup("system.serviceModel") as ServiceModelSectionGroup;

foreach (CustomBindingElement customBinding in serviceModel.Bindings.CustomBinding.Bindings)
{
    if (customBinding.Name == "MyCustomBinding")
    {
        binding = new CustomBinding();
        customBinding.ApplyConfiguration(binding);
        binding.Name = "MyCustomBinding";
        break;
    }
}
```
