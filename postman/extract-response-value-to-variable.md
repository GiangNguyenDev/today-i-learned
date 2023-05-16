# How to extract value from response and set to a variable

In the tab `Tests` you can write customized JavaScript to extract value from a response and write it in a variable.

## Export value from JSON

```javascript
// Convert response to JSON object
var jsonData = JSON.parse(responseBody);

// Define a global variable
pm.globals.set("global_variable", jsonData.access_token);

// Define a collection variable
pm.collectionVariables.set("collection_variable", jsonData.access_token);

// Define an environment variable in the currently selected environment
pm.environment.set("environment_variable", jsonData.access_token);

// Define a local variable
pm.variables.set("local_variable", jsonData.access_token);
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

---

_Source: https://learning.postman.com/docs/sending-requests/variables/#defining-variables-in-scripts_
