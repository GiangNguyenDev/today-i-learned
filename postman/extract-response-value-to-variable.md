# How to extract value from response and set to a variable

In the tab `Tests` you can write customized JavaScript to extract value from a response and write it in a variable.

## Export value from JSON

```javascript
// Convert response to JSON object
var jsonData = JSON.parse(responseBody);

// Set variable of current environment
postman.setEnvironmentVariable("local_variable", jsonData.access_token);

// Set global variable
pm.globals.set("global_variable", jsonData.access_token);

// Set variable of current collection
pm.collectionVariables.set("local_variable", jsonData.access_token);
```

## Export value from XML

```javascript
var jsonData = xml2Json(responseBody);

const elementValue = _.get(
  jsonData,
  '["s:Envelope"]["s:Body"]["ns:myOperation"]["myElement"]'
);

if (elementValue) {
  pm.environment.set("my_variable", elementValue);
}
```
