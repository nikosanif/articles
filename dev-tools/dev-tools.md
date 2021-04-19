# Awesome Dev Tools To Automate Code Tasks 🔥

## Introduction :thinking:

Automation is awesome! It allows us to increase our productivity and achieve more in shorter amount of time. Thus, this article will teach you step-by-step how to install and use tools that automate processes which leave our limited attention span to focus on solving real challenges!

More specifically, **we are going to learn how to**:

- Format automatically our source code, following rules with unique style
- Build meaningful commit messages
- Take advantage of the power of Git Hooks
- Force our repository to accept specific structure of commits
- Ensure that source code will be committed properly formatted
- Automate new releases, generating versions, changelogs, Git tags, etc.

> This article refers to any Git repository with `Node.js`, including `Angular`, `React.JS`, etc.

## Prerequisites :zap:

- **[Prettier](https://prettier.io/)** constitutes an opinionated code formatter which supports many languages and integrations with most code editors. It allow us to format automatically our code on save saving time and making sure our code have only one, unique and clean format style.

- **[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)** is a lightweight convention on top of commit messages, providing an easy set of rules for creating an explicit commit history; which makes it easier to write automated tools on top of.

  ```
  <type>[optional scope]: <description>

  [optional body]

  [optional footer]
  ```

- **[Husky](https://github.com/typicode/husky#readme)** is a library that allows us to run specific commands and scripts at time of committing and pushing our code using [Git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).

- **[Standard Version](https://github.com/conventional-changelog/standard-version#readme)** is a utility library for versioning using [semver](https://semver.org/) and CHANGELOG generation based on conventional commits of your repository.

In this tutorial, we will also use [commitlint](https://github.com/conventional-changelog/commitlint) and [pretty-quick](https://github.com/azz/pretty-quick#readme) libs to check if your commit messages meet the conventional commit format and run prettier on changed files respectively.

## Install Prettier :hibiscus:

First of all we are going to install Prettier in our the project.

```sh
$ npm i -D prettier
```

Now let's add some configuration files that specify rules and ignore files from prettier formatting.

In your project at root directory create two files with names `.prettierrc` and `.prettierignore`.

```sh
# .prettierrc

{
  "useTabs": false,
  "printWidth": 80,
  "tabWidth": 2,
  "singleQuote": true,
  "trailingComma": "all",
  "semi": true
}
```

```sh
# .prettierignore

/dist
/coverage
```

Last step is to install Prettier extension at VS Code. Open "Quick Open" (`CMD`/`CTRL` + `P`), paste the following command, and press enter.

```sh
ext install esbenp.prettier-vscode
```

Great, now try to write some code (without :bug: :laughing:), save your file and let the magic begin!

## Install Husky :dog2:

First, to install Husky in your project, you have to run the following command:

```sh
$ npm i -D husky
```

Then, run the command below in order to initialize the configuration files.

```sh
$ npx husky install
```

It will generate a new folder at root directory as depicted below:

![husky-install](./assets/husky-install.png)

In order to ensure that husky is going to be initialized each time that you install your project, add the following `postinstall` script at your `package.json` file.

```json
// package.json

{
  "name": "awesome-project",
  "version": "0.0.0",
  "scripts": {
    "postinstall": "husky install"
    // ...
  }
  // ...
}
```

## Setup Git Hooks :twisted_rightwards_arrows:

Awesome, you made it so far! Let's combine them together and setup our project. :smirk:

It's time to add some scripts for:

- commit message validation that meet conventional commit format
- format with prettier the changed files before committing new code

Install all the required _dev-dependencies_, by running:

```sh
$ npm i -D @commitlint/cli @commitlint/config-conventional pretty-quick
```

Open your project and create a new file with name `commitlint.config.js` under the root folder. Then copy/paste commitlint configuration in your new file as depicted below.

```js
// commitlint.config.js

module.exports = {
  extends: ["@commitlint/config-conventional"],
};
```

The next step is to tell Husky what command to execute on which Git Hook action. Create two files under the `.husky` folder with names `commit-msg` and `pre-commit`.

![husky-hooks](./assets/husky-hooks.png)

In order to validate commit messages on commit-msg Git Hook, we use **commitlint** command. This action allows us to verify if the new commit message meets the requirements of Conventional Commits specification.

If the commit message does not follow the rules, it will not allow us to commit our new code.

```sh
# commit-msg

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no-install commitlint --edit $1
```

The second action that we want to achieve is to format our code automatically before we commit our code to the repository. We use **pretty-quick** command to format our code depending on the defined configuration that we have specified.

Also, we use `--staged` command flag to format only those files that are changed in our new commit.

```sh
# pre-commit

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no-install pretty-quick --staged
```

**_[Optional]_** As final step, I recommend to install the Conventional Commits official plugin in your VS Code. It will help you to write structured commit messages through a step-by-step form without having to memorize all the rules.

Open "Quick Open" (`CMD`/`CTRL` + `P`), paste the following command, and press enter.

```
ext install vivaxy.vscode-conventional-commits
```

Once you have installed the plugin, open "Source Control" panel and click on the "circle" icon button at the top. A panel will appear and will guide you to write your commit message.

![vs-code-plugin](./assets/vs-code-plugin.png)

> :bookmark: **Tip**
>
> Although it may seem like a tedious process at first, it will help a lot and over time you will get used to it very easily writing well structured commit messages. :wink:

## Install Standard Version :package:

Before we proceed with the installation, let's do a small recap. At the previous step, we saw how to format commit messages and how to enforce validation of them with Git Hooks.

So, our commits look like this:

```
- feat(ui): add avatar component for users
- style(ui): add roundness at avatar component
- chore: update lodash to 4.17.21
- feat(layout): add navigation links at header
- fix(auth): pass valid token at authentication process
```

Although it's totally the best practice to follow specific message convention for your commits, it's also **the only requirement** to be able to fully use the `standard-version` package.

In brief, `standard-version` undertakes to:

- Change versions at `package.json` and `package-lock.json` files
- Track changes from your commits and generate/update automatically `CHANGELOG.md`
- Commit changed files
- Create appropriate tags with versions at your repository

To install standard-version run:

```sh
$ npm i -D standard-version
```

Then, let's create three scripts at our project. According to [semver](https://semver.org/), we create one script for each version number (MAJOR.MINOR.PATCH).

Update your `package.json` file like:

```json
// package.json

{
  "name": "awesome-project",
  "version": "0.0.0",
  "scripts": {
    // ...
    "release": "standard-version --release-as major",
    "release:minor": "standard-version --release-as minor",
    "release:patch": "standard-version --release-as patch"
  }
  // ...
}
```

Once you write some code and create 2-3 commits, try to run the following command:

```sh
$ npm run release -- --dry-run
```

> We use the `--dry-run` command flag to demonstrate what commands would be run, without committing to git or updating files.
>
> During your official release just run: `npm run release`

You will get an output similar to this:

```
✔ bumping version in package.json from 0.0.0 to 1.0.0
✔ bumping version in package-lock.json from 0.0.0 to 1.0.0
✔ outputting changes to CHANGELOG.md

---
### Features

* **ui:** add avatar component for users (...)
* **layout:** add navigation links to header (...)

### Fixes

* **auth:** pass valid token at authentication process (...)
---

✔ committing package-lock.json and package.json and CHANGELOG.md
✔ tagging release v1.0.0
ℹ Run `git push --follow-tags origin master` to publish
```

You have created a new release version for your project. :clap:

> Keep in mind that you have a ton of options to customize according to your needs from the official documentation.

## Conclusion :white_check_mark:

Hooray! We made it to the end! :raised_hands:

I hope you enjoyed this article and boost your projects with some magic.
All aforementioned tools can help you to enrich the endless automation possibilities in an excellent way and make you and your team even more productive!

Please support this article with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).

<sup>_Cover Photo by [rhondak](https://unsplash.com/@rhondak) on [Unsplash](https://unsplash.com/)._</sup>
