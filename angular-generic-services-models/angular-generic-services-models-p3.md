# Generic CRUD Service in Angular: Part 3 - Generic CRUD Model

<sup>_Cover Photo by [Randy Fath](https://unsplash.com/@randyfath?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyTex) on [Unsplash](https://unsplash.com/)._</sup>

This article is a part of a series about how to create **Generic CRUD Service & Models in Angular**:

- [Part 1 - Understanding Generics](https://dev.to/nikosanif/generic-crud-service-in-angular-part-1-understanding-generics-2n75)
- [Part 2 - What is CRUD?](https://dev.to/nikosanif/generic-crud-service-in-angular-part-2-what-is-crud-30ek)
- :point_right: **Part 3 - Generic CRUD Model**
- [Part 4 - Generic CRUD Service in Angular](https://dev.to/nikosanif/generic-crud-service-in-angular-part-4-3neo)

## Generic CRUD Model

Once we have walked about the theory of generics ([Part 1](https://dev.to/nikosanif/generic-crud-service-in-angular-part-1-understanding-generics-2n75)) and we have understand the main CRUD methods ([Part 2](https://dev.to/nikosanif/generic-crud-service-in-angular-part-2-what-is-crud-30ek)), now it's time to see it in action.

Before start to implement the generic service of CRUD. We must define the generic model of all resources.

In this article, we assume that all models have some common properties like `id`, `createdAt`, and `updatedAt`.

Thus, we create a generic abstract class that contains all these common properties.
Also we used the generic `T` type to identify the model that we are going to extend.

Last but not least, the class is going to apply logic in the constructor about property casting and provide a common method `toJson()` which allows us to return a pure JSON based on the instance.

Below we can see the full implementation of the generic CRUD model:

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

  public toJson(): any {
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
const johnDoeAsJSON = johnDoe.toJson();
```

## What's next?

- **[Part 4 - Generic CRUD Service in Angular](https://dev.to/nikosanif/generic-crud-service-in-angular-part-4-3neo)**

Author: Nikos Anifantis ✍️

[![Twitter](https://img.shields.io/twitter/url/https/twitter.com/nikosanif.svg?style=social&label=Follow%20nikosanif)](https://twitter.com/nikosanif) [![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?style=social&style=flat&logo=linkedin&labelColor=blue&label=Connect%20Nikos%20Anifantis)](https://www.linkedin.com/in/nikosanifantis/)
