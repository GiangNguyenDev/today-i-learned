# Use pipe in HTML template

## Pipe in string iterpolation

```html
<h3>Birthdate: {{ person.birthdate | shortDate }}</h3>
```

## Pipe in data binding

```html
<input type="text" [value]="person.birthdate | shortDate" />
```