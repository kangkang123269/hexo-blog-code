---
title: github项目学习方法
tags: [github]
categories: [github]
description: github项目学习方法
date: 2024-05-31
cover: https://is.gd/ISMSi4
---

# 学习 github 项目的方法

### 1、判断是否移动端设备

```js
function isMobile() {
  const userAgentInfo = navigator.userAgent;
  const Agents = [
    "Android",
    "iPhone",
    "SymbianOS",
    "Windows Phone",
    "iPad",
    "iPod",
  ];
  let flag = false;
  for (let v = 0; v < Agents.length; v++) {
    if (userAgentInfo.indexOf(Agents[v]) > 0) {
      flag = true;
      break;
    }
  }
  const w = document.body && document.body.clientWidth;

  if (w > 960) {
    return false;
  } else if (w < 480) {
    return true;
  } else {
    return flag;
  }
}
```
