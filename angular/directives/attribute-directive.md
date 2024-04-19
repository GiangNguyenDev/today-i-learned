# Handling user events

## Handling key pressed event

```typescript
@HostListener("document:keydown", ["$event"]) onKeydownHandler(event: KeyboardEvent) {
  if (event.key === "Enter") {
    // Handling action
  }
}
```

[KeyboardEvent: key property](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key)