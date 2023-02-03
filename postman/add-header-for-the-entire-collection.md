# How to add headers for the entire collection/folder

In `Pre-request Script` tab of a collection or folder, add the following JavaScript

```javascript
const headerValue = pm.environment.get("header_var");
pm.request.headers.add({key: 'header_name', value: headerValue });
```