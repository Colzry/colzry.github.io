---
title: "GitHub代理加速"
description: "git"
keywords: "git"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Git
tags:
  - Git
---

项目地址：[https://github.com/hunshcn/gh-proxy](https://github.com/hunshcn/gh-proxy)
## 使用cloudflare免费的代理加速
网址：[https://workers.cloudflare.com](https://workers.cloudflare.com)
先登录或注册
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666409502370-3063276f-15dc-4ab2-b9e6-aedccbe0192b.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=425&id=u371deac8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=638&originWidth=1547&originalType=binary&ratio=1&rotation=0&showTitle=false&size=79869&status=done&style=none&taskId=u9f274855-c6de-4d6e-8cc2-62ec954b4ed&title=&width=1031.3333333333333)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666409546711-a63a2108-6f75-4654-81cb-4b88a27afcf7.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=611&id=uc862ad96&margin=%5Bobject%20Object%5D&name=image.png&originHeight=917&originWidth=1288&originalType=binary&ratio=1&rotation=0&showTitle=false&size=99288&status=done&style=none&taskId=u4c1a0b8b-39fc-410f-87b7-e5486591c12&title=&width=858.6666666666666)
将下面的放入左侧的方框中(不需要任何的改动)
```javascript
'use strict'

/**
 * static files (404.html, sw.js, conf.js)
 */
const ASSET_URL = 'https://hunshcn.github.io/gh-proxy/'
// 前缀，如果自定义路由为example.com/gh/*，将PREFIX改为 '/gh/'，注意，少一个杠都会错！
const PREFIX = '/'
// 分支文件使用jsDelivr镜像的开关，0为关闭，默认关闭
const Config = {
    jsdelivr: 0
}

const whiteList = [] // 白名单，路径里面有包含字符的才会通过，e.g. ['/username/']

/** @type {RequestInit} */
const PREFLIGHT_INIT = {
    status: 204,
    headers: new Headers({
        'access-control-allow-origin': '*',
        'access-control-allow-methods': 'GET,POST,PUT,PATCH,TRACE,DELETE,HEAD,OPTIONS',
        'access-control-max-age': '1728000',
    }),
}


const exp1 = /^(?:https?:\/\/)?github\.com\/.+?\/.+?\/(?:releases|archive)\/.*$/i
const exp2 = /^(?:https?:\/\/)?github\.com\/.+?\/.+?\/(?:blob|raw)\/.*$/i
const exp3 = /^(?:https?:\/\/)?github\.com\/.+?\/.+?\/(?:info|git-).*$/i
const exp4 = /^(?:https?:\/\/)?raw\.(?:githubusercontent|github)\.com\/.+?\/.+?\/.+?\/.+$/i
const exp5 = /^(?:https?:\/\/)?gist\.(?:githubusercontent|github)\.com\/.+?\/.+?\/.+$/i
const exp6 = /^(?:https?:\/\/)?github\.com\/.+?\/.+?\/tags.*$/i

/**
 * @param {any} body
 * @param {number} status
 * @param {Object<string, string>} headers
 */
function makeRes(body, status = 200, headers = {}) {
    headers['access-control-allow-origin'] = '*'
    return new Response(body, {status, headers})
}


/**
 * @param {string} urlStr
 */
function newUrl(urlStr) {
    try {
        return new URL(urlStr)
    } catch (err) {
        return null
    }
}


addEventListener('fetch', e => {
    const ret = fetchHandler(e)
        .catch(err => makeRes('cfworker error:\n' + err.stack, 502))
    e.respondWith(ret)
})


function checkUrl(u) {
    for (let i of [exp1, exp2, exp3, exp4, exp5, exp6]) {
        if (u.search(i) === 0) {
            return true
        }
    }
    return false
}

/**
 * @param {FetchEvent} e
 */
async function fetchHandler(e) {
    const req = e.request
    const urlStr = req.url
    const urlObj = new URL(urlStr)
    let path = urlObj.searchParams.get('q')
    if (path) {
        return Response.redirect('https://' + urlObj.host + PREFIX + path, 301)
    }
    // cfworker 会把路径中的 `//` 合并成 `/`
    path = urlObj.href.substr(urlObj.origin.length + PREFIX.length).replace(/^https?:\/+/, 'https://')
    if (path.search(exp1) === 0 || path.search(exp5) === 0 || path.search(exp6) === 0 || path.search(exp3) === 0 || path.search(exp4) === 0) {
        return httpHandler(req, path)
    } else if (path.search(exp2) === 0) {
        if (Config.jsdelivr) {
            const newUrl = path.replace('/blob/', '@').replace(/^(?:https?:\/\/)?github\.com/, 'https://cdn.jsdelivr.net/gh')
            return Response.redirect(newUrl, 302)
        } else {
            path = path.replace('/blob/', '/raw/')
            return httpHandler(req, path)
        }
    } else if (path.search(exp4) === 0) {
        const newUrl = path.replace(/(?<=com\/.+?\/.+?)\/(.+?\/)/, '@$1').replace(/^(?:https?:\/\/)?raw\.(?:githubusercontent|github)\.com/, 'https://cdn.jsdelivr.net/gh')
        return Response.redirect(newUrl, 302)
    } else {
        return fetch(ASSET_URL + path)
    }
}


/**
 * @param {Request} req
 * @param {string} pathname
 */
function httpHandler(req, pathname) {
    const reqHdrRaw = req.headers

    // preflight
    if (req.method === 'OPTIONS' &&
        reqHdrRaw.has('access-control-request-headers')
    ) {
        return new Response(null, PREFLIGHT_INIT)
    }

    const reqHdrNew = new Headers(reqHdrRaw)

    let urlStr = pathname
    let flag = !Boolean(whiteList.length)
    for (let i of whiteList) {
        if (urlStr.includes(i)) {
            flag = true
            break
        }
    }
    if (!flag) {
        return new Response("blocked", {status: 403})
    }
    if (urlStr.startsWith('github')) {
        urlStr = 'https://' + urlStr
    }
    const urlObj = newUrl(urlStr)

    /** @type {RequestInit} */
    const reqInit = {
        method: req.method,
        headers: reqHdrNew,
        redirect: 'manual',
        body: req.body
    }
    return proxy(urlObj, reqInit)
}


/**
 *
 * @param {URL} urlObj
 * @param {RequestInit} reqInit
 */
async function proxy(urlObj, reqInit) {
    const res = await fetch(urlObj.href, reqInit)
    const resHdrOld = res.headers
    const resHdrNew = new Headers(resHdrOld)

    const status = res.status

    if (resHdrNew.has('location')) {
        let _location = resHdrNew.get('location')
        if (checkUrl(_location))
            resHdrNew.set('location', PREFIX + _location)
        else {
            reqInit.redirect = 'follow'
            return proxy(newUrl(_location), reqInit)
        }
    }
    resHdrNew.set('access-control-expose-headers', '*')
    resHdrNew.set('access-control-allow-origin', '*')

    resHdrNew.delete('content-security-policy')
    resHdrNew.delete('content-security-policy-report-only')
    resHdrNew.delete('clear-site-data')

    return new Response(res.body, {
        status,
        headers: resHdrNew,
    })
}

```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666409680492-0ff4eb33-d199-466a-bd6a-b05a58d0e9b7.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=329&id=u7a771a7c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=494&originWidth=2221&originalType=binary&ratio=1&rotation=0&showTitle=false&size=97131&status=done&style=none&taskId=u5eeb4080-f2cf-4a6b-b823-f85519d250c&title=&width=1480.6666666666667)
点击`保存并部署`后可以点击`发送`测试是否成功
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666409787668-ccdb9746-bb25-46d9-9534-e50ec2a6c1ce.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=179&id=u5bfc5733&margin=%5Bobject%20Object%5D&name=image.png&originHeight=269&originWidth=908&originalType=binary&ratio=1&rotation=0&showTitle=false&size=66587&status=done&style=none&taskId=u6790f58b-0146-4dfc-a16f-d0270e4ffc8&title=&width=605.3333333333334)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666409773334-c56c9f01-4022-4ade-8049-32fed5750c25.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=421&id=u6be53c86&margin=%5Bobject%20Object%5D&name=image.png&originHeight=631&originWidth=1322&originalType=binary&ratio=1&rotation=0&showTitle=false&size=106792&status=done&style=none&taskId=u751a91ea-42c6-44ce-b300-06b7f1e2ee4&title=&width=881.3333333333334)

目前国内无法访问 `*.workers.dev`，需要`cloudflare`托管的域名反代进行`CNAME`解析
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666410008929-f6068f61-1c2c-4b9a-919b-f1703d180e27.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=393&id=u2fd9ff97&margin=%5Bobject%20Object%5D&name=image.png&originHeight=589&originWidth=1557&originalType=binary&ratio=1&rotation=0&showTitle=false&size=70185&status=done&style=none&taskId=u4df7f1df-2de2-4091-b560-c7c25986b23&title=&width=1038)
之后点击`Worker路由`-> `添加路由`
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666410033094-f8b638cd-151d-4e43-832f-c77ff10758a7.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=530&id=ue2ea1b5b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=795&originWidth=2199&originalType=binary&ratio=1&rotation=0&showTitle=false&size=146753&status=done&style=none&taskId=u7104bdd6-dff9-4cf5-999d-ab6edd88f2b&title=&width=1466)
按照下图的形式编写进行保存即可
![image.png](https://cdn.nlark.com/yuque/0/2022/png/22223333/1666410088397-854f841a-8743-42d4-9a81-5997990bf750.png#clientId=ub339564c-2536-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=451&id=u9b20526e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=677&originWidth=666&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36733&status=done&style=none&taskId=ubba7dd11-2daa-4ab8-990b-45291d956be&title=&width=444)
之后就能通过自定义的域名进行访问

## 终端使用方法
例如下载 `Releases` 文件
```bash
# 原来的使用方法
wget https://github.com/fatedier/frp/releases/download/v0.44.0/frp_0.44.0_linux_amd64.tar.gz

# 代理使用方法
wget https://gitpy.colzry.tk/https://github.com/fatedier/frp/releases/download/v0.44.0/frp_0.44.0_linux_amd64.tar.gz
```
clone也是如此
