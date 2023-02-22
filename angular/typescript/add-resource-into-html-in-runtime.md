# Introduction

Sometimes you want to add stylesheet and JavaScript as resource into your application HTML in runtime.

## Add external resource

```typescript
loadAuraResources() {
  this.loadLink(`https://external-service.com/external-style.css`);
  this.loadLink(`override-style.css`);
  this.loadScript(`https://external-service.com/external-script.js`);
}

private loadLink(url: string) {
  let hasElement = this.hasElement("link", url);

  if (!hasElement) {
    const link = document.createElement("link");
    link.rel = "stylesheet";
    link.href = url;
    const body = <HTMLDivElement>document.body;
    body.append(link);
  }
}

private loadScript(url: string) {
  let hasElement = this.hasElement("script", url);

  if (!hasElement) {
    const script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    script.async = false;
    const body = <HTMLDivElement>document.body;
    body.append(script);
  }
}

private hasElement(elementType: string, url: string) {
  let result = false;
  const urlAttribute = elementType === "link" ? "href" : "src";
  let scripts = document.getElementsByTagName(elementType);

  for (var i = 0; i < scripts.length; ++i) {
    if (scripts[i].getAttribute(urlAttribute) === url) {
      result = true;
    }
  }

  return result;
}
```

## Add internal resource

In `angular.json` you can configure the `styles` section, so that your `override-style.scss` will not be merged into the `styles.css` file, but compiled into `override-style.css` and copied into the `dist` folder during build process.

```json
"styles": [
  "node_modules/font-awesome/css/font-awesome.min.css",
  "src/styles/bootstrap-grid.css",
  "src/styles.scss",
  {
    "input": "src/styles/override-style.scss",
    "bundleName": "override-style",
    "inject": false
  }
]
```

Then use the same method like [Add external resource section](#add-external-resource).
