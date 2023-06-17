---
title: "Python视频爬虫"
description: "Python视频爬虫"
keywords: "爬虫"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
  - 爬虫
---

```python
import requests
from lxml import etree
import re
import os
import aiohttp
import aiofiles
import asyncio
import shutil
from Crypto.Cipher import AES

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/91.0.4472.106 Safari/537.36'
}
# 下载地址
path = "D:/COLI/"

def login(url):
    session = requests.session()
    url = url + 'login.php?'
    data = {
        'pwuser': '小车车压槽',
        'pwpwd': 'qazwsxedc',
        'hideid': 0,
        'cktime': '31536000',
        'forward': 'https://www.d5034af6f919.xyz/',
        'jumpurl': 'https://www.d5034af6f919.xyz/',
        'step': 2
    }
    session.post(url=url, data=data, headers=headers)
    return session

# 封装网络请求
def requ(session, url):
    resp = session.get(url=url, headers=headers)
    resp.encoding = 'utf-8'
    return resp

# 使用模块区的链接获取
def serach_tody(session, url):
    today_url = url + '&search=today'
    resp = requ(session, today_url)
    tree = etree.HTML(resp.text)
    tag_list = tree.xpath('//tr[@class="tr3 t_one tac"]')
    link_list = []
    print('------------------------今日内容--------------------------')
    for index, tag in enumerate(tag_list):
        title = tag.xpath('./td[2]/h3/a/text()')[0]
        time = tag.xpath('./td[3]/div/text()')[0]
        link = tag.xpath('./td[2]/h3/a/@href')[0]
        print(f'{index}.----------------------------------------------{title} {time}')
        link_list.append(link)
    return link_list

# 使用页面page的链接获取
def get_video_url(session, url):
    resp = requ(session, url)
    tree = etree.HTML(resp.text)
    video_url = tree.xpath('//*[@id="iframe1"]/@src')[0]
    return video_url

# 使用video的链接获取m3u8文件
def get_m3u8_url(session, url):
    resp = requ(session, url)
    rexp = re.compile(r"url: '(?P<url>.*?)\?t=3',", re.S)
    page_source = resp.text
    m3u8_url = rexp.search(page_source).group("url")
    rexp = re.compile(r"<title>(?P<title>.*?)</title>", re.S)
    title = rexp.search(page_source).group("title")
    return title, m3u8_url

# 使用m3u8的链接下载
def down_m3u8(name, url):
    print('开始下载', name)
    if not os.path.exists(path + 'M3U8/'):
        os.makedirs(path + 'M3U8/')
    file_path = path + 'M3U8/' + name + '.m3u8'
    data = requests.get(url=url, headers=headers).content
    with open(file_path, 'wb') as f:
        f.write(data)
    print(name + '.m3u8下载成功')



async def down_ts(url, name, ts_key, session, title):
    file_path = path + 'Ts/' + title.replace(" ", "") + '/'
    if not os.path.exists(file_path):
        os.mkdir(file_path)
    aes = AES.new(key=ts_key, mode=AES.MODE_CBC)
    async with session.get(url) as resp:
        async with aiofiles.open(f'{file_path}/{name}', mode='wb') as f:
            data = await resp.content.read()
            await f.write(aes.decrypt(data))
    print(f'{name}下载完成')


def get_ts_key(url):
    ts_key = requests.get(url=url, headers=headers).content
    return ts_key


async def down_video(name, ts_domain):
    tasks = []
    file_path = path + 'M3U8/' + name + '.m3u8'
    async with aiohttp.ClientSession() as session:
        async with aiofiles.open(file_path, mode='r', encoding='utf-8') as f:
            async for line in f:
                if '.key' in line:
                    rexp = re.compile(r'URI="(?P<key_url>.*?)",', re.S)
                    key_url = rexp.search(line).group("key_url")
                    ts_key = get_ts_key(ts_domain + key_url)
                if line.startswith("#"):
                    continue
                # 去掉多余的空格和换行
                line = line.strip()
                # 拿到正真的ts地址
                ts_url = ts_domain + line
                # 创建异步任务
                task = asyncio.create_task(down_ts(ts_url, line, ts_key, session, title))
                # 加入任务列表
                tasks.append(task)
            await asyncio.wait(tasks)


def merge_ts(name):
    print(f'正在合并{name}.mp4 请稍后...........')
    name = name.replace(" ", "")
    video_path = f'{path}Video/'
    if not os.path.exists(video_path):
        os.mkdir(video_path)
    os.chdir(path + '/Ts/' + name)
    os.system(f'copy /b *.ts ..\\..\\Video\\{name}.mp4')
    print(f'{name}.mp4 合并成功')



if __name__ == '__main__':
    # 进入地址
    domain = 'https://www.d5034af6f919.xyz/'
    print('正在登录，请稍后..........')
    # 拿到登录的会话
    session = login(domain)
    # 进入主页
    index_url = domain + 'index.php'
    resp = requ(session, index_url)
    tree = etree.HTML(resp.text)
    # 拿到用户名
    user = tree.xpath('//*[@id="header"]/div[2]/span/text()')[0]
    # 拿到vip区的地址
    vip_link = domain + tree.xpath('//*[@id="cate_1"]/tr[2]/th[1]/h2/a/@href')[0]
    print('登录成功')
    print('用户名：', user)
    print('正在进入vip影视区.........')
    links = serach_tody(session, vip_link)
    if not os.path.exists(path + 'Ts/'):
        os.mkdir(path + 'Ts/')
    else:
        shutil.rmtree(path + 'Ts/')
        os.mkdir(path + 'Ts/')

    loop = True
    while loop:
        index = int(input('请选择你要下载的索引[-1退出]：'))
        if index == -1:
            break
        elif index < -1 and index > len(links):
            index = int(input('你的输入有误，请重新输入[-1退出]：'))
        else:
            page_url = domain + links[index]
            video_url = domain + get_video_url(session, page_url)
            title, m3u8_url = get_m3u8_url(session, video_url)
            print(m3u8_url)
            m3u8_domain = m3u8_url.rsplit('/', 1)[0] + '/'
            down_m3u8(title, m3u8_url)
            asyncio.run(down_video(title, m3u8_domain))
            merge_ts(title)
```
