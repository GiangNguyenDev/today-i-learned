# Parse String to Boolean

Following string can be parsed to boolean value
- Words with 4 characters <kbd>T</kbd>, <kbd>R</kbd>, <kbd>U</kbd>, <kbd>E</kbd> (case-insensitive), e.g., true, True, TRUE
- Words with 5 characters <kbd>F</kbd>, <kbd>A</kbd>, <kbd>L</kbd>, <kbd>S</kbd>, <kbd>E</kbd> (case-insensitive), e.g., false, False, FALSE

```c#
// These expressions are valid
bool.Parse("true");
bool.Parse("True");
bool.Parse("FALSE");

// Following expression will throw exception
bool.Parse("yes");
bool.Parse("wrong");
```