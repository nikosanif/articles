# [4+1 ways] How to Unsubscribe from Observables in Angular like a 😎

## Introduction

In this article, we will see some of the best practices to unsubscribe from subscriptions (aka Observables) and how to keep our code clean and nicely architectured avoiding memory leaks.

In a nutshell, we will use:

- the `async` Angular build-in pipe
- RxJS operators (e.g `takeUntil`)
- custom array of subscriptions
- the `SubSink` npm package
- the `@ngneat/until-destroy` npm package

## The Problem :thinking:

If you have experience in Angular, you already know that [RxJS](https://github.com/ReactiveX/rxjs) is one of the most powerful packages. It uses the concept of Observables to handle and work with asynchronous and event-based code. More specifically, `Observable` is an entity that emits multiple data values over time and asynchronously.

> _It sounds cool, right?_ **Yes, it is cool and powerful, but you already know that!**

> _What's the problem with Observables then?_ **Memory Leak!**

Let me elaborate a bit on this, explaining a memory leak issue by an example. Below, we initialize 3 observables where they emit values every 1 second and we create a subscription for each one of them.

```ts
@Component(/* ... */)
export class ProblematicExampleComponent implements OnInit {
  constructor() {}

  ngOnInit() {
    interval(1000).subscribe((value) => {
      console.log('sub1', value);
    });
    interval(1000).subscribe((value) => {
      console.log('sub2', value);
    });
    interval(1000).subscribe((value) => {
      console.log('sub3', value);
    });
  }
}
```

Our code seems to work! BUT, what would happen if we destroy this component (e.g by navigating to an other route that doesn't contain this component)? The values will continue to be logged!

An even worse scenario is that if we go back to the previous route, the values will be printed twice, and so on. While we create more and more subscriptions without cleaning up them, it constitutes a typical example of a memory leak.

## 1. The `async` Pipe

As we know, Angular provide us a lot of built-in functionalities that we don't have to worry about by choosing third-party libraries (that's why we love :heart: Angular after all, right?). Such a useful functionality is the `async` pipe.

Looking at the [official documentation](https://angular.io/api/common/AsyncPipe), we will see the following description:

> _The `async` pipe subscribes to an **Observable** or **Promise** and returns the latest value it has emitted. When a new value is emitted, the `async` pipe marks the component to be checked for changes. When the component gets destroyed, the `async` pipe unsubscribes automatically to avoid potential memory leaks._

Example:

```ts
// async-example.component.ts

import { Component } from '@angular/core';
import { interval } from 'rxjs';
import { tap } from 'rxjs/operators';
import { Logger } from '../utils/logger';

@Component(/* ... */)
export class AsyncExampleComponent {
  private logger = new Logger(AsyncExampleComponent.name);

  obs1$ = interval(1000).pipe(
    tap((value) => {
      this.logger.log('sub1', value);
    })
  );
  obs2$ = interval(1000).pipe(
    tap((value) => {
      this.logger.log('sub2', value);
    })
  );
  obs3$ = interval(1000).pipe(
    tap((value) => {
      this.logger.log('sub3', value);
    })
  );

  constructor() {}
}
```

```html
<!-- async-example.component.html -->

<p>Observable 1 value: {{obs1$ | async}}</p>
<p>Observable 2 value: {{obs2$ | async}}</p>
<p>Observable 3 value: {{obs3$ | async}}</p>

<!-- Pro Tip -->
<ng-container *ngIf="obs1$ | async as val1">
  <p>Observable 1 value: {{val1}}</p>
</ng-container>
```

As we can see in the above example, during the creation of the component, 3 observables are initialized which emit values every 1 second using `interval` operator from RxJS.

At the same time we can see that these observables are piped with `async` to the template. This means that they will automatically `subscribe` during initialization and `unsubscribe` during component destruction. This constitutes a big advantage, because we do not bother to do it ourselves through code.

## 2. RxJS Operators

One more time RxJS is here to help us. It provides us a lot of useful operators that help us to to unsubscribe from subscriptions.

Firstly, let's look at some definitions with what we will use:

- **Subject**: A `Subject` is a special type of Observable which shares a single execution path among observers. You can think of this as a single speaker talking at a microphone in a room full of people. Their message (the subject) is being delivered to many (multicast) people (the observers) at once. This is the basis of multicasting. Typical observables would be comparable to a 1 on 1 conversation.
- **interval**: An operator that returns an observable which emits numbers in sequence based on provided timeframe.
- **takeUntil**: A filtering operator that emits values until provided observable emits.

Example:

```ts
// take-until-example.component.ts

import { Component, OnDestroy, OnInit } from '@angular/core';
import { interval, Subject } from 'rxjs';
import { takeUntil } from 'rxjs/operators';
import { Logger } from '../utils/logger';

@Component(/* ... */)
export class TakeUntilExampleComponent implements OnInit, OnDestroy {
  private logger = new Logger(TakeUntilExampleComponent.name);
  private unsubscribe$ = new Subject<void>();

  constructor() {}

  ngOnInit() {
    interval(1000)
      .pipe(takeUntil(this.unsubscribe$))
      .subscribe((value) => {
        this.logger.log('sub1', value);
      });
    interval(1000)
      .pipe(takeUntil(this.unsubscribe$))
      .subscribe((value) => {
        this.logger.log('sub2', value);
      });
    interval(1000)
      .pipe(takeUntil(this.unsubscribe$))
      .subscribe((value) => {
        this.logger.log('sub3', value);
      });
  }

  ngOnDestroy() {
    this.unsubscribe$.next();
    this.unsubscribe$.complete();
  }
}
```

Now let us look in detail at the above example step by step:

- First of all, we initialize a new `Subject` which does not emit any data type (`void`).
- Then we created 3 observables with the help of `interval` operator.
- We pass to these the `takeUntil` operator as pipe in order to filter values until the `unsubscribe$` emits.
- Last but not least step is to trigger `unsubscribe$` during the `ngOnDestroy`. We call `.next()` method to trigger new value emission and `.complete()` to automatically unsubscribe all the observers.

Although we used only the `takeUntil` operator, there are other which can help us, too. For example we can use:

- **take**: It emits provided number of values before completing. You can use it when you are interested in only the first emission, you want to use `take`. Maybe you want to see what the user first clicked on when they entered the page, or you would want to subscribe to the click event and just take the first emission.
- **takeWhile**: It emits values until provided expression is false. You can use it when the optional inclusive parameter is set to true it will also emit the first item that didn't pass the predicate.

## 3. Custom Array of Subscriptions

Another way to unsubscribe a `Subscription` is to put them in an array. So we can iterate all items and call the `.unsubscribe()` method for each item in this array during component destruction.

Example:

```ts
// custom-array-example.component.ts

import { Component, OnDestroy, OnInit } from '@angular/core';
import { interval, Subscription } from 'rxjs';
import { Logger } from '../utils/logger';

@Component(/* ... */)
export class CustomArrayExampleComponent implements OnInit, OnDestroy {
  private logger = new Logger(CustomArrayExampleComponent.name);
  private subs: Subscription[] = [];

  constructor() {}

  ngOnInit() {
    const sub1 = interval(1000).subscribe((value) => {
      this.logger.log('sub1', value);
    });
    this.subs.push(sub1);

    const sub2 = interval(1000).subscribe((value) => {
      this.logger.log('sub2', value);
    });
    this.subs.push(sub2);

    const sub3 = interval(1000).subscribe((value) => {
      this.logger.log('sub3', value);
    });
    this.subs.push(sub3);
  }

  ngOnDestroy() {
    this.subs.forEach((s) => s.unsubscribe());
  }
}
```

This method works very well with multiple subscriptions without any third-party library. However, there are 3 important disadvantages:

- We need to initialize an extra variable in the component
- For each new `Subscription` we must add it to the array
- We must not forget to iterate the array and unsubscribe its items at `ngOnDestroy`

## 4. `subsink` npm package

Now it's time to view some third-party libraries that facilitate us to unsubscribe our subscriptions. More specifically, [SubSink](https://github.com/wardbell/subsink) library constitutes a RxJS subscription sink for unsubscribing gracefully in a component. Its usage is very simple and you can see it in action below.

Example:

```ts
// subsink-example.component.ts

import { Component, OnDestroy, OnInit } from '@angular/core';
import { interval } from 'rxjs';
import { SubSink } from 'subsink';
import { Logger } from '../utils/logger';

@Component(/* ... */)
export class SubsinkExampleComponent implements OnInit, OnDestroy {
  private logger = new Logger(SubsinkExampleComponent.name);
  private subs = new SubSink();

  constructor() {}

  ngOnInit() {
    this.subs.sink = interval(1000).subscribe((value) => {
      this.logger.log('sub1', value);
    });
    this.subs.sink = interval(1000).subscribe((value) => {
      this.logger.log('sub2', value);
    });
    this.subs.sink = interval(1000).subscribe((value) => {
      this.logger.log('sub3', value);
    });
  }

  ngOnDestroy() {
    this.subs.unsubscribe();
  }
}
```

As in the above examples, we initialize the `subs` variable as new instance of `SubSink` class. The second step is to use the `sink` property to collect the subscriptions using a setter. Finally, we call the `unsubscribe()` method to unsubscribe all of them, as we would do in our component `OnDestroy` lifecycle event.

As you can see, it has a dead simple usage and similar behaviour with the previous approach with custom array of subscriptions.

## 5. `@ngneat/until-destroy` npm package

> :thumbsup: Personal Preference

Last but not least, we will see the [@ngneat/until-destroy](https://github.com/ngneat/until-destroy) library.
Similar to the previous approach, we have very simple use within our source code.

Example:

```ts
// until-destroy-example.component.ts

import { Component, OnInit } from '@angular/core';
import { UntilDestroy, untilDestroyed } from '@ngneat/until-destroy';
import { interval } from 'rxjs';
import { Logger } from '../utils/logger';

@UntilDestroy()
@Component(/* ... */)
export class UntilDestroyExampleComponent implements OnInit {
  private logger = new Logger(UntilDestroyExampleComponent.name);

  constructor() {}

  ngOnInit() {
    interval(1000)
      .pipe(untilDestroyed(this))
      .subscribe((value) => {
        this.logger.log('sub1', value);
      });
    interval(1000)
      .pipe(untilDestroyed(this))
      .subscribe((value) => {
        this.logger.log('sub2', value);
      });
    interval(1000)
      .pipe(untilDestroyed(this))
      .subscribe((value) => {
        this.logger.log('sub3', value);
      });
  }
}
```

In this case, we have slightly different approach as it uses decorators. Firstly, we attach the `@UntilDestroy()` decorator at our component and then we pass the `untilDestroyed` operator in observable's pipe.

Although each case suits to different use cases, this is my favorite approach and I use it very often in my projects. Personally I prefer to install a third-party library and get clean and easy to read source code.

## Conclusion :white_check_mark:

Hooray! We made it to the end! :raised_hands:

I hope you enjoyed this article and will make your applications even more clean without any memory leak issues by choosing the most suitable way to unsubscribe your observables.

> **I would be more than happy to hear your personal preference or suggest other ways, leaving your comments down below!**

Please support this article with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).

**You can find the final source code in stackblitz:**

<!-- custom tag to embed stackblitz -->
<!-- https://stackblitz.com/edit/angular-ivy-crdui2 -->

{% stackblitz angular-ivy-crdui2 %}

<sup>_Cover Photo by [Ali Kazal](https://unsplash.com/@lureofadventure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/)._</sup>
