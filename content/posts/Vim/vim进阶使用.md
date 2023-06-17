---
title: "Vim进阶使用"
description: "Vim进阶使用"
keywords: "Vim"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - Vim
tags:
  - Vim
---

## 1. 查找和替换
### 1.1 搜索类命令
```shell
  输入 / 加上一个字符串可以用以在当前文件中查找该字符串。
```
```
1. 在正常模式下输入 / 字符。您此时会注意到该字符和光标都会出现在屏幕底部，这跟 : 命令是一样的。
2. 接着输入 errroor <回车>。那个errroor就是您要查找的字符串。
```

3. 要查找同上一次的字符串，只需要按 n 键。要向相反方向查找同上一次的字
符串，请输入大写 N 即可。
4. 如果您想逆向查找字符串，请使用 ? 代替 / 进行。
5. 要回到您之前的位置按 CTRL-O (按住 Ctrl 键不放同时按下字母 o)。重复按可以回退更多步。CTRL-I 会跳转到较新的位置。
```shell
--->  "errroor" is not the way to spell error;  errroor is an error.
```

提示：如果查找已经到达文件末尾，查找会自动从文件头部继续查找，除非 `wrapscan` 选项被复位。

### 1.2 替换命令
```shell
 输入 :s/old/new/g 可以替换 old 为 new。
```

```
1. 请将光标移动到本节中下面标记有 ---> 的那一行。
```

2. 输入 :s/thee/the <回车> 。请注意该命令只改变光标所在行的第一个匹配
串。
3. 输入 :s/thee/the/g	则是替换全行的匹配串，该行中所有的 "thee" 都会被
改变。

```
---> thee best time to see thee flowers is in thee spring.
```

4. 要替换两行之间出现的每个匹配串，请
输入   :#,#s/old/new/g   其中 #,# 代表的是替换操作的若干行中首尾两行的行号。
输入   :%s/old/new/g     则是替换整个文件中的每个匹配串。
输入   :%s/old/new/gc    会找到整个文件中的每个匹配串，并且对每个匹配串提示是否进行替换。

## 2. 宏录制

**宏的录制**

1. 在 Vim 编辑器中创建新文件或打开现有文件；
2. 按 `q` 并跟上 `宏名` 开始录制。假设我们想要将宏名命名为 `b` ，则按 `Esc` 和 `qb` 开始录制；
3. 按 `q` 停止录制。至此，宏的录制已完成。

**宏的运行**

按 `@` 接 `宏名` 即可运行宏一次，按 `次数` 接 `@宏名` 重复运行宏多次。

下面给出实例来详细介绍。

#### 示例-1：使用宏多次生成相同的文本

例如我们要生成 100 行一样的文字，一种比较笨的办法就是复制然后粘贴所需要的遍数。这种效率非常低，而且非常容易出错。有了宏这个工具，一切变得超级简单！

首先，我们在 Vim 编辑器中创建一个名为 `file1.txt` 的新文件。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153547.webp#crop=0&crop=0&crop=1&crop=1&id=rAT8m&originHeight=199&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

然后，我们按 `q` 之后再按 `z` 开始录制宏。这里的 `z` 就是 `宏名` ，后面会用到。如果录制正确，底部就会出现 `recording @z` 字样。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153554.jpeg#crop=0&crop=0&crop=1&crop=1&id=vvm6R&originHeight=202&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

再之后，按 `i` 然后输入要需要重复的文本。比如这里输入文本 `Learning Vim Macros` 。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153600.jpeg#crop=0&crop=0&crop=1&crop=1&id=Fr02s&originHeight=203&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

最后按 `q` 停止录制，这时 `recording @z` 就会从底部消失。这样，一个名为 `z` 的 宏就录制完成了，后续我们就可以利用这个宏来重复完成文字录入工作。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153602.jpeg#crop=0&crop=0&crop=1&crop=1&id=iTpKe&originHeight=202&originWidth=732&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果你想运行一次刚刚创建的宏，那么将光标移动到文件的所需位置，然后并按 `@z` 就可以了，这时文字就会被复制一遍。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153605.jpeg#crop=0&crop=0&crop=1&crop=1&id=Bh3YD&originHeight=201&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

如果你想要复制 N 多次这行文字，则可以多次运行刚刚那个宏。此时只需在宏前面加 `次数` 即可。例如 `4@z` 就可以运行刚才的宏 4 次，相应文字就会被复制 4 遍。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153608.jpeg#crop=0&crop=0&crop=1&crop=1&id=ZrCgp&originHeight=200&originWidth=726&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 示例-2：使用宏生成序列号

在示例-1里我们是机械重复复制一堆一样的文字，那如果想要生成带序列的文本该如何处理？使用宏也可以轻松完成！

假设我们要生成 `101-110` 的一系列数字，我们一起来完成这个需求。

首先，在 Vim 编辑器中创建一个名为 `file2.txt` 的新文件，接着按 `i` 键入 `101.`。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153612.jpeg#crop=0&crop=0&crop=1&crop=1&id=EshAG&originHeight=201&originWidth=731&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

按 `ESC` 退出键入。然后再按 `qy` 以宏名称 `y` 开始录制后续的操作。我们将光标移到第一行的开头并按 `yy` 复制文本，然后按 `p` 将文本粘贴到下一行。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153616.jpeg#crop=0&crop=0&crop=1&crop=1&id=SMGbh&originHeight=199&originWidth=724&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

接着，按 `Ctrl + a` 将第二行的值从 `101` 增加到 `102` 。最后，像之前一样按 `q` 停止录制，我们的宏就制作完成了。

**接下来，就是见证奇迹的时刻！**

我们键入 `8@y` ，刚刚所录制的宏就会运行 8 次，生成从 `103` 到 `110` 的连续数字。一键操作，简单高效！

这对于一些带有序列性的文本非常有用，你就无需再重复去做 复制-->改数字 这样重复枯燥的工作，同时效率也会大大提升！

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153623.jpeg#crop=0&crop=0&crop=1&crop=1&id=mEQ78&originHeight=198&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 示例-3：在文件的每一行中添加相同的文本

我们在操作数据库的时候，很多情况下一些语句非常相似，比如 `insert into items values(100, 20077100050, alvin)` 这样的文本，会重复很多次。假如我们现在已经有了要插入的数据，如何快速生成多条 SQL 语句？

在这个例子里，我们假设数据文件 `file3.txt` 有 4 条数据，如下图示。

首先，我们在 Vim 编辑器中打开数据文件 `file3.txt` ，然后按 `ESC` ，再然后按 `qd` 以宏名 `d` 开始录制。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153625.jpeg#crop=0&crop=0&crop=1&crop=1&id=Y73D2&originHeight=111&originWidth=728&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

将光标转到文件的开头，按 `i` 键插入文本。然后再输入 `insert into items values(` ，再接着按 `ESC`， `w`， `yw` 5 次。

在这里，`w` 用于转到下一个单词，`yw` 用于复制当前单词。之所以这里要 5 次是因为这里每行有五部分。分别是 `11001` `,` `HDD` `,` `Samsung` （注意逗号也算一部分）。

之后，按 `ESC` 和 `a` 键将光标移动到行尾，再输入 `);` 。

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153628.jpeg#crop=0&crop=0&crop=1&crop=1&id=s4ls3&originHeight=108&originWidth=725&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

最后，按 `j` 然后按 `q` 。这里，`j` 用于移动到下一行，`q` 用于停止录制。这样我们所需要的宏就制作完成了。

现在，我们想要让下面剩下的 3 行的文本都处理成第一行那样的格式，直接按 `3@d` 运行宏 3 次就可以实现。这样一来，在数据库中进行插入就会方便很多，而且我们无需手动一个个去更改，简单高效！

![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210520153631.jpeg#crop=0&crop=0&crop=1&crop=1&id=vRQLm&originHeight=111&originWidth=730&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
