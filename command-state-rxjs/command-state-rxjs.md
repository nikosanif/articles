# Managing Requests State in Angular with RxJS and Adapters

Managing the state of asynchronous operations is a common task in Angular applications. Whether you're fetching data from a server, handling errors, or showing loading spinners, it's important to maintain clear control over different states like **loading**, **success**, and **error**.

In this article, we’ll explore how to manage such states using **adapters** in Angular with RxJS, making it simple to handle server requests and display state directly in the template using the `async` pipe.

## The Problem: Managing Multiple States

When dealing with server requests, we often encounter multiple states like:

- **Loading**: When the request is in progress.
- **Success**: When data is successfully retrieved.
- **Error**: When something goes wrong, like a network or server error.

Typically, you might manage these states manually in your components, which can lead to repetitive code. The goal here is to **abstract** this logic into a reusable **adapter** that makes state management clean and consistent.

## The Solution: Using an Adapter

An adapter is a reusable utility that helps manage these states without repeating code. Here, we’re going to create a command adapter that can handle the three key states (loading, success, error) and simplify their usage within an Angular component.

### Step 1: Defining the Command Adapter interfaces

First, let's define the interfaces for the command adapter. We’ll create a `CommandStatus` type and a `CommandState` interface that holds the state of the command:

```typescript
// @file: interfaces.ts

export type CommandStatus = 'pending' | 'loading' | 'success' | 'error';

export interface CommandState<T, E = unknown> {
  status: CommandStatus;
  error: E | null;
  data: T | null;
}
```

### Step 2: Creating the Command Adapter

Next, let's look at the `createCommandAdapter`. This function will return an object with specific setters and getters for managing the state transitions.

Let's create a `commandSetters` function that returns an object with these state transition functions:

```typescript
// @file: command-adapter.ts

import { CommandState, CommandStatus } from './interfaces';

export function commandSetters<T, E = unknown>() {
  return {
    setPending: (state: CommandState<T, E>): CommandState<T, E> => ({
      ...state,
      status: 'pending',
      error: null,
    }),

    setLoading: (state: CommandState<T, E>): CommandState<T, E> => ({
      ...state,
      status: 'loading',
      error: null,
    }),

    setSuccess: (state: CommandState<T, E>, data: T): CommandState<T, E> => ({
      ...state,
      status: 'success',
      error: null,
      data,
    }),

    setError: (state: CommandState<T, E>, error: E): CommandState<T, E> => ({
      ...state,
      status: 'error',
      error,
    }),

    reset: (state: CommandState<T, E>): CommandState<T, E> => ({
      ...state,
      status: 'pending',
      error: null,
      data: null,
    }),
  };
}
```

What about getters? Let's create a `commandGetters` function that returns an object with state selectors like `selectData`, `selectError`, and `selectStatus`:

```typescript
// @file: command-adapter.ts

// Existing code...

export function commandSelectors<T, E = unknown>() {
  return {
    selectData: (state: CommandState<T, E>): T | null => state.data,
    selectError: (state: CommandState<T, E>): E | null => state.error,
    selectStatus: (state: CommandState<T, E>): CommandStatus => state.status,

    isPending: (state: CommandState<T, E>): boolean =>
      state.status === 'pending',
    isLoading: (state: CommandState<T, E>): boolean =>
      state.status === 'loading',
    isSuccess: (state: CommandState<T, E>): boolean =>
      state.status === 'success',
    hasError: (state: CommandState<T, E>): boolean => state.status === 'error',
    isDone: (state: CommandState<T, E>): boolean =>
      state.status === 'success' || state.status === 'error',

    // TIP: Add more selectors as needed
  };
}
```

Now, let's combine the setters and getters into a single `createCommandAdapter` function that returns an object with all the necessary functions:

```typescript
// @file: command-adapter.ts

// Existing code...

export function createCommandAdapter<T, E = unknown>() {
  return {
    getInitialState: (
      data: T | null = null,
      initialState: Partial<CommandState<T, E>> = {}
    ): CommandState<T, E> => ({
      status: 'pending',
      error: null,
      ...initialState,
      data,
    }),

    ...commandSetters<T, E>(),
    ...commandSelectors<T, E>(),
  };
}
```

### Step 3: Integrating with RxJS

Now that we have an adapter for managing state, we can integrate it into an RxJS stream using `withCommandState`. This higher-order function wraps any observable (like a server request) and handles the state transitions for you.

```typescript
// @file: with-command-state.ts

import { Observable, catchError, map, of, startWith } from 'rxjs';
import { createCommandAdapter } from './command-adapter';

export function withCommandState<T, E = unknown>() {
  const adapter = createCommandAdapter<T, E>();
  const initialState = adapter.getInitialState();

  return (o: Observable<T>) =>
    o
      .pipe(
        map((data: T) => adapter.setSuccess(initialState, data)),
        catchError((error: E) => of(adapter.setError(initialState, error))),
        startWith(adapter.setLoading(initialState))
      )
      .pipe(
        map((state) => ({
          ...state,
          isLoading: adapter.isLoading(state),
          isSuccess: adapter.isSuccess(state),
          hasError: adapter.hasError(state),
          isDone: adapter.isDone(state),
        }))
      );
}
```

### Step 4: Using the Adapter in a Component

Now that the heavy lifting is done, let's put it to work in an Angular component. We’ll simulate a server request using RxJS and apply `withCommandState` to manage the state.

Here’s how you can use it in a component:

<!-- prettier-ignore-start -->
```typescript
// @file: server-request.component.ts

// Existing imports...

@Component({
  selector: 'app-server-request',
  standalone: true,
  imports: [AsyncPipe, JsonPipe],
  template: `
    <h1>Server Request Example</h1>

    @if (serverRequest$ | async; as state) {
      @if (state.isLoading) {
        🕦 Loading...
      }

      @if (state.isSuccess) {
        ✅ Data: {{ state.data | json }}
      }

      @if (state.hasError) {
        ❌ Failed to load data: {{ state.error | json }}
      }

      <!-- TIP: Add more state checks as needed -->

      @if (state.isDone && !state.hasError && !state.data) {
        🤷 No data available
      }
    }
  `,
})
export class ServerRequestComponent {
  private readonly http = inject(HttpClient);

  readonly serverRequest$ = this.http
    .get('https://api.example.com/data')
    .pipe(withCommandState()); // 👈 Apply state management
}
```
<!-- prettier-ignore-end -->

The magic of Angular’s `async` pipe allows us to subscribe to observables directly in the template and automatically handle unsubscribing. Here’s what happens in the template:

- When the request starts, the `status` is set to `loading`, and the **Loading...** message appears.
- Once the data is fetched, the status changes to `success`, and the **Data: Server data** message is displayed.
- If an error occurs, the status changes to `error`, and the appropriate error message is shown.
- Additional checks like `isDone` can be used to display fallback messages.

This approach keeps your component clean by managing the state transitions in the adapter, and the template naturally reflects the current state.

### Benefits of This Approach

- **Cleaner code**: Instead of manually setting loading, success, and error states in each component, the adapter centralizes this logic, making your code DRY (Don’t Repeat Yourself).
- **Easier testing**: By separating state management from your component, you can easily test state transitions in isolation.
- **Reusability**: The adapter can be reused across different parts of your application, making it simple to handle server requests consistently.
- **Reactive by nature**: Leveraging RxJS streams allows you to build reactive UIs that respond to changes in real-time.

### Bonus: Using `Angular Signals` and `toSignal()`

If you’re using Angular 18 or later, you can take advantage of the new [Angular Signals](https://angular.dev/guide/signals) feature to manage state transitions in your components. Here’s how you can use it with the command adapter:

<!-- prettier-ignore-start -->
```typescript
// @file: server-request.component.ts

// Existing imports...
import { toSignal } from '@angular/core/rxjs-interop';

@Component({
  selector: 'app-server-request',
  standalone: true,
  imports: [],
  template: `
    <h1>Server Request Example with Signals</h1>

    @if (serverRequestSignal(); as state) {
      @if (state.isLoading) {
        🕦 Loading...
      }

      @if (state.isSuccess) {
        ✅ Data: {{ state.data | json }}
      }

      @if (state.hasError) {
        ❌ Failed to load data: {{ state.error | json }}
      }

      <!-- TIP: Add more state checks as needed -->

      @if (state.isDone && !state.hasError && !state.data) {
        🤷 No data available
      }
    }
  `,
})
export class ServerRequestComponent {
  private readonly http = inject(HttpClient);

  readonly serverRequest$ = this.http
    .get('https://api.example.com/data')
    .pipe(withCommandState()); // 👈 Apply state management

  // Convert the observable to a signal 👇
  readonly serverRequestSignal = toSignal(this.serverRequest$);
}
```
<!-- prettier-ignore-end -->

By using `toSignal()`, you can convert the observable into a signal that can be used directly in the template. Also, we removed the `AsyncPipe` imports since Angular Signals handle these automatically. This approach is more aligned with Angular’s reactive programming model and simplifies the handling of asynchronous operations.

Of course, you can implement your own custom implementation using Angular Signals directly instead of using the `withCommandState` operator, but this might be another topic for a future article. 😉

### Conclusion

Managing the state of asynchronous operations in Angular doesn’t have to be complicated. By using an adapter and RxJS, we can abstract the logic for handling **loading**, **success**, and **error** states, allowing us to focus on building a clean and efficient UI.

By following the pattern of combining `withCommandState` with the `async` pipe, you can keep your components streamlined and handle server requests with ease. This approach is agnostic and can be extended or customized to fit various scenarios in your application.

If you found this article helpful, please support it with your :heart: to help it reach a wider audience. :pray:

Feel free to reach out if you have any questions—leave your comments below or DM me on X [@nikosanif](https://x.com/nikosanif) or [LinkedIn](https://linkedin.com/in/nikosanifantis).
