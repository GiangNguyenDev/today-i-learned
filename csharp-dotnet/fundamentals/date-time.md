# Convert DateTime to String

```c#
DateTime now = DateTime.Now;
Console.WriteLine($"{now:yyyy-MM-dd}"); // 2023-05-26
Console.WriteLine($"{now:s}");          // 2023-05-26T15:54:31
Console.WriteLine($"{now:HH:mm:ss}");   // 15:54:31
```

# Convert UTC to local Time

```c#
var utcDateTime = DateTime.UtcNow;
var localDateTime = utcDateTime.ToLocalTime();
Console.WriteLine($"Universal time: {utcDateTime}"); // 26.05.2023 13:57:21
Console.WriteLine($"Local time: {localDateTime}"); // 26.05.2023 15:57:21
```