# Introduction

Sometimes you want to create a file on the fly to use right away, and don't want to save it in the hard drive. You can achieve it like below.

```c#
// Use Unicode to encode XML data
byte[] xmlData = Encoding.Unicode.GetBytes(xmlString);
var xmlStream = new MemoryStream(xmlData);

// User UTF8 to encode normal text
byte[] txtData = Encoding.UTF8.GetBytes(txtString);
var txtStream = new MemoryStream(txtData);
```