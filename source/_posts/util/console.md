---
title: JavaScript console方法
tags: [JavaScript]
categories: [JavaScript]
description: JavaScript console方法
date: 2023-09-06
---

# JavaScript console 方法

### 1. table()

![Alt text](../../images/util/image.png)

### 2. assert()

console.assert() 非常适合调试目的，它接收断言，并在断言为 false 时向控制台写入错误信息。但如果是 true ，则不会发生任何事情:

![Alt text](../../images/util/image-1.png)

### 3. trace()

console.trace() 可以帮助您在调用它的位置输出当前堆栈跟踪。例如

![Alt text](../../images/util/image-2.png)

### 4. error()

![Alt text](../../images/util/image-3.png)

### 5. warn()

![Alt text](../../images/util/image-4.png)

### 6. count() 和 countReset()

![Alt text](../../images/util/image-5.png)

![Alt text](../../images/util/image-6.png)

> countReset() 方法将标签的计数设回零。

![Alt text](../../images/util/image-7.png)

### 7. time(), timeEnd(), and timeLog()

![Alt text](../../images/util/image-8.png)

### 8. clear()

console.clear() 通过清除日志来清除控制台中的杂乱信息。

### 9. group(), groupCollapsed(), and groupEnd()

![Alt text](../../images/util/image-9.png)

![Alt text](../../images/util/image-10.png)

### 10. dir()

![Alt text](../../images/util/image-11.png)
