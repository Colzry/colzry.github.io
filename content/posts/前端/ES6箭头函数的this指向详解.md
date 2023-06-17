---
title: "ES6箭头函数this指向详解"
description: "ES6箭头函数this指向详解"
keywords: "ES6"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 前端
tags:
  - ES6
---

箭头函数体内的this对象，就是**定义该函数时所在的作用域指向的对象**，而不是**使用时**所在的作用域指向的对象。
> **举例**

```javascript
var name = 'window'; // 其实是window.name = 'window'

var A = {
   name: 'A',
   sayHello: function(){
      console.log(this.name)
   }
}

A.sayHello();// 输出A

var B = {
  name: 'B'
}

A.sayHello.call(B);//输出B

A.sayHello.call();//不传参数指向全局window对象，输出window.name也就是window
```
```javascript
var name = 'window'; 

var A = {
   name: 'A',
   sayHello: () => {
      console.log(this.name)
   }
}

A.sayHello();// 还是以为输出A ? 错啦，其实输出的是window
```
```javascript
var name = 'window'; 

var A = {
   name: 'A',
   sayHello: function(){
      var s = () => console.log(this.name) // 存在函数上下文
      return s//返回箭头函数s
   }
}

var sayHello = A.sayHello();
sayHello();// 输出A 

var B = {
   name: 'B';
}

sayHello.call(B); //还是A
sayHello.call(); //还是A
```
