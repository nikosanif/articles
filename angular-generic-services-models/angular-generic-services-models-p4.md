# Generic CRUD Service in Angular

<sup>_Cover Photo by [Xan Griffin](https://unsplash.com/@xangriffin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/)._</sup>

This article is a part of a series about how to create **Generic CRUD Service & Models in Angular**:

- [Part 1 - Understanding Generics](https://dev.to/nikosanif/generic-crud-service-in-angular-part-1-understanding-generics-2n75)
- [Part 2 - What is CRUD?](https://dev.to/nikosanif/generic-crud-service-in-angular-part-2-what-is-crud-30ek)
- [Part 3 - Generic CRUD Model](https://dev.to/nikosanif/generic-crud-service-in-angular-part-3-generic-crud-model-2hl)
- :point_right: **Part 4 - Generic CRUD Service in Angular**

## Generic CRUD Service

Before we start generating and writing code, let’s take a step back and see the bigger picture. The generic service should accept the following arguments:

- the `HttpClient` is used for the HTTP requests
- the class of model for creating instances
- the path of the API endpoints.

Also, all resources should have 5 main methods related to CRUD…

- **Create** - Returns a new resource.
- **Get all** - Retrieves all resources as a list.
- **Get by ID** - Returns a specific resource by ID.
- **Update** - Updates a specific resource by ID.
- **Delete** - Removes a specific resource by ID.

Great, let’s create our methods step by step now.

### :one: Create

The `create()` method accepts a partial model as argument and returns the created model from server. We say "partial" because before we create the resource, some properties are not available (e.g. `id`, `createdAt`, etc). Also, it converts the result to an instance of model's class.

> **TIP:** All methods try to create instances of model's class in order to apply and benefit extra functionality from them (e.g. convert string dates to actual `Date` in constructor or for future usage of their methods such as `toJson()` function).

```ts
public create(resource: Partial<T> & { toJson: () => T }): Observable<T> {
  return this.httpClient
    .post<T>(`${this.apiUrl}`, resource.toJson())
    .pipe(map((result) => new this.tConstructor(result)));
}
```

### :two: Get all

The `get()` method returns an `Observable` with a list of all existing resources. It accepts no arguments and iterates the list to create multiple instances instead of simple JSON objects.

```ts
public get(): Observable<T[]> {
  return this.httpClient
    .get<T[]>(`${this.apiUrl}`)
    .pipe(map((result) => result.map((i) => new this.tConstructor(i))));
}
```

### :three: Get by ID

The next method of "read" is `getById()`. As is obvious, it accepts as argument an ID of type `number` and returns an `Observable` of the existing resource instance.

```ts
public getById(id: number): Observable<T> {
  return this.httpClient
    .get<T>(`${this.apiUrl}/${id}`)
    .pipe(map((result) => new this.tConstructor(result)));
}
```

### :four: Update

When we want to update an existing resource, we'll use the `update()` method. It accepts a partial model (e.g. only properties that we want to update) and returns the updated instance as `Observable`.

```ts
public update(resource: Partial<T> & { toJson: () => T }): Observable<T> {
  return this.httpClient
    .put<T>(`${this.apiUrl}/${resource.id}`, resource.toJson())
    .pipe(map((result) => new this.tConstructor(result)));
}
```

### :five: Delete

Finally, the `delete()` method removes completely an existing resource from the server by a given ID. It accepts a number as argument that matches the ID of the model, but it does not return anything (`Observable<void>`).

```ts
public delete(id: number): Observable<void> {
  return this.httpClient.delete<void>(`${this.apiUrl}/${id}`);
}
```

### :arrow_right: Final result

Once we described one-by-one all methods, now it's time to see the final result of the generic service:

```ts
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

import { ResourceModel } from 'your-path-to-model'; // see: Part 3

export abstract class ResourceService<T extends ResourceModel<T>> {
  constructor(
    private httpClient: HttpClient,
    private tConstructor: { new (m: Partial<T>, ...args: unknown[]): T },
    protected apiUrl: string
  ) {}

  public create(resource: Partial<T> & { toJson: () => T }): Observable<T> {
    return this.httpClient
      .post<T>(`${this.apiUrl}`, resource.toJson())
      .pipe(map((result) => new this.tConstructor(result)));
  }

  public get(): Observable<T[]> {
    return this.httpClient
      .get<T[]>(`${this.apiUrl}`)
      .pipe(map((result) => result.map((i) => new this.tConstructor(i))));
  }

  public getById(id: number): Observable<T> {
    return this.httpClient
      .get<T>(`${this.apiUrl}/${id}`)
      .pipe(map((result) => new this.tConstructor(result)));
  }

  public update(resource: Partial<T> & { toJson: () => T }): Observable<T> {
    return this.httpClient
      .put<T>(`${this.apiUrl}/${resource.id}`, resource.toJson())
      .pipe(map((result) => new this.tConstructor(result)));
  }

  public delete(id: number): Observable<void> {
    return this.httpClient.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

Finally, here a working example of users' service:

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { User } from 'your-path-to-user-model';
import { ResourceService } from 'your-path-to-resource-service';

@Injectable({ providedIn: 'root' })
export class UsersService extends ResourceService<User> {
  constructor(private http: HttpClient) {
    super(http, User, `your-api-of-users-here`);
  }
}
```

**You can find the final source code in stackblitz:**

<!-- custom tag to embed stackblitz -->
<!-- https://stackblitz.com/edit/angular-ivy-spjlcf -->

{% stackblitz angular-ivy-spjlcf %}

## Conclusion :white_check_mark:

Hooray! We made it to the end! :raised_hands:

I hope you enjoyed this series of article and you will make your applications' code even more generic and reusable following the DRY principle. Also, I hope to use this article not only for the CRUD feature but whenever it's possible in your apps by using generics.

Please support this article (and the previous parts) with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).

Author: Nikos Anifantis ✍️

[![Twitter](https://img.shields.io/twitter/url/https/twitter.com/nikosanif.svg?style=social&label=Follow%20nikosanif)](https://twitter.com/nikosanif) [![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?style=social&style=flat&logo=linkedin&labelColor=blue&label=Connect%20Nikos%20Anifantis)](https://www.linkedin.com/in/nikosanifantis/)
