---
categories: Code
date: 2016-02-28T00:00:00Z
tags:

- Software
title: 微信群重复人员识别
toc: true
url: /2016/02/28/wechat-remove/
---

由于微信的限制，人数超过一百以后，只有邀请才能加入。所以，如果想要组织大型的在线活动，就只有建立很多个分群。然而分群的问题在于，会有很多人重复加群，挤占了本来就缺乏的空间。所以需要找到一个可行的办法，清除两个群中的重复人员。

<!--more-->

# 可行的方法

在微信网页版中，通过点击群名称右侧的向下箭头，可以打开一个有所有人昵称的面板，通过审查工具可以得到对应的代码，右击选择`Edit As HTML`，全选复制，保存到`a.txt`中，然后执行

```bash
cat a.txt | grep -o "\">.*</p>" > fen.txt
```

我们便可以将所有人的名字都输出到`fen.txt`中，尽管还有着一些`<`，`>`的符号，但是已经不影响识别了，故不再做处理。

对两个群分别如此操作之后，我们得到了两个群的成员名单。
然后执行如下Python代码：

```
with open('fen.txt', 'r') as f:
    fen = f.readlines()
    f.close()
with open('main.txt', 'r') as f:
    main = f.readlines()
    f.close()

for i in main:
    for j in fen:
        if i == j:
            print(i)
```

得到的输出就是两个群中重复的成员，然后一一删除即可。

# 方案反思

## 存在的缺陷

- 自动化程度较低
- 如果存在用户同名的话，会导致删除错误的用户。

## 可能的改进

- 获取资料不需要手动点击审查元素，通过jQuery的text()函数也可以实现。
- 最后实现删除的过程可以进一步优化，通过模拟用户点击的脚本实现（存在隐私泄漏风险）。
- 可以获取用户的UUID，以避免重名现象。


# 更新日志

- 2016年02月28日  首次发布

