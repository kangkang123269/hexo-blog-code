---
title: nextTick 原理
tags: [vue]
categories: [vue]
description: nextTick 原理
date: 2023-09-06
---
# nextTick 原理

Vue 的`nextTick`是一个非常重要的 API，它允许你延迟一段代码的执行直到下一次 DOM 更新周期。这对于在数据改变后立即操作 DOM 非常有用。

关于其原理，Vue 使用了一个异步队列来控制所有观察者（watchers）的更新。当某个观察者被触发时，它会被添加到这个异步队列中。然后，在下一个 tick 时，Vue 会清空整个队列并执行所有存储在其中的观察者。

为了实现这种异步机制，Vue 尝试使用原生 Promise、MutationObserver 和 setImmediate 等最新 APIs。只有当环境不支持这些 APIs 时才会回退到 setTimeout。

先用 Promise.then()方法实现一下:

```js
let callbacks = [];
let pending = false;

function flushCallbacks() {
  pending = false;
  const copies = callbacks.slice(0);
  callbacks.length = 0;
  for (let i = 0; i < copies.length; i++) {
    copies[i]();
  }
}

function nextTick(cb) {
  callbacks.push(() => {
    if (cb) cb();
  });

  if (!pending) {
    pending = true;

    if (typeof Promise !== "undefined") {
      // 如果环境支持Promise
      Promise.resolve().then(flushCallbacks);
    } else {
      // 如果环境不支持Promise，则回退到setTimeout
      setTimeout(flushCallbacks, 0);
    }
  }
}
```

具体来说：

1. 首选 Promise.then()方法。
2. 如果环境不支持 Promise，则选择 MutationObserver。
3. 如果还不支持 MutationObserver，则选择 setImmediate。
4. 最后如果都不支持，则使用 setTimeout。

以上就是`nextTick`的基本原理：通过异步队列确保代码在下一次 DOM 更新周期之后运行，并通过多种方式尝试实现该功能以适应各种环境。

完整按照上述顺序实现一下：

```js
let callbacks = [];
let pending = false;

function flushCallbacks() {
  pending = false;
  const copies = callbacks.slice(0);
  callbacks.length = 0;
  for (let i = 0; i < copies.length; i++) {
    copies[i]();
  }
}

// 异步方法选择器
let timerFunc;

if (typeof Promise !== "undefined") {
  // 如果环境支持Promise
  const p = Promise.resolve();
  timerFunc = () => {
    p.then(flushCallbacks);
  };
} else if (typeof MutationObserver !== "undefined") {
  // 如果环境不支持Promise，但支持MutationObserver
  let counter = 1;
  const observer = new MutationObserver(flushCallbacks);
  const textNode = document.createTextNode(String(counter));

  observer.observe(textNode, {
    characterData: true,
  });

  timerFunc = () => {
    counter += 1;
    textNode.data = String(counter % 2);
  };
} else if (typeof setImmediate !== "undefined") {
  // 如果环境不支持Promise和MutationObserver，但支持setImmediate
  timerFunc = () => setImmediate(flushCallbacks);
} else {
  // 最后如果都不支持，则使用setTimeout
  timerFunc = () => setTimeout(flushCallbacks, 0);
}

function nextTick(cb) {
  callbacks.push(() => cb && cb());

  if (!pending) {
    pending = true;
    timerFunc();
  }
}
```

> 这个实现更接近 Vue.js 中真正的 nextTick。注意在使用 Mutation Observer 时，我们观察了一个文本节点并通过改变它来触发回调。
