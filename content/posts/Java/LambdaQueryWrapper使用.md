---
title: "LambdaQueryWrapper使用"
description: "LambdaQueryWrapper使用"
keywords: "Java"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Java
tags:
  - Java
---

```java
// eq用法
LambdaQueryWrapper<News> queryWrapper = new LambdaQueryWrapper<>();
queryWrapper.eq(News::getNid, nid);
News news = newsService.getOne(queryWrapper);
        
// select用法
LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
queryWrapper.eq(User::getUid, uid).select(User::getUid, User::getUsername, User::getIsVip);
User user = userService.getOne(queryWrapper);


// and用法
Page<User> userPage = new Page<>(pageParam.getPage(), pageParam.getPageSize());
LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
queryWrapper.eq(User::getType, 1).and(u -> u.eq(User::getStatus, 0));
Page<User> page = userService.page(userPage, queryWrapper);

```

![](https://img-blog.csdnimg.cn/20210518172357678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FsencxOTkw,size_16,color_FFFFFF,t_70#pic_center#crop=0&crop=0&crop=1&crop=1&id=I5OQR&originHeight=1380&originWidth=1314&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
