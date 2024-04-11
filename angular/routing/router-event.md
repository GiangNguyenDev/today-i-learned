# Subscribe to router event

```typescript
constructor(private router: Router) {}

ngOnInit() {
  this.router.events
    .pipe(filter((event) => event instanceof NavigationStart))
    .forEach((event: NavigationStart) => {
      const hasMyPath = !event.url.includes('my-path');
    });
}
```
