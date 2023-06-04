# Serialize object without escaping Unicode characters

```c#
var result = JsonSerializer.Serialize(dataObject, typeof(DataType), new JsonSerializerOptions()
{
    DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingNull,
    Encoder = JavaScriptEncoder.UnsafeRelaxedJsonEscaping,
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
    WriteIndented = true,
});
```