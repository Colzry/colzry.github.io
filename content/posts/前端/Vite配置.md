---
title: "Vite配置"
description: "Vite配置"
keywords: "Vite"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 前端
tags:
  - Vite
---

## vite.config.js
```javascript
import { defineConfig } from "vite";
import { resolve } from "path";
import vue from '@vitejs/plugin-vue';
function pathResolve(dir) {
  return resolve(__dirname, ".", dir);
}

export default defineConfig({
    base: "",
    plugins:[vue()],
    resolve: {
        alias: {
          "@": pathResolve("src"),
        }
    },
    optimizeDeps: {
        include: ['axios'],
    },
    build: {
      target: 'modules',
      outDir: 'dist',
      assetsDir: 'assets',
      minify: 'terser' // 混淆器
    },
    server: {
        cors: true,
        open: true,
        proxy: {
          '/api': {
              target: 'http://192.168.99.223:3000',   //代理接口
              changeOrigin: true,
              rewrite: (path) => path.replace(/^\/api/, '')
          }
        }
    }
});
```
## jsconfig.json
```json
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "exclude": ["node_modules", "dist"]
}
```
