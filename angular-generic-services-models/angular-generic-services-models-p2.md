# How To Create Generic CRUD Service & Models In Angular - Part 2

## What is CRUD?

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

### :arrow_right: Create

| Operation | Endpoint     | Status Code   |
| --------- | ------------ | ------------- |
| `POST`    | `/api/users` | 201 (Created) |

### :arrow_right: Read

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `GET`     | `/api/users`      | 200 (OK)    |
| `GET`     | `/api/users/{id}` | 200 (OK)    |

### :arrow_right: Update

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `PUT`     | `/api/users/{id}` | 200 (OK)    |

### :arrow_right: Delete

| Operation | Endpoint          | Status Code      |
| --------- | ----------------- | ---------------- |
| `DELETE`  | `/api/users/{id}` | 204 (No Content) |

## References

- [What is CRUD?](https://www.codecademy.com/articles/what-is-crud)
- [Create, read, update and delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)
