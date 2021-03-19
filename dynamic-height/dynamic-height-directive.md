# How To Set Dynamic Height At Element With Angular Directive 📐

## The Problem

Some interactive web designs require elements with dynamic height that depends on their location in the window viewport. More specifically, their height starts at the top offset position of the element inside the window and ends at the end of the window. In this article we will try to implement dynamic height using Angular directives.

## Implementation

### Create New Angular Directive

First of all, we create an Angular directive by injecting the the native element from `ElementRef`.

```ts
@Directive({
  selector: "[fluidHeight]",
})
export class FluidHeightDirective implements AfterViewInit {
  @Input("fluidHeight") topOffset: number;
  // ...

  private domElement: HTMLElement;

  constructor(private elementRef: ElementRef) {
    this.domElement = this.elementRef.nativeElement as HTMLElement;
    // ...
  }
}
```

### Calculate Top Offset Of The Element

![element-box-diagram](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect/element-box-diagram.png)

The next step is to calculate the top offset of the HTML element depending on the window. We use the `.getBoundingClientRect()` method that returns a DOMRect object which is the smallest rectangle which contains the entire element, including its padding and border-width.

```ts
private calcTopOffset(): number {
    try {
      const rect = this.domElement.getBoundingClientRect();
      const scrollTop =
        window.pageYOffset || document.documentElement.scrollTop;

      return rect.top + scrollTop;
    } catch (e) {
      return 0;
    }
  }
```

### Set Dynamic Height To Element

This method sets the calculated height to the host HTML element, using the Angular Renderer (`Renderer2`). If the user knows the to offset we don't need to recalculate it.

```ts
private setHeight() {
  const windowHeight = window?.innerHeight;
  const topOffset = this.topOffset || this.calcTopOffset();
  let height = windowHeight - topOffset;

  // set min height instead of the calculated
  if (this.minHeight && height < this.minHeight) {
    height = this.minHeight;
  }

  this.renderer.setStyle(this.domElement, 'height', `${height}px`);
}
```

### Observe Window Resize

We need to observe the window resize event in order to calculate the height on any change. We use the `fromEvent` method from `rxjs` to register at window's resize events and it converts them to observable. The problem here is that the events are too many when we resize the window and we have to decrease them. Thus, we use `throttleTime` and `debounceTime` from `rxjs/operators` to minimize the re-calculation of the dynamic height.

> Tip: Unsubscribe on destroy to avoid memory leaks.

```ts
// register on window resize event
fromEvent(window, "resize")
  .pipe(throttleTime(500), debounceTime(500))
  .subscribe(() => this.setHeight());
```

## Final Result 😉

Great, we have done it! We have created an Angular directive that sets dynamic height to its host HTML element in a very short time.

```ts
import {
  AfterViewInit,
  Directive,
  ElementRef,
  Input,
  Renderer2,
} from "@angular/core";
import { fromEvent } from "rxjs";
import { debounceTime, throttleTime } from "rxjs/operators";

@Directive({
  selector: "[fluidHeight]",
})
export class FluidHeightDirective implements AfterViewInit {
  @Input() minHeight: number;
  @Input("fluidHeight") topOffset: number;

  private domElement: HTMLElement;

  constructor(private renderer: Renderer2, private elementRef: ElementRef) {
    this.domElement = this.elementRef.nativeElement as HTMLElement;

    // register on window resize event
    fromEvent(window, "resize")
      .pipe(throttleTime(500), debounceTime(500))
      .subscribe(() => this.setHeight());
  }

  ngAfterViewInit() {
    this.setHeight();
  }

  private setHeight() {
    const windowHeight = window?.innerHeight;
    const topOffset = this.topOffset || this.calcTopOffset();
    let height = windowHeight - topOffset;

    // set min height instead of the calculated
    if (this.minHeight && height < this.minHeight) {
      height = this.minHeight;
    }

    this.renderer.setStyle(this.domElement, "height", `${height}px`);
  }

  private calcTopOffset(): number {
    try {
      const rect = this.domElement.getBoundingClientRect();
      const scrollTop =
        window.pageYOffset || document.documentElement.scrollTop;

      return rect.top + scrollTop;
    } catch (e) {
      return 0;
    }
  }
}
```

We made it to the end!👏👏👏 Hope you found this article helpful!
