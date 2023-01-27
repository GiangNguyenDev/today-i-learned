# How to extract value from response and set to a variable

In the tab `Tests` you can write customized JavaScript to extract value from a response and write it in a variable.

```javascript
// Convert response to JSON object
var jsonData = JSON.parse(responseBody);

// Set variable of current environment
postman.setEnvironmentVariable("local_variable", jsonData.access_token);

// Set global variable
pm.environment.set("global_variable", jsonData.access_token);
```