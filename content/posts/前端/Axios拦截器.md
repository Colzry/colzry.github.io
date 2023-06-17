---
title: "Axios拦截器配置"
description: "Axios拦截器配置"
keywords: "Axios"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - 前端
tags:
  - Axios
---

```javascript
import axios from "axios";
import nprogress from "nprogress";
import "nprogress/nprogress.css";
import { Message } from "@arco-design/web-vue";
import { useUserStore } from "@/store/user/index.js";
import router from "@/router/index.js";

const service = axios.create({
    //基础路径
    baseURL: "/api",
    responseType: "json",
    headers: {
        'Content-Type': 'application/json; charset=utf-8'
    },
    //请求不能超过5S
    timeout: 5000,
});

//请求拦截器
service.interceptors.request.use(
  config => {
    // 可以让进度条开始动
    nprogress.start();
    // 需要携带token带给服务器
    const userStore = useUserStore()
    if(userStore.token){
        config.headers.token = userStore.token;
    }
    return config;
  },
  error => {
    // 请求失败提示
    Message.error({content: error})
    return Promise.reject(error)
  });

// 响应拦截器
service.interceptors.response.use(
  response => {
    //进度条结束
        nprogress.done();
        // 如果返回的状态码为200，说明接口请求成功，可以正常拿到数据
        // 否则的话抛出错误
        if (response.status === 200) {
            return Promise.resolve(response.data);
        } else {
            return Promise.reject(response);
        }
  },
  error => {
    const userStore = useUserStore()
        if (error.response.status) {
            switch (error.response.status) {
                case 401:
                    Message.error({content: error.response.data.message})
                    localStorage.removeItem("token")
                    userStore.token = ''
                    router.replace("/login")
                    break
                case 403:
                    Message.error({content: error.response.data.message})
                    break
                case 404:
                    Message.error({content: '请求的资源不存在!'})
                    break
                default:
                    Message.error({content: '错误的请求'})
            }
        }
        return Promise.reject(error.response)
  });

export default service;
```

```javascript
import request from "@/request";

export function login(loginInfo) {
    return request({
        url: '/login',
        method: 'post',
        data: loginInfo
    })
}
```
