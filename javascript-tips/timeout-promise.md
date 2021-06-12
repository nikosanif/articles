# Create Promises with Timeout Error in Typescript

<sup>_Cover Photo by [TODO](TODO) on [Unsplash](https://unsplash.com/)._</sup>

## The Problem :thinking:

## Implementation :rocket:

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

## Conclusion :white_check_mark:

Hooray! We made it to the end :raised_hands:! I hope you enjoyed this post and do not wait for long promises any more :laughing:.

Please support this article with your :heart: :unicorn: :bookmark: to help it spread to a wider audience. :pray:

Also, don’t hesitate to contact me if you have any questions leaving here your comments or Twitter DMs [@nikosanif](https://twitter.com/nikosanif).

**You can find the final source code in stackblitz:**

<!-- custom tag to embed stackblitz -->
<!-- https://stackblitz.com/edit/promise-with-timeout-rwt8ye -->

{% stackblitz promise-with-timeout-rwt8ye %}
