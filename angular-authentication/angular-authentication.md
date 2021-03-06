# Angular Authentication: Best Practices 🏆

An Angular application that demonstrates best practices for user authentication flow.

By [@nikosanif](https://twitter.com/nikosanif)

<!-- Embedded code for Dev.to -->
<!-- {% github nikosanif/angular-authentication %} -->

Github Repository: [nikosanif/angular-authentication](https://github.com/nikosanif/angular-authentication)

## Live Demo

Live application: [angular-authentication.netlify.app](https://angular-authentication.netlify.app/)

![Angular Authentication Demo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7p2wnj9eqte8ojlv6ziz.png)

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/en/)
- [Angular CLI](https://angular.io/cli)

### Setup & Usage

- Clone this repository: `git clone git@github.com:nikosanif/angular-authentication.git`
- `cd angular-authentication`
- Install dependencies: `npm install`
- Serve the Angular app: `npm start`
- Open your browser at: `http://localhost:4200`

### Useful Commands

- `npm start` - starts a dev server of Angular app
- `npm run build` - builds full prod build
- `npm run lint` - linting source code of this project
- `npm run format:check` - runs prettier to check for formatting errors
- `npm run format:write` - runs prettier to format whole code base
- `npm run release` - runs standard-version to create new release

## Features

### Authentication Flows

![Angular Authentication Demo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hpqovmrkx6dpuc9zkkmo.png)
![Angular Authentication Demo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8pr3yxcwi28h35mqit5e.png)

### Other Features

- Lazy loading of Angular modules
- API requests with `@ngrx/effects`
- Responsive design
- In-memory Web API

## Tech Stack

- [Angular](https://angular.io/)
- [NgRX](https://ngrx.io/) - @ ngrx/{store,effects,component}
- [Taiga UI](https://taiga-ui.dev/)
- [Tailwind CSS](https://tailwindcss.com/)
- Other dev tools
  - ESLint
  - Prettier
  - Husky
  - standard-vesrion

## High-level Design

Below is the high-level structure of the application.

```sh
./src
├── app
│   ├── app # root app component
│   │   ├── app.component.html
│   │   ├── app.component.scss
│   │   └── app.component.ts
│   │
│   ├── app-routing.module.ts
│   ├── app.module.ts
│   │
│   ├── auth # includes authentication logic
│   │   ├── auth.module.ts
│   │   ├── auth.service.ts
│   │   ├── guards
│   │   ├── interceptors
│   │   ├── login
│   │   └── store
│   │
│   ├── core # includes core utilities
│   │   ├── core.module.ts
│   │   ├── fake-api
│   │   └── services
│   │
│   ├── features # all features of application
│   │   ├── about
│   │   ├── home
│   │   └── secured-feat
│   │
│   └── shared # shared UI modules and utilities
│       ├── ui
│       └── util
│
├── assets
│   ├── ...
│
├── environments
│   ├── environment.prod.ts
│   └── environment.ts
│
├── ...
│
└── theme # global theme styles
    ├── _components.scss
    ├── _typography.scss
    └── index.scss
```

## Contributing

Who is for this? I would love for you to contribute to Angular Authentication! Before you start, please read the [Contributor Guide](https://github.com/nikosanif/angular-authentication/blob/main/CONTRIBUTING.md).

If you have found any bug in the source code or want to _request_ a new feature, you can help by [submitting an issue](https://github.com/nikosanif/angular-authentication/issues/new/choose) at GitHub. Even better, you can fork this repository and [submit a PR](https://github.com/nikosanif/angular-authentication/compare) with the fix or the new feature description.

## Support

- Star this [repository](https://github.com/nikosanif/angular-authentication) ⭐️
- Help it spread to a wider audience: [Tweet](https://twitter.com/intent/tweet?text=An%20Angular%20application%20that%20demonstrates%20best%20practices%20for%20user%20authentication.%0A%0A%40nikosanif%20%0A%F0%9F%94%97%20https%3A%2F%2Fgithub.com%2Fnikosanif%2Fangular-authentication%0A%0A&hashtags=Angular,NgRx,TaigaUI,tailwindcss,ngAuth)

### Author: Nikos Anifantis ✍️

- Fullstack Software Engineer - I’m currently working on Angular & Node.js application development.
- I write stuff at [dev.to/nikosanif](https://dev.to/nikosanif) and [nikosanif.medium.com](https://nikosanif.medium.com/)
- How to reach me: [![Twitter](https://img.shields.io/twitter/url/https/twitter.com/nikosanif.svg?style=social&label=Follow%20nikosanif)](https://twitter.com/nikosanif)
  [![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?style=social&style=flat&logo=linkedin&labelColor=blue&label=Connect%20Nikos%20Anifantis)](https://www.linkedin.com/in/nikosanifantis/)

## License

Feel free to use this repository, but **please star and put a reference to this repository.** :pray: :heart:

[MIT](https://opensource.org/licenses/MIT)
