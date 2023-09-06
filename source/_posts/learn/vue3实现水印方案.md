---
title: vue3实现水印方案
tags: [个人学习]
categories: [个人学习]
description: vue3实现水印方案
date: 2023-09-06
---

# vue3 实现水印方案

- 生成 canvas 图片

```js
import { computed } from "vue";
export default function useWatermarkBg(props) {
  return computed(() => {
    // 创建一个 canvas
    const canvas = document.createElement("canvas");
    const devicePixelRatio = window.devicePixelRatio || 1;
    // 设置字体大小
    const fontSize = props.fontSize * devicePixelRatio;
    const font = fontSize + "px serif";
    const ctx = canvas.getContext("2d");
    // 获取文字宽度
    ctx.font = font;
    const { width } = ctx.measureText(props.text);
    const canvasSize = Math.max(100, width) + props.gap * devicePixelRatio;
    canvas.width = canvasSize;
    canvas.height = canvasSize;
    ctx.translate(canvas.width / 2, canvas.height / 2);
    // 旋转 45 度让文字变倾斜
    ctx.rotate((Math.PI / 180) * -45);
    ctx.fillStyle = "rgba(0, 0, 0, 0.3)";
    ctx.font = font;
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    // 将文字画出来
    ctx.fillText(props.text, 0, 0);
    return {
      base64: canvas.toDataURL(),
      size: canvasSize,
      styleSize: canvasSize / devicePixelRatio,
    };
  });
}
```

- 用 MutationObserver api 监听删除和修改属性，重新生成水印

```html
<template>
  <div class="watermark-container" ref="parentRef">
    <slot></slot>
    <!-- 我们要做的就是在这里添加一个 div，填充满整个区域，设置水印背景并且重复 -->
  </div>
</template>

<script setup>
import { ref, watchEffect, onMounted, onUnmounted } from "vue";
import useWatermarkBg from "./useWatermarkBg";
// 定义一些基本的属性（ 如果说你想开发的更加完善，可以加入更多的属性来适应你的要求 ）
const props = defineProps({
  text: {
    // 传入水印的文本
    type: String,
    required: true,
    default: "watermark",
  },
  fontSize: {
    // 字体的大小
    type: Number,
    default: 40,
  },
  gap: {
    // 水印重复的间隔
    type: Number,
    default: 20,
  },
});

// 将属性传递进去就返回个创建好的对象
// useWatermarkBg 函数用来创建一个 canvas 图片
const bg = useWatermarkBg(props);
const parentRef = ref(null);
const flag = ref(0); // 声明一个依赖
// 将 div 保存在外部因为要判断节点时使用
let div;

watchEffect(() => {
  flag.value; // 将依赖放在 watchEffect 里
  if (!parentRef.value) {
    return;
  }
  // 判断之前的节点是否有内容，如果有的话删除
  if (div) {
    div.remove();
  }
  const { base64, styleSize } = bg.value;
  div = document.createElement("div");
  div.style.backgroundImage = `url(${base64})`;
  div.style.backgroundSize = `${styleSize}px ${styleSize}px`;
  div.style.backgroundRepeat = "repeat";
  div.style.inset = 0;
  div.style.zIndex = 9999;
  div.style.position = "absolute";
  div.style.pointerEvents = "none";
  parentRef.value.appendChild(div);
});

let ob;
onMounted(() => {
  // 在 onMounted 里边创建一个 MutationObserver 来进行监控
  // 一旦某个东西有变化就会运行这个回调函数
  ob = new MutationObserver((records) => {
    // 并把变化记录下来传递给我们
    console.log("records >>> ", records);
    for (const record of records) {
      // 如果有节点被删除，循环一下判断是否有水印的节点
      for (const dom of record.removedNodes) {
        if (dom === div) {
          console.log("水印被删除");
          flag.value++; // 删除节点的时候更新依赖
          return;
        }
      }
      // 如果有节点被修改，判断一下是否是水印的节点
      if (record.target === div) {
        console.log("属性被修改");
        flag.value++; // 修改属性的时候更新依赖
        return;
      }
    }
  });
  // 创建好监听器之后，告诉监听器需要监听的元素
  ob.observe(parentRef.value, {
    // 监听的时候需要加一些配置
    childList: true, // 元素内容有没有发生变化
    attributes: true, // 元素本身的属性有没有发生变化
    subtree: true, // 告诉它监控的是整个子树，就是包含整个子元素
  });
});

// 在组件卸载的时候取消监听
onUnmounted(() => {
  ob && ob.disconnect(); // 取消监听
  div = null; // 因为 div 是全局变量在写在的时候值为空
});
</script>
```

- 使用

```html
<template>
  <Watermark text="版权不能侵犯">
    <div>内容</div>
  </Watermark>
</template>

<script>
import Watermark from "./components/Watermark.vue";

export default {
  name: "App",
  components: {
    Watermark,
  },
};
</script>
```
