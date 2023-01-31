# Fix error during serializing object without parameterless constructor

If you have a constructor with parameter in your class, you can get an error during serializing object of this class. There are some solutions for this scenario.

## Option A: Use `DataContract` and `DataMember` attributes

```c#
[DataContract]
public class Document
{
	public Document(string title, string type)
	{
		Title = title;
		Type = type;
	}

	[DataMember]
	public string Title { get; set; }

	[DataMember]
	public string Type { get; set; }
}
```

## Option B: Create a parameterless constructor, which doesn't need to be public.

```c#
public class Document
{
	private Document() {}

	public Document(string title, string type)
	{
		Title = title;
		Type = type;
	}

	public string Title { get; set; }

	public string Type { get; set; }
}
```