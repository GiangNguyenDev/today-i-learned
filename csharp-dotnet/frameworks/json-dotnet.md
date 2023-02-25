# How to ignore a property if null during deserialization

Use attribute property `JsonProperty` like below.

```cs
public class Vessel
{
  public string Name { get; set; }
  public string Class { get; set; }

  [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
  public DateTime? LaunchDate { get; set; }
}
```
