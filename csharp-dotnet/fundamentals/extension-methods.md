# How to define and call the extension method

- Define a static class to contain the extension method.
- The class must be visible to client code. For more information about accessibility rules, see Access Modifiers.
- Implement the extension method as a static method with at least the same visibility as the containing class.
- The first parameter of the method specifies the type that the method operates on; it must be preceded with the this modifier.
- In the calling code, add a using directive to specify the namespace that contains the extension method class.
- Call the methods as if they were instance methods on the type.
- Note that the first parameter is not specified by calling code because it represents the type on which the operator is being applied, and the compiler already knows the type of your object. You only have to provide arguments for parameters 2 through n.

# Example

```c#
using System.Linq;
using System.Text;
using System;

namespace CustomExtensions
{
  // Extension methods must be defined in a static class.
  public static class StringExtension
  {
    // This is the extension method.
    // The first parameter takes the "this" modifier
    // and specifies the type for which the method is defined.
    public static int WordCount(this string str)
    {
      return str.Split(new char[] {' ', '.','?'}, StringSplitOptions.RemoveEmptyEntries).Length;
    }
  }
}

namespace Extension_Methods_Simple
{
  // Import the extension method namespace.
  using CustomExtensions;
  class Program
  {
    static void Main(string[] args)
    {
      string s = "The quick brown fox jumped over the lazy dog.";
      // Call the method as if it were an
      // instance method on the type. Note that the first
      // parameter is not specified by the calling code.
      int i = s.WordCount();
      System.Console.WriteLine("Word count of s is {0}", i);
    }
  }
}
```

---

*Source: https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/how-to-implement-and-call-a-custom-extension-method*