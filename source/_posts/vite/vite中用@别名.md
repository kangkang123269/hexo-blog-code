---
title: vite中配置路径别名
tags: [前端,项目,Vue,vite]
categories: [Vue3]
description: 配置路径别名@代表src路径
date: 2022-07-26
cover: https://urlify.cn/3YRb2e
---

# vite 中使用 @ ，配置路径别名

vite 加 vue3项目中报错`Cannot find module 'XXXXXX ’ or its corresponding type declarations`

我们只需要在以下几个文件里面配置：

**vite.config.ts**: 

注意这里用了node的path模块需要执行`npm i --save-dev @types/node`命令

~~~ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
const path = require('path');

export default defineConfig({
  plugins: [vue()],
  define: {
    'process.env': {},
  },
  resolve: {
    // 配置路径别名
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
~~~


**tsconfig.json**：

配置baseUrl，paths

~~~json
{
  "compilerOptions": {
    "target": "esnext",
    "useDefineForClassFields": true,
    "module": "esnext",
    "moduleResolution": "node",
    "strict": true,
    "jsx": "preserve",
    "sourceMap": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "lib": ["esnext", "dom"],
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
}
~~~

大工告成啦！接下来可以用@别名代表src的绝对路径

