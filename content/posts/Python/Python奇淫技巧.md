---
title: "Python奇淫技巧"
description: "Python奇淫技巧"
keywords: "Python"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Python
tags:
  - Python
---

## 1. 交换
```python
# 交换
a = 3
b = 5
print(f'a: {a}, b: {b}')
a, b = b, a
print(f'a: {a}, b: {b}')

# 输出
a: 3, b: 5
a: 5, b: 3
```
## 2. yield的使用
```python
# yield的使用
def fibonacci(n):
    a = 0
    b = 1
    for _ in range(n):
        yield a
        a, b = a+b, a
for i in fibonacci(10):
    print(i)
    
# 输出
0
1
1
2
3
5
8
13
21
34
```
## 3. 列表循环
```python
# 列表循环
list = [1, 3, 5, 7, 9]
for li in list:
    print(li)
# 进阶使用
for index, value in enumerate(list):
    print(f"索引: {index}, 值: {value}")
    
# 输出
1
3
5
7
9
索引: 0, 值: 1
索引: 1, 值: 3
索引: 2, 值: 5
索引: 3, 值: 7
索引: 4, 值: 9
```
## 4. 字典合并
```python
# 字典合并
x = {'1': 'admin', '2': 'mike'}
y = {'3': 'lula', '4': 'lihua'}
z = {**x, **y}
print(z)

# 输出
{'1': 'admin', '2': 'mike', '3': 'lula', '4': 'lihua'}
```
## 5. 三元运算符
```python
# 三元运算符
# Ture if 条件 else False
a = 10
b = 20
print(a) if a < b else print(b)

# 输出
10
```
## 6. 序列解包
```python
# 序列解包
# eg1:
name = "Yong Zhong"
last_name, fist_name = name.split()
print(f"fist_name: {fist_name}, last_name: {last_name}")
# eg2:
list = [1, 2, 3]
a, b, c = list
print(a, b, c)

# 输出
fist_name: Zhong, last_name: Yong
1 2 3
```
## 7. 关闭流
```python
# with语句
# 相对于关闭流
with open('./test.txt', 'r+') as fp:
    str = fp.readline()
    print(str)
```
