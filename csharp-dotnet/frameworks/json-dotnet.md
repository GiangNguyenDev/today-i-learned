# Serialize an object to JSON

```c#
public class Account
{
  public string Email { get; set; }
  public bool Active { get; set; }
  public DateTime CreatedDate { get; set; }
  public IList<string> Roles { get; set; }
}

Account account = new Account
{
  Email = "james@example.com",
  Active = true,
  CreatedDate = new DateTime(2013, 1, 20, 0, 0, 0, DateTimeKind.Utc),
  Roles = new List<string>
  {
    "User",
    "Admin"
  }
};

string json = JsonConvert.SerializeObject(account, Formatting.Indented);
```

# Deserialize JSON to an object

```c#
public class Account
{
  public string Email { get; set; }
  public bool Active { get; set; }
  public DateTime CreatedDate { get; set; }
  public IList<string> Roles { get; set; }
}

string json = @"{
  'Email': 'james@example.com',
  'Active': true,
  'CreatedDate': '2013-01-20T00:00:00Z',
  'Roles': [
    'User',
    'Admin'
  ]
}";

Account account = JsonConvert.DeserializeObject<Account>(json);

Console.WriteLine(account.Email);
```

# How to ignore a property if null during deserialization

Use attribute property `JsonProperty` like below.

```c#
public class Vessel
{
  public string Name { get; set; }
  public string Class { get; set; }

  [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
  public DateTime? LaunchDate { get; set; }
}
```
