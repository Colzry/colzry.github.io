---
title: "CSS高度塌陷"
description: "CSS高度塌陷"
keywords: "CSS"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 前端
tags:
  - CSS
---

# CSS高度塌陷

```css
.clearfix::before,
.clearfix::after {
  content: '';
  display: table;
  clear: both;
}
```