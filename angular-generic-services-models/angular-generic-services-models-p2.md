# Generic CRUD Service in Angular: Part 2 - What is CRUD?

<sup>_Cover Photo by [Tobias Fischer](https://unsplash.com/@tofi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](TODO)._</sup>

This article is a part of a series about how to create **Generic CRUD Service & Models in Angular**:

- [Part 1 - Understanding Generics](TODO)
- :point_right: **Part 2 - What is CRUD?**
- Part 3 - Generic CRUD Model _(coming soon...)_
- Part 4 - Generic CRUD Service in Angular _(coming soon...)_

## What is CRUD?

> **C**reate, **R**ead, **U**pdate, and **D**elete :point_right: **CRUD**

Starting from the acronym, CRUD means create, read, update, and delete. These are the four main functionalities that must be provided by all models when building APIs.

Firstly let's explain some basics... An API is a set of definitions and protocols for building and integrating application software. It's the main contract between frontend and backend regarding their communication. An API in order to be RESTful, it should follow the constraints of REST architectural style and allows for interaction with RESTful web services. REST stands for representational state transfer and was created by computer scientist [Roy Fielding](https://en.wikipedia.org/wiki/Roy_Fielding).

So, talking about RESTful APIs, the CRUD feature usually corresponds to the HTTP methods POST, GET, PUT, and DELETE, respectively. These are the basic elements of a persistent storage system.

> **Important note:** Keep in mind that this article **recommends** the most common approaches followed by developers for CRUD regarding responses, status codes, endpoints' paths etc. Follow this article for inspiration to create your rules according your needs. :rocket:

## Model Example

This series of articles examine a very common model for most of real-world applications, the model of `User`.

We simplify the model with the following properties:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@email.com",
  "createdAt": "2021-09-22T16:21:47.760Z",
  "updatedAt": "2021-09-22T16:21:47.851Z"
}
```

In this hypothetical database, let's assume that the `id`, `createdAt`, and `updatedAt` properties are handled only from our server's API. It's not important for now, but we'll notice in the next article that all models share the aforementioned properties.

## :arrow_right: Create

If we want to create a new user in our system, we use `POST` method and the endpoint path should start thi the base followed by the model name (usually in plural). The response should return `201 - Created` status code.

| Operation | Endpoint     | Status Code   |
| --------- | ------------ | ------------- |
| `POST`    | `/api/users` | 201 (Created) |

### Payload:

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@email.com"
}
```

### Response:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@email.com",
  "createdAt": "2021-09-22T16:21:47.760Z",
  "updatedAt": "2021-09-22T16:21:47.851Z"
}
```

## :arrow_right: Read

In order to retrieve all existing models from our database we request with `GET` method at base path of the user model. It's very similar to the previous endpoint, but here we changed the `POST` to `GET`. Also, this method accepts an empty payload as we cannot change users in our database. Finally, we get a list of users as response with `200 - OK` status code.

| Operation | Endpoint     | Status Code |
| --------- | ------------ | ----------- |
| `GET`     | `/api/users` | 200 (OK)    |

### Payload: **None**

### Response:

```json
[
  {
    "id": 1,
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@email.com",
    "createdAt": "2021-09-22T16:21:47.760Z",
    "updatedAt": "2021-09-22T16:21:47.851Z"
  }
  {
    "id": 2,
    "firstName": "John",
    // ...
  }
]
```

Another use case is when we want to retrieve only one specific user by ID. Then, we call the same base model's path, but we add its ID at the end. Another difference is that instead of getting a list of users, the response returns an object with the found user.

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `GET`     | `/api/users/{id}` | 200 (OK)    |

### Payload: **None**

### Response:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@email.com",
  "createdAt": "2021-09-22T16:21:47.760Z",
  "updatedAt": "2021-09-22T16:21:47.851Z"
}
```

## :arrow_right: Update

The "update" functionality is used when we want to modify an existing user. We recommend to send only the values that we want to be updated to the server. The endpoint's path is similar with "read by ID", but we use the `PUT` method. The response should include the updated version of user, followed by a `200 - OK` status code.

| Operation | Endpoint          | Status Code |
| --------- | ----------------- | ----------- |
| `PUT`     | `/api/users/{id}` | 200 (OK)    |

### Payload:

```json
{
  "firstName": "Nikos"
}
```

### Response:

```json
{
  "id": 1,
  "firstName": "Nikos", // <-- Changed
  "lastName": "Doe",
  "email": "john@email.com",
  "createdAt": "2021-09-22T16:21:47.760Z",
  "updatedAt": "2021-09-23T12:13:07.651Z" // <-- Changed from server
}
```

## :arrow_right: Delete

Last but not least, we have the "delete" functionality which is used to delete an existing user by ID. Again the path is the same when reading/updating a model, but we use the `DELETE` method. Another important point here is that neither the payload nor the response transfers any data.

| Operation | Endpoint          | Status Code      |
| --------- | ----------------- | ---------------- |
| `DELETE`  | `/api/users/{id}` | 204 (No Content) |

### Payload: **None**

### Response: **None**

## What's next?

- **Part 3 - Generic CRUD Model** _(coming soon...)_

## References

- [What is CRUD?](https://www.codecademy.com/articles/what-is-crud)
- [Create, read, update and delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)

Author: Nikos Anifantis ✍️

[![Twitter](https://img.shields.io/twitter/url/https/twitter.com/nikosanif.svg?style=social&label=Follow%20nikosanif)](https://twitter.com/nikosanif) [![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?style=social&style=flat&logo=linkedin&labelColor=blue&label=Connect%20Nikos%20Anifantis)](https://www.linkedin.com/in/nikosanifantis/)
