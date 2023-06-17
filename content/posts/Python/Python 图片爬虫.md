---
title: "Python图片爬虫"
description: "Python图片爬虫"
keywords: "爬虫"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
  - 爬虫
---

## 图片爬取1.1

```python
import requests
import os
from lxml import etree

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}


def request(url):
    response = requests.get(url=url, headers=headers)
    # 中文乱码处理
    response.encoding = 'utf-8'
    return response.text

def get_link(url):
    tree = etree.HTML(request(url))
    li_list = tree.xpath('/html/body/div[2]/div[8]/ul/li')
    # print(li_list)

    for li in li_list:
        link = 'https://www.umei.net' + li.xpath('./a/@href')[0]
        file_name = li.xpath('./a/span/text()')[0]
        print('开始下载:' + file_name)
        path = 'D:/Img/'+file_name
        if not os.path.exists(path):
            os.mkdir(path)
        img_src,next_page = get_allSrc(link)
        while next_page != '':
            img_download(path,img_src)
            img_src, next_page = get_allSrc(next_page)


def get_allSrc(url):
    tree = etree.HTML(request(url))
    src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')[0]
    a_list = tree.xpath('/html/body/div[2]/div[12]/a')
    next_page = ''
    for a in a_list:
        if a.xpath('./text()')[0] == '下一页':
            next_page = 'https://www.umei.net' + a.xpath('./@href')[0]

    return src, next_page

def img_download(path,img_src):
    name = img_src.rsplit('/', 1)[1]
    img_path= path + '/' + name
    img_data = requests.get(url=img_src, headers=headers).content
    with open(img_path,'wb') as fp:
        fp.write(img_data)
    print(name, "下载成功")



if __name__ == '__main__':
    url = 'https://www.umei.net/meinvtupian/meinvxiezhen/'

    get_link(url)
```

## 图片爬取1.2

```python
import requests
import os
from lxml import etree

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}


def request(url):
    response = requests.get(url=url, headers=headers)
    # 中文乱码处理
    response.encoding = 'utf-8'
    return response.text

def get_link(url):
    tree = etree.HTML(request(url))
    li_list = tree.xpath('/html/body/div[2]/div[8]/ul/li')
    # print(li_list)

    for li in li_list:
        link = 'https://www.umei.net' + li.xpath('./a/@href')[0]
        file_name = li.xpath('./a/span/text()')[0]
        print('开始下载:' + file_name)
        path = 'D:/Img/'+file_name
        if not os.path.exists(path):
            os.mkdir(path)
        img_src,next_page = get_allSrc(link)
        while next_page != '':
            img_download(path,img_src)
            img_src, next_page = get_allSrc(next_page)


def get_allSrc(url):
    tree = etree.HTML(request(url))
    src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')[0]
    a_list = tree.xpath('/html/body/div[2]/div[12]/a')
    next_page = ''
    for a in a_list:
        if a.xpath('./text()')[0] == '下一页':
            next_page = 'https://www.umei.net' + a.xpath('./@href')[0]

    return src, next_page

def img_download(path,img_src):
    name = img_src.rsplit('/', 1)[1]
    img_path= path + '/' + name
    img_data = requests.get(url=img_src, headers=headers).content
    with open(img_path,'wb') as fp:
        fp.write(img_data)
    print(name, "下载成功")



if __name__ == '__main__':
    url = 'https://www.umei.net/meinvtupian/meinvxiezhen/'
    get_link(url)
    for i in range(146):
        if i >= 2:
            index = f'{i}'
            url = 'https://www.umei.net/meinvtupian/meinvxiezhen/index_' + index + '.htm'
            get_link(url)
```

## 图片爬虫1.3

```python
import requests
import os
from lxml import etree

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}


def request(url):
    response = requests.get(url=url, headers=headers)
    # 中文乱码处理
    response.encoding = 'utf-8'
    return response.text

def get_link(url):
    tree = etree.HTML(request(url))
    li_list = tree.xpath('/html/body/div[2]/div[8]/ul/li')
    # print(li_list)

    for li in li_list:
        link = 'https://www.umei.net' + li.xpath('./a/@href')[0]
        file_name = li.xpath('./a/span/text()')[0]
        print('开始下载:' + file_name)
        path = 'D:/Img/'+file_name
        if not os.path.exists(path):
            os.mkdir(path)
        img_src,next_page = get_allSrc(link)
        while next_page != '':
            img_download(path,img_src)
            img_src, next_page = get_allSrc(next_page)


def get_allSrc(url):
    tree = etree.HTML(request(url))
    a_list = tree.xpath('/html/body/div[2]/div[12]/a')
    next_page = ''

    if len(a_list) == 0:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/img/@src')[0]
    else:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')[0]
        for a in a_list:
            if a.xpath('./text()')[0] == '下一页':
                next_page = 'https://www.umei.net' + a.xpath('./@href')[0]

    return src, next_page

def img_download(path,img_src):
    name = img_src.rsplit('/', 1)[1]
    img_path= path + '/' + name
    img_data = requests.get(url=img_src, headers=headers).content
    with open(img_path,'wb') as fp:
        fp.write(img_data)
    print(name, "下载成功")



if __name__ == '__main__':
    url = 'https://www.umei.net/meinvtupian/meinvxiezhen/'
    get_link(url)
    for i in range(146):
        if i >= 2:
            index = f'{i}'
            url = 'https://www.umei.net/meinvtupian/meinvxiezhen/index_' + index + '.htm'
            get_link(url)
```

## 图片爬虫1.4

```python
import requests
import os
from lxml import etree

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}


def request(url):
    response = requests.get(url=url, headers=headers)
    # 中文乱码处理
    response.encoding = 'utf-8'
    return response.text

def get_link(url):
    tree = etree.HTML(request(url))
    li_list = tree.xpath('/html/body/div[2]/div[8]/ul/li')
    # print(li_list)

    for li in li_list:
        link = 'https://www.umei.net' + li.xpath('./a/@href')[0]
        file_name = li.xpath('./a/span/text()')[0]
        print('开始下载:' + file_name)
        path = 'D:/Img/'+file_name
        if not os.path.exists(path):
            os.mkdir(path)
        img_src,next_page = get_allSrc(link)
        while next_page != '':
            img_download(path,img_src)
            img_src, next_page = get_allSrc(next_page)


def get_allSrc(url):
    tree = etree.HTML(request(url))
    a_list = tree.xpath('//div[@class=\'NewPages\']/a')
    src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')
    next_page = ''

    if len(a_list) == 0 or len(src) == 0:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/img/@src')[0]
    else:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')[0]
        for a in a_list:
            if a.xpath('./text()')[0] == '下一页':
                next_page = 'https://www.umei.net' + a.xpath('./@href')[0]

    return src, next_page

def img_download(path,img_src):
    name = img_src.rsplit('/', 1)[1]
    img_path= path + '/' + name
    img_data = requests.get(url=img_src, headers=headers).content
    with open(img_path,'wb') as fp:
        fp.write(img_data)
    print(name, "下载成功")



if __name__ == '__main__':
    url = 'https://www.umei.net/meinvtupian/meinvxiezhen/'
    get_link(url)
    for i in range(146):
        if i >= 2:
            index = f'{i}'
            url = 'https://www.umei.net/meinvtupian/meinvxiezhen/index_' + index + '.htm'
            get_link(url)
```

## 图片爬虫1.5（异步）

```python
import requests
import asyncio
import aiohttp
import aiofiles
import os
from lxml import etree

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}


def request(url):
    response =  requests.get(url=url, headers=headers)
    # 中文乱码处理
    response.encoding = 'utf-8'
    return response.text

def get_link(url):
    tree = etree.HTML(request(url))
    li_list = tree.xpath('/html/body/div[2]/div[8]/ul/li')
    tasks = []
    for li in li_list:
        link = 'https://www.umei.net' + li.xpath('./a/@href')[0]
        file_name = li.xpath('./a/span/text()')[0]
        print('开始下载:' + file_name)
        path = 'D:/ImgTest'
        if not os.path.exists(path):
            os.mkdir(path)
        img_src,next_page = get_allSrc(link)
        tasks.append(download_img(path, img_src))
        while next_page != '':
            img_src, next_page = get_allSrc(next_page)
            tasks.append(download_img(path, img_src))
        asyncio.run(asyncio.wait(tasks))


def get_allSrc(url):
    tree = etree.HTML(request(url))
    a_list = tree.xpath('//div[@class=\'NewPages\']/a')
    src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')
    next_page = ''

    if len(a_list) == 0 or len(src) == 0:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/img/@src')[0]
    else:
        src = tree.xpath('//*[@id="ArticleId{dede:field.reid/}"]/p/a/img/@src')[0]
        for a in a_list:
            if a.xpath('./text()')[0] == '下一页':
                next_page = 'https://www.umei.net' + a.xpath('./@href')[0]

    return src, next_page

async def download_img(path, img_src):
    name = img_src.rsplit('/', 1)[1]
    img_path = path + '/' + name
    print(f'开始下载: {name} ......')
    async with aiohttp.ClientSession() as session:
        async with session.get(img_src) as resp:
            img_data = await resp.content.read()
            async with aiofiles.open(img_path, mode='wb') as fp:
                await fp.write(img_data)
    print(name, "下载成功")



if __name__ == '__main__':
    url = 'https://www.umei.net/meinvtupian/meinvxiezhen/'
    get_link(url)
    for i in range(146):
        if i >= 2:
            index = f'{i}'
            url = 'https://www.umei.net/meinvtupian/meinvxiezhen/index_' + index + '.htm'
            get_link(url)
```
