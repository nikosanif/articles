# How To Create Generic CRUD Service & Models In Angular

## Introduction

In this article, we will see how to create generic models and services regarding the CRUD feature of resources in Angular.

In a nutshell, we're going to learn:

- How to use generics in Typescript
- What is CRUD operations
- How to create generic models in Angular?
- How to create generic service for CRUD in Angular?

## The Problem :thinking:

One of the most important principles in programming is the "Don't-Repeat-Yourself" (**DRY**) principle when it comes to writing dynamic and reusable code. Thankfully, Typescript supports generics and allow us to achieve this.

> @TODO

## Prerequisites :zap:

First things first! Before we start to analyze our main content, we must understand how does generics work in Typescript and the definition of CRUD in APIs.

> :sparkles: Feel free to skip this section if are already familiar with these concepts.

### Understanding Generics

```ts
function objectify<T, U>(value1: T, value2: U): { value1: T; value2: U } {
  return { value1, value2 };
}

const obj1 = objectify<string, number>('Hello World', 10);
const obj2 = objectify<number, boolean>(10, true);

console.log(obj1); // Output: { value1: "Hello World"; value2: 10 }
console.log(obj2); // Output: { value1: 10; value2: true }
```

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

```ts
function echo<T extends string | number>(value: T): T {
  return value;
}

const result1 = echo<string>('Hello World');
const result2 = echo<number>(10);

console.log(result1); // Output: Hello World
console.log(result2); // Output: 10
```

### What is CRUD?

## References

- [Typescript Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [How To Use Generics in TypeScript](https://www.digitalocean.com/community/tutorials/typescript-generics-in-typescript)
- [What is CRUD?](https://www.codecademy.com/articles/what-is-crud)
- [Create, read, update and delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
