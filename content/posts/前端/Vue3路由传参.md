---
title: "Vue3路由传参"
description: "Vue3路由传参"
keywords: "路由"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 前端
tags:
  - 路由
---

# Vue3路由传参

## 方法1：页面刷新数据不会丢失
> 路由

```javascrip
export default {
    path: '/product/:productId',
    name: 'product',
    component: Product
}
```

> 传入方

```javascript
import router from "@/router/index.js";

router.push("/product/" + productId)
```

> 接收方

```javascript
import router from "@/router/index.js";

let productId = router.currentRoute.value.params.productId
```

## 方法2：使用path来匹配路由，通过query来传递参数。这种情况下query传递的参数会显示在地址栏中url?id=‘传值’
> 路由

```javascrip
export default {
    path: '/product',
    name: 'product',
    component: Product
}
```

> 传入方

```javascript
import router from "@/router/index.js";

router.push({
    path: '/product',
    query: {
        productId: productId
    }
})
```

> 接收方

```javascript
import router from "@/router/index.js";

let productId = router.currentRoute.value.query.productId
```