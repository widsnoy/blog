---
title: "详细揭秘⚡\U0001F9CA专业"
categories: 编程
tags: python
mathjax: false
abbrlink: bfc9
date: 2023-12-09 17:15:29
updated:
keywords:
description:
top_img:
comments:
cover:
toc:
toc_number:
toc_style_simple:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
aplayer:
highlight_shrink:
aside:
---
好吧，我是标题党x

> 请各位完成一份杭州电子科技大学院系逻辑结构，以树形结构描述出来，叶子结点为各专业，可去各分院官网搜索信息，作为本学期的思政作业。

我发现学校总是喜欢布置些奇怪的任务x  
不过不会真有人手写吧（x  
记录一下我是怎么做这个作业的（  
这个应该算可以公开的信息吧（？

<!--more-->

## 获取数据
我记得教务网站选课那里有所有学院和对应专业的信息。
![](/img/hdu_majors_0.png)
Ctrl+Shift+I 搜一下，发现所有信息都是明文存储的，所以打算从这里入手。
![](/img/hdu_majors_1.png)
但是有个问题，要选择对应的学院才会有其下相关的专业，如果手动选择再记录数据还是很麻烦，并且也很无聊...
所以我上网搜索了一下，有没有模拟点击并且能记录网页源码的自动化工具，了解到这个其实就属于 python 爬虫干的活。

在[这里](https://github.com/wistbean/learn_python3_spider)稍微学习了一下前几章后~~为什么他有这么多神比表情包~~，我想了下，这个 selenium 不就能实现我的需求。

于是便很快写了一个脚本，实现了以下操作：
1. 登陆  
2. 在下拉框依次选择学院  
3. 提取当前相关专业的信息  

~~但是并没有那么顺利~~  
在模拟选择下拉框时，遇到报错
```
selenium Select could not be scrolled into view
```
[stackoverflow](https://stackoverflow.com/questions/56085152/selenium-python-error-element-could-not-be-scrolled-into-view) 上老哥说是因为点击太快页面还没来得及加载，但是按照他的方法问题也没有得到解决。  

```python
driver.execute_script("arguments[0].style.display = 'block';", xxx)
```
这个报错浪费了我很多时间，后来突然发 style="display:none" 才恍然大雾，只需要让这个框可见就行了。

```python
#!/usr/bin/python3
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select
import time

driver = webdriver.Firefox()
driver.get("http://newjw.hdu.edu.cn/jwglxt/kbdy/bjkbdy_cxBjkbdyIndex.html?gnmkdm=N214505&layout=default")
driver.find_element(By.ID, 'yhm').send_keys('')
driver.find_element(By.ID, 'mm').send_keys('')
driver.find_element(By.ID, 'dl').click()

faculty = driver.find_element(By.ID, 'jg_id')
driver.execute_script("arguments[0].style.display = 'block';", faculty)
select_f = Select(faculty)
faculty_list = select_f.options

output = open("/home/widsnoy/Documents/DS Homework/HDU map/output.txt", "w")

for i, x in enumerate(faculty_list):
    select_f.select_by_index(i)
    major = driver.find_element(By.ID, 'zyh_id')
    driver.execute_script("arguments[0].style.display = 'block';", major)
    major_list = Select(major).options
    if (x.text == "全部") :
        continue
    print(x.text, end = ' ', file = output)
    for y in major_list:
        if (y.text == "全部") :
            continue
        print(y.text, end=' ', file = output)
    print("\n--------------------", file = output)
    time.sleep(0.5)

driver.close()
```
## 画图
经过简单🔎找到了这个开源的库，[anytree](https://github.com/c0fec0de/anytree)  
```python
>>> udo = Node("Udo")
>>> marc = Node("Marc", parent=udo)
```
这样简单建出树就可以用一行命令可视化这颗树
```python
UniqueDotExporter(ROOT).to_picture("tree.png")
```
其实在做这个作业之前，我就想到了这个树会长什么样子...
想象一下高度为 3 树却有 105 个叶子
```bash
$ identify ./tree.png
./tree.png PNG 32767x226 32767x226+0+0 8-bit sRGB 997678B 0.000u 0:00.000
```
不过我想到了三种方法解决这个问题
1. 通过改变边的长度使节点错开
![期望效果](/img/hdu_majors_2.png)
但是并不知道该怎么实现，也就作罢了  

2. 这个方法比较暴力，直接按列把图片切割就行了
```bash
convert -crop 1700x226 ./HDU_Majors_Map.png ./HDU_Majors_Map.png
```
不过我觉得这个方法不能说差强人意，只能说聊胜于无
![](/img/已切割.jpg)
3. anytree 自带的 RenderTree 实现和 linux 的 tree 命令一样的效果，我自己认为这是最好的解决办法，不知道这样的形式能不能被老师接受。
```python
for pre, fill, node in RenderTree(ROOT):
    print("%s%s" % (pre, node.name))
```
附上结果（
```
杭州电子科技大学
├── 机械工程学院
│   ├── 车辆工程(0105)
│   ├── 机械工程新工科创新实验班(0112)
│   ├── 机械类(0107)
│   ├── 机械设计制造及其自动化(0101)
│   ├── 机械设计制造及其自动化(第二学士学位专业)(0109)
│   └── 智能制造工程(0108)
├── 管理学院
│   ├── 保密管理(0301)
│   ├── 电子商务(0316)
│   ├── 电子商务(联合培养专升本)(0306)
│   ├── 工商管理(0312)
│   ├── 工商管理类(0373)
│   ├── 工业工程(0368)
│   ├── 管理科学与工程类(0304)
│   ├── 人力资源管理(0315)
│   ├── 信息管理与信息系统(0313)
│   └── 信息管理与信息系统（第二学士学位专业）(0305)
├── 电子信息学院（集成电路科学与工程学院）
│   ├── 电子科学与技术(0420)
│   ├── 电子信息工程(0418)
│   ├── 电子信息工程（第二学士学位专业）(0404)
│   ├── 电子信息类（电子信息学院）(0406)
│   ├── 电子信息新工科实验班(0407)
│   └── 集成电路设计与集成系统(0422)
├── 计算机学院（软件学院）
│   ├── 计算机科学与技术(0523)
│   ├── 计算机科学与技术（第二学士学位专业）(0506)
│   ├── 计算机类(0505)
│   ├── 计算机信息技术（微专业）(0586)
│   └── 软件工程(0527)
├── 自动化学院（人工智能学院）
│   ├── 测控技术与仪器(0661)
│   ├── 测控技术与仪器（第二学士学位专业）(0606)
│   ├── 电气工程及其自动化(0601)
│   ├── 医学信息工程(0687)
│   ├── 智能科学与技术(0631)
│   ├── 自动化(0628)
│   ├── 自动化(新工科实验班)(0609)
│   └── 自动化（第二学士学位专业）(0605)
├── 理学院
│   ├── 光电信息科学与工程(0707)
│   ├── 民族预科生(0799)
│   ├── 数学类(0706)
│   ├── 数学与应用数学(0733)
│   ├── 信息与计算科学(0731)
│   └── 应用物理学(0732)
├── 通信工程学院
│   ├── 电子信息类（通信工程学院）(0805)
│   ├── 通信工程(0834)
│   ├── 通信工程（第二学士学位专业）(0804)
│   ├── 通信新工科创新实验班(0806)
│   └── 信息对抗技术(0835)
├── 外国语学院
│   ├── 俄语语言文学（微专业）(1105)
│   └── 英语(1137)
├── 体育教学部
├── 会计学院
│   ├── 财务管理(1409)
│   ├── 会计学(1406)
│   ├── 会计学(ACCA)(1401)
│   ├── 会计学（第二学士学位专业）(1407)
│   └── 审计学(1447)
├── 经济学院
│   ├── 国际经济与贸易(1508)
│   ├── 国际经济与贸易(联合培养专升本)(1515)
│   ├── 金融学(1510)
│   ├── 金融学（CFA）(1509)
│   ├── 金融学（第二学士学位专业）(1514)
│   ├── 经济学(1511)
│   ├── 经济学类(1513)
│   └── 统计学(1507)
├── 国际教育学院
│   ├── 电气工程及其自动化(国际教育学院)(全英文)(1726)
│   ├── 电子信息工程(国际教育学院)(全英文)(1719)
│   ├── 工商管理(国际教育学院)(1721)
│   ├── 工商管理(国际教育学院)(全英文)(1798)
│   ├── 国际经济与贸易(国际教育学院)(1711)
│   ├── 国际经济与贸易(国际教育学院)(全英文)(1704)
│   ├── 汉语国际教育(国际教育学院)(1716)
│   ├── 机械设计制造及其自动化(国际教育学院)(全英文)(1718)
│   ├── 计算机科学与技术(国际教育学院)(全英文)(1709)
│   ├── 软件工程(国际教育学院)(全英文)(1725)
│   ├── 通信工程(国际教育学院)(全英文)(1708)
│   ├── 医学信息工程(国际教育学院)(全英文)(1751)
│   └── 智能制造工程(国际教育学院)(全英文)(1750)
├── 卓越学院
│   ├── 大数据与智能决策(管理科学与工程类)(1874)
│   ├── 计算机科学英才班(1871)
│   ├── 金融科技(金融学)(1872)
│   ├── 经管类实验班(1842)
│   ├── 理工类实验班(1841)
│   ├── 人工智能(智能科学与技术)(1879)
│   ├── 智能安全(网络空间安全类)(1880)
│   ├── 智能财务(会计学)(1865)
│   ├── 智能计算与数据科学(计算机科学与技术)(1862)
│   ├── 智能硬件与系统(电子信息工程)(1860)
│   └── 智能硬件与系统(集成电路设计与集成系统)(1861)
├── 材料与环境工程学院
│   ├── 材料科学与工程(2088)
│   └── 环境工程(2003)
├── 数字媒体与艺术设计学院
├── 马克思主义学院
├── 网络空间安全学院（浙江保密学院）
│   ├── 网络工程(2724)
│   ├── 网络空间安全(2703)
│   └── 信息安全(2736)
├── 人文艺术与数字媒体学院、法学院
│   ├── 产品设计(2807)
│   ├── 产品设计（专升本）(2817)
│   ├── 传播学(2811)
│   ├── 法学(2810)
│   ├── 法学(联合培养专升本)(2819)
│   ├── 法学（第二学士学位专业）(2820)
│   ├── 工业设计(2805)
│   ├── 汉语国际教育(2803)
│   ├── 社会学(2809)
│   ├── 设计学类(2806)
│   ├── 数字媒体技术(2804)
│   ├── 数字媒体艺术(2808)
│   └── 数字媒体艺术（专升本）(2818)
├── 继续教育学院
├── 圣光机联合学院
│   ├── 计算机科学与技术(中外合作办学)(3201)
│   └── 自动化(中外合作办学)(3202)
└── 教务处
```

可以发现泥电大多都是工科专业  
~~还有这个教务处是什么~~
