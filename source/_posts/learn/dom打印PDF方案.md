---
title: dom转PDF方案
tags: [个人学习]
categories: [个人学习]
description: 记录下dom转PDF方案
date: 2023-09-06
---

# dom 转 PDF 方案

### 全局打印

```js
window.print();
```

### iframe 打开 dom 元素

```js
function printElement(e) {
  var ifr = document.createElement("iframe");
  ifr.style = "height: 0px; width: 0px; position: absolute";
  document.body.appendChild(ifr);
  ifr.contentDocument.body.appendChild(e.cloneNode(true));
  ifr.contentWindow.print();

  ifr.parentElement.removeChild(ifr);
}
var style = document.createElement("style");
style.innerText = "* { color: red; }";
var img = document.createElement("img");
img.height = 300;
img.width = 300;
img.src = "https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png";
var div = document.createElement("div");
div.innerText = "这是百度网站";
div.appendChild(style);
div.appendChild(img);
printElement(div);
```

### js 库

- jsPdf

```js
// 控制台执行第一段
var jspdfScript = document.createElement("script");
jspdfScript.src = "https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js";
document.body.append(jspdfScript);
var html2canvasScript = document.createElement("script");
html2canvasScript.src =
  "https://html2canvas.hertzen.com/dist/html2canvas.min.js";
document.body.append(html2canvasScript);

// 等 js 加载完成, 控制台执行第二段
// $0 是调试工具选中的元素, 没有使用过的话, 可以直接控制台打印看看 $0 是啥
window.jsPDF = jspdf.jsPDF;
var doc = new jsPDF();
doc.html($0, {
  callback: function (doc) {
    doc.save("sample-document.pdf");
  },
  x: 1,
  y: 1,
  width: 170,
  windowWidth: 1650,
});
```

- jspdf + html2Canvas

```js
// 控制台执行第一段
var jspdfScript = document.createElement("script");
jspdfScript.src = "https://unpkg.com/jspdf@latest/dist/jspdf.umd.min.js";
document.body.append(jspdfScript);
var html2canvasScript = document.createElement("script");
html2canvasScript.src =
  "https://html2canvas.hertzen.com/dist/html2canvas.min.js";
document.body.append(html2canvasScript);

// 等 js 加载完成, 控制台执行第二段
// $0 是调试工具选中的元素, 没有使用过的话, 可以直接控制台打印看看 $0 是啥
window.jsPDF = jspdf.jsPDF;
html2canvas($0).then(function (canvas) {
  var max = { height: 300 - 40 * 2, width: 210 - 15 * 2 };
  var doc = new jsPDF("p", "mm", "a2");
  var height = canvas.height;
  var width = canvas.width;
  var ratio = canvas.height / canvas.width;
  if (height > max.height) {
    // 先调整高
    height = max.height;
    width = height * (1 / ratio);
  }
  if (width > max.width) {
    // 再调整宽
    width = max.width;
    height = width * ratio;
  }
  // 最后宽高都是合适的
  doc.addImage(canvas, "PNG", 15, 40, width, height);
  doc.save("sample-document.pdf");
});
```
