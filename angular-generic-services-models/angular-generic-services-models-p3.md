# How To Create Generic CRUD Service & Models In Angular - Part 3

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
