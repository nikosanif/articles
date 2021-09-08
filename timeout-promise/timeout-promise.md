# Create Promises with Timeout Error in Typescript

<sup>_Cover Photo by [Immo Wegmann](https://unsplash.com/@macroman?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/)._</sup>

## The Problem :thinking:

Sometimes we create promises that take too long to finish and we don't want to wait. For example, if we want to make a request and the response takes 20 seconds to finish and for any reason we may want to throw an error in the first 5 seconds and proceed to the next code block.

In this article, we will see how to implement a promise which throws an error after a specific time span. It supports typings with the power of Typescript and accepts a custom error to be thrown when the time comes.

## Implementation :rocket:

First of all, we create a new promise that rejects an error after a time span in milliseconds. The return type of the timeout promise is `never` which represents the type of values that never occur. For instance, `never` is the return type for a function expression or an arrow function expression that always throws an exception or one that never returns.

```ts
// create a promise that rejects in milliseconds
const timeout = new Promise<never>((_, reject) => {
  setTimeout(() => {
    reject(timeout);
  }, ms);
});
```

The next step is to use [Promise.race()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) which returns a `Promise` that fulfills or rejects as soon as one of the promises in an iterable fulfills or rejects, with the value or reason from that promise. Here you can see that we have used `<T>` generic type that it will be defined in our final function.

```ts
Promise.race<T>([anyPromise, anyOtherPromise]);
```

Great, we saw all code blocks, let's see the full implementation now below:

```ts
function promiseWithTimeout<T>(
  promise: Promise<T>,
  ms: number,
  timeoutError = new Error('Promise timed out')
): Promise<T> {
  // create a promise that rejects in milliseconds
  const timeout = new Promise<never>((_, reject) => {
    setTimeout(() => {
      reject(timeout);
    }, ms);
  });

  // returns a race between timeout and the passed promise
  return Promise.race<T>([promise, timeout]);
}
```

As you can see, we have created a function that accepts the following arguments:

- `promise`: our actual promise
- `ms`: the maximum time in milliseconds which we want to wait
- `timeoutError`: (optional) we may pass a custom error to throw after timeout

**You can find the final source code in stackblitz:**

<!-- custom tag to embed stackblitz -->
<!-- https://stackblitz.com/edit/promise-with-timeout-rwt8ye -->

{% stackblitz promise-with-timeout-rwt8ye %}

## Conclusion :white_check_mark:

Hooray, we made it to the end! I hope you enjoyed this post and do not wait for time consuming promises any more :laughing:.

Please support this article with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).
