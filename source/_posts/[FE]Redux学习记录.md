

### `isCrushed()` 函数

这个函数是为了检测用了压缩插件。压缩插件应该在生产环境使用，在开发环境使用会降低编译速度。

由于压缩插件会精简函数名，此函数就利用这点来检测。

```javascript
/*
 * This is a dummy function to check if the function name has been altered by minification.
 * If the function has been minified and NODE_ENV !== 'production', warn the user.
 */
function isCrushed() {}

if (
  process.env.NODE_ENV !== 'production' &&
  typeof isCrushed.name === 'string' &&
  isCrushed.name !== 'isCrushed'
) {
  warning(
    'You are currently using minified code outside of NODE_ENV === "production". ' +
      'This means that you are running a slower development build of Redux. ' +
      'You can use loose-envify (https://github.com/zertosh/loose-envify) for browserify ' +
      'or setting mode to production in webpack (https://webpack.js.org/configuration/mode/) ' +
      'to ensure you have the correct code for your production build.'
  )
}
```

`warning()` 函数

在控制台打印警告信息

```typescript
/**
 * Prints a warning in the console if it exists.
 *
 * @param message The warning message.
 */
export default function warning(message: string): void {
  /* eslint-disable no-console */
  if (typeof console !== 'undefined' && typeof console.error === 'function') {
    console.error(message)
  }
  /* eslint-enable no-console */
  try {
    // This error was thrown as a convenience so that if you enable
    // "break on all exceptions" in your console,
    // it would pause the execution at this line.
    throw new Error(message)
  } catch (e) {} // eslint-disable-line no-empty
}

```

