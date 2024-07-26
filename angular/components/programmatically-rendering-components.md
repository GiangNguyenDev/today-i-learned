# How to programmatically render components

In Angular, you can dynamically add multiple components of different types using a combination of Angular's `ComponentFactoryResolver`, `ViewContainerRef`, and `ComponentRef`. Here's a step-by-step guide to achieve this:

1. **Create the Components**: First, you need to have the components you want to add dynamically.

```typescript
// example-component1.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-example-component1",
  template: `<p>Example Component 1</p>`,
})
export class ExampleComponent1 {}
```

```typescript
// example-component2.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "app-example-component2",
  template: `<p>Example Component 2</p>`,
})
export class ExampleComponent2 {}
```

2. **Create a Directive to Mark the Insertion Point**: This directive will be used to mark where the dynamic components will be inserted in the template.

```typescript
// dynamic-host.directive.ts
import { Directive, ViewContainerRef } from "@angular/core";

@Directive({
  selector: "[appDynamicHost]",
})
export class DynamicHostDirective {
  constructor(public viewContainerRef: ViewContainerRef) {}
}
```

3. **Create a Service to Load Components Dynamically**: This service will use `ComponentFactoryResolver` to create and insert components.

```typescript
// dynamic-component.service.ts
import {
  Injectable,
  ComponentFactoryResolver,
  ViewContainerRef,
} from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class DynamicComponentService {
  constructor() {}

  loadComponent(component: any, viewContainerRef: ViewContainerRef): void {
    viewContainerRef.clear();
    viewContainerRef.createComponent(component);
  }
}
```

4. **Use the Directive and Service in a Container Component**: This component will use the directive to mark the insertion point and the service to dynamically load components.

```typescript
// dynamic-container.component.ts
import { Component, ViewChild } from "@angular/core";
import { DynamicHostDirective } from "./dynamic-host.directive";
import { DynamicComponentService } from "./dynamic-component.service";
import { ExampleComponent1 } from "./example-component1.component";
import { ExampleComponent2 } from "./example-component2.component";

@Component({
  selector: "app-dynamic-container",
  template: `<ng-template appDynamicHost></ng-template>`,
})
export class DynamicContainerComponent {
  @ViewChild(DynamicHostDirective, { static: true })
  dynamicHost!: DynamicHostDirective;

  constructor(private dynamicComponentService: DynamicComponentService) {}

  loadComponents(): void {
    const viewContainerRef = this.dynamicHost.viewContainerRef;

    // Example: Load ExampleComponent1 and ExampleComponent2 dynamically
    this.dynamicComponentService.loadComponent(
      ExampleComponent1,
      viewContainerRef
    );
    this.dynamicComponentService.loadComponent(
      ExampleComponent2,
      viewContainerRef
    );
  }
}
```

5. **Trigger the Loading of Components**: You can call the `loadComponents` method in the `ngOnInit` lifecycle hook or in response to some event.

```typescript
// dynamic-container.component.ts
import { Component, ViewChild, OnInit } from "@angular/core";
import { DynamicHostDirective } from "./dynamic-host.directive";
import { DynamicComponentService } from "./dynamic-component.service";
import { ExampleComponent1 } from "./example-component1.component";
import { ExampleComponent2 } from "./example-component2.component";

@Component({
  selector: "app-dynamic-container",
  template: `<ng-template appDynamicHost></ng-template>`,
})
export class DynamicContainerComponent implements OnInit {
  @ViewChild(DynamicHostDirective, { static: true })
  dynamicHost!: DynamicHostDirective;

  constructor(private dynamicComponentService: DynamicComponentService) {}

  ngOnInit(): void {
    this.loadComponents();
  }

  loadComponents(): void {
    const viewContainerRef = this.dynamicHost.viewContainerRef;

    // Example: Load ExampleComponent1 and ExampleComponent2 dynamically
    this.dynamicComponentService.loadComponent(
      ExampleComponent1,
      viewContainerRef
    );
    this.dynamicComponentService.loadComponent(
      ExampleComponent2,
      viewContainerRef
    );
  }
}
```

6. **Update Module Declarations**: Ensure all components and directives are declared in the appropriate module.

```typescript
// app.module.ts
import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";
import { AppComponent } from "./app.component";
import { ExampleComponent1 } from "./example-component1.component";
import { ExampleComponent2 } from "./example-component2.component";
import { DynamicContainerComponent } from "./dynamic-container.component";
import { DynamicHostDirective } from "./dynamic-host.directive";

@NgModule({
  declarations: [
    AppComponent,
    ExampleComponent1,
    ExampleComponent2,
    DynamicContainerComponent,
    DynamicHostDirective,
  ],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

This example shows how to dynamically add two different components (`ExampleComponent1` and `ExampleComponent2`) to a container component using a directive to mark the insertion point. You can adapt the `loadComponents` method to add logic to load different components based on your application needs.
