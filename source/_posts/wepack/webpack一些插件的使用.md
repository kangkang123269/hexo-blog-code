---
title: Vue3 新特性
tags: [Vue深入]
categories: [Vue深入]
description: 在Vue中使用keep-alive
date: 2022-12-30
cover: http://tny.im/KpBhu
---

# webpack 一些插件的使用

## 性能优化分析 webpack-bundle-analyzer

1. 安装

```
npm install --save-dev webpack-bundle-analyzer
```

2. 在 webpack.config.js 中配置：

```js
const BundleAnalyzerPlugin =
  require("webpack-bundle-analyzer").BundleAnalyzerPlugin;
module.exprots = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: "server",
      analyzerHost: "127.0.0.1",
      analyzerPort: 8889,
      reportFilename: "report.html",
      defaultSizes: "parsed",
      openAnalyzer: true,
      generateStatsFile: false,
      statsFilename: "stats.json",
      statsOptions: null,
      logLevel: "info",
    }),
  ],
};
```

3. 查看线上打包后的效果，在 page.json 的 scripts 添加脚本

```json
"analyz": "NODE_ENV=production npm_config_report=true npm run build"
```

> [https://zhuanlan.zhihu.com/p/31541721](https://zhuanlan.zhihu.com/p/31541721)
