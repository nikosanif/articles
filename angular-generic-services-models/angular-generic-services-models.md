# How To Create Generic CRUD Service & Models In Angular

## Introduction

In this article, we are going to learn how to create generic models and services step-by-step regarding the CRUD feature of resources in Angular.

In a nutshell, we're going to learn:

- How to use generics in Typescript
- What is CRUD operations
- How to create generic models in Angular
- How to create generic service for CRUD in Angular

## The Problem :thinking:

One of the most important principles in programming is the "Don't-Repeat-Yourself" (**DRY**) principle when it comes to writing dynamic and reusable code. Thankfully, Typescript supports generics and allow us to achieve this.

> @TODO

## Prerequisites :zap:

First things first! Before we start to analyze our main content, we must understand how does generics work in Typescript and the definition of CRUD in APIs.

> :sparkles: Feel free to skip this section if are already familiar with these topics.

### Understanding Generics

Generics are awesome! It allows us to keep our code clean and reusable avoiding duplications. I believe that whoever has used it with Typescript, loves it. The rest of you will love it after this article :laughing:.

#### :arrow_right: Example 1 - Type-Safe Generics

The first example that we will see, presents a method where it takes as arguments 2 parameters and returns an object based on them. It is a very simple method but with powerful types.

As you can see below, we use `T` and `U` as generic types which enforces both arguments and returned type to be the same type.
If we pass as first argument a value of type `string`, we are able to know that the property `value1` of the result will be `string` as well. Maybe it looks like a little bit dummy but imagine a real world example (e.g. the main topic of this article).

Furthermore, if we look the following example closer, we can predefine the returned object by adding `objectify<string, number>(...)`. Thus, we enforce the first argument to be of type `string` and the second of type `number`. If we try to pass a different type (e.g boolean), then we'll have a type error.

```ts
function objectify<T, U>(value1: T, value2: U): { value1: T; value2: U } {
  return { value1, value2 };
}

const obj1 = objectify<string, number>('Hello World', 10);
const obj2 = objectify<number, boolean>(10, true);

console.log(obj1); // Output: { value1: "Hello World", value2: 10 }
console.log(obj2); // Output: { value1: 10, value2: true }
```

#### :arrow_right: Example 2 - Generic Classes

The second example concerns `class`-es. Yes, classes! Generics work not only with functions but for `class`, `interface`, even `type`. In this example, we will create a custom class implementing a simple functionality of array. It can be initialized by a given array of items, add new item, and get all items.

We use `T` as generic type to declare the type of the stored items in our custom class. Thus, we ensure that all items will be the same type. For example, if we add the following `new MyCustomArray<number>()`, we restrict our instance to accept only values of type `number` at `addItem()` method. Also, we can infer that the `getItems()` method will return a list of values of type `number`.

```ts
class MyCustomArray<T> {
  private items: T[];

  constructor(initialItems: T[]) {
    this.items = initialItems;
  }

  getItems(): T[] {
    return this.items;
  }

  addItem(item: T) {
    this.items.push(item);
  }
}

const instance1 = new MyCustomArray<number>([1, 2]);
instance1.addItem(3);

const instance2 = new MyCustomArray<string>(['foo']);
instance2.addItem('bar');

console.log(instance1.getItems()); // Output: [1, 2, 3]
console.log(instance2.getItems()); // Output: ["foo", "bar"]
```

#### :arrow_right: Example 3 - Generic Constraints

In our third example, we'll learn how to add constraints at generic types. Bellow you will find a method that echoes a value, it accepts an argument and returns it as is. But the main difference is that the value must be either of type `string` or `number`. We use `T` generic type and we extend it to be `string | number`.

Thus, if we try to write something like `echo<boolean>(true)`, Typescript will throw a type error. This functionality is very useful because sometimes our implementation requires specific criteria and we can limit the accepted values.

```ts
function echo<T extends string | number>(value: T): T {
  return value;
}

const result1 = echo<string>('Hello World');
const result2 = echo<number>(10);

console.log(result1); // Output: Hello World
console.log(result2); // Output: 10
```

#### :arrow_right: Example 4 - Bonus

We have succeeded so far! Let's see a bonus example in action with VSCode!

Let's create an `Animal` interface that can be extended by a `Cat`. But, the `Cat` can only have 2 colors - white or black (no grey zone here :laughing:).

```ts
interface Animal {
  name: string;
  voice: string;
}

interface Cat<T extends 'black' | 'white'> extends Animal {
  color: T;
}
```

When we are trying to create an object of type `Cat`, the editor will help us by suggesting us only the 2 main colors. You can see the picture below:

![Initialize cat type](./assets/cat-1.png)

Now, let's create a black cat (I hope this does not mean bad luck for this article :sweat:). As you can see, we have set the generic type to `black` with `Cat<'black'>` and the editor give us as the only option to set `color: 'black'`.

![Set color property of cat type](./assets/cat-2.png)

```ts
const myCat: Cat<'black'> = {
  color: 'black',
  name: 'Kitty',
  voice: 'meow',
};
```

Now let's forget cats and see a real world example... The following code is about how to apply generics at `type`. We have created a type that enforces an array to have a least one item.

We used `T[]` generic type which tell us that the type is an array of items and `{ 0: T }` which indicates that the first item will not be empty. As you already noticed we have used the `&` operator that will enforce the `NonEmptyArray` type to fulfill both cases.

```ts
export type NonEmptyArray<T> = T[] & { 0: T };

function getFirstItemOfArray(items: NonEmptyArray<T>): T {
  return items[0];
}
```

### What is CRUD?

> **C**reate, **R**ead, **U**pdate, and **D**elete :point_right: **CRUD**

Starting from the acronym, CRUD means create, read, update, and delete. These are the four main functionalities that must be provided by all models when building APIs.

Let's see an example to see it in action. In this article we will examine the example of `User` which is very common to most of real world applications. We simplify the model with the following properties:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "nikos@email.com",
  "createdAt": "2021-09-22T16:21:47.760Z",
  "updatedAt": "2021-09-22T16:21:47.851Z"
}
```

#### :arrow_right: Create

| Operation | Endpoint     | Status Code   |
| --------- | ------------ | ------------- |
| `POST`    | `/api/users` | 201 (Created) |

#### :arrow_right: Read

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `GET`     | `/api/users`      | 200 (OK)    |
| `GET`     | `/api/users/{id}` | 200 (OK)    |

#### :arrow_right: Update

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `PUT`     | `/api/users/{id}` | 200 (OK)    |

#### :arrow_right: Delete

| Operation | Endpoint          | Status Code      |
| --------- | ----------------- | ---------------- |
| `DELETE`  | `/api/users/{id}` | 204 (No Content) |

## Generic CRUD Model

Once we have walked about the theory of generics, now it's time to see it in action.

Before start to implement the generic service of CRUD. We must define the generic model of all resources. In this article, we assume that all models have some common properties like `id`, `createdAt`, and `updatedAt`.

Thus, we create a generic abstract class that contains all these common properties.
Also we used the generic `T` type to identify the model that we are going to extend. Last but not least, the class is going to apply logic in the constructor about property casting and provide a common method `toJSON()` which allows us to return a pure JSON based on the instance.

Below we can see the full implementation:

```ts
export abstract class ResourceModel<T> {
  public id?: number;
  public createdAt?: Date;
  public updatedAt?: Date;

  constructor(model?: Partial<T>) {
    if (model) {
      Object.assign(this, model);
    }
    if (this.createdAt) {
      this.createdAt = new Date(this.createdAt);
    }
    if (this.updatedAt) {
      this.updatedAt = new Date(this.updatedAt);
    }
  }

  public toJSON(): any {
    return JSON.parse(JSON.stringify(this));
  }
}
```

### Example

Do you remember the example of user model? Great! The same model will be used here. Below there is an example how we can extend the user model:

```ts
export class User extends ResourceModel<User> {
  public firstName!: string;
  public lastName!: string;
  public email!: string;

  constructor(model?: Partial<User>) {
    super(model);
  }
}

// ------ example ------
const johnDoe = new User({ firstName: 'John', lastName: 'Doe', ... });
const johnDoeAsJSON = johnDoe.toJSON();
```

## Generic CRUD Service

Before we start generating and writing code, let’s take a step back and see the bigger picture. The generic service should accept the following arguments: the `HttpClient` is used for the HTTP requests, the class of model for object casting, and the path of the API endpoints.

Also, all resources should have 5 main methods related to CRUD…

- **Create** - Returns a new resource.
- **Get all** - Retrieves all resources as a list.
- **Get by ID** - Returns a specific resource by ID.
- **Update** - Updates a specific resource by ID.
- **Delete** - Removes a specific resource by ID.

Great, let’s create our methods step by step now.

### :one: Create

The `create()` method accepts a partial model as argument and returns the created model from server. We say "partial" because before we create the resource, some properties are not available (e.g. `id`, `createdAt`, etc). Also, it converts the result to an instance of model's class.

> **TIP:** All methods try to create instances of model's class in order to apply and benefit extra functionality from them (e.g. convert string dates to actual `Date` in constructor or for future usage of their methods such as `toJSON()` function).

```ts
public create(resource: Partial<T> & { toJSON: () => T }): Observable<T> {
  return this.httpClient
    .post<T>(`${this.apiUrl}`, resource.toJSON())
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
public update(resource: Partial<T> & { toJSON: () => T }): Observable<T> {
  return this.httpClient
    .put<T>(`${this.apiUrl}/${resource._id}`, resource.toJSON())
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

import { ResourceModel } from '@turintech/shared/data-access/models';

export abstract class ResourceService<T extends ResourceModel<T>> {
  constructor(
    private httpClient: HttpClient,
    private tConstructor: { new (m: Partial<T>, ...args: unknown[]): T },
    protected apiUrl: string
  ) {}

  public create(resource: Partial<T> & { toJSON: () => T }): Observable<T> {
    return this.httpClient
      .post<T>(`${this.apiUrl}`, resource.toJSON())
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

  public update(resource: Partial<T> & { toJSON: () => T }): Observable<T> {
    return this.httpClient
      .put<T>(`${this.apiUrl}/${resource._id}`, resource.toJSON())
      .pipe(map((result) => new this.tConstructor(result)));
  }

  public delete(id: number): Observable<void> {
    return this.httpClient.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

## Conclusion :white_check_mark:

Hooray! We made it to the end! :raised_hands:

I hope you enjoyed this article and wil make your applications' code even more generic and reusable following the DRY principle. Also, I hope to use this article not only for the CRUD feature but whenever it's possible in your apps by using generics.

Please support this article with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).

## References

- [Typescript Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [How To Use Generics in TypeScript](https://www.digitalocean.com/community/tutorials/typescript-generics-in-typescript)
- [What is CRUD?](https://www.codecademy.com/articles/what-is-crud)
- [Create, read, update and delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
