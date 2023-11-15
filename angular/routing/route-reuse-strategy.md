# How to define custom Reuse Strategy for Routing

## Create custom strategy class

```typescript
import {
  ActivatedRouteSnapshot,
  BaseRouteReuseStrategy,
  DetachedRouteHandle,
} from "@angular/router";

export class AppRouteReuseStrategy implements BaseRouteReuseStrategy {
  retrieve(route: ActivatedRouteSnapshot): DetachedRouteHandle {
    return null;
  }

  shouldAttach(route: ActivatedRouteSnapshot): boolean {
    return false;
  }

  shouldDetach(route: ActivatedRouteSnapshot): boolean {
    return false;
  }

  shouldReuseRoute(
    future: ActivatedRouteSnapshot,
    current: ActivatedRouteSnapshot
  ): boolean {
    return (
      future.data.reuseComponent ?? future.routeConfig === current.routeConfig
    );
  }

  store(
    route: ActivatedRouteSnapshot,
    detachedTree: DetachedRouteHandle
  ): void {}
}
```

## Register custom strategy

```typescript
@NgModule({
  // ...
  providers: [
    {provide: RouteReuseStrategy, useClass: AppRouteReuseStrategy}
  ],
  // ...
})
```

## Use strategy in route definition

```typescript
{
  path: `app`,
  component: MyComponent,
  data: { reuseComponent: false },
}
```

Source:

- https://www.auroria.io/angular-route-reuse-strategy/
- https://angular.dev/api/router/RouteReuseStrategy
