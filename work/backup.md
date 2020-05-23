---
title: Navicat 定时备份
---

## 前言
当前项目数据量不大，所以只有一个单体数据库。为防止数据丢失，故采用定时备份的方式防止损失。

软件版本：Navicat Premium 11.2.7 (64-bit) - premium

## 步骤
1. 打开Navicat，选中要备份的数据库。如图，点击计划，双击可用任务，已选择的任务会出现在下方。

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/1.jpg)

2. 点击保存，设置文件名。

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/2.jpg)

3. 点击计划，选中刚才创建的批处理作业。点击 `设置任务计划`

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/3.jpg)

4. 对任务计划进行参数设置，我这里设置每天12：30备份。

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/4.jpg)

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/5.jpg)

如果未选中`仅在登录后运行`,可能会出现错误。


5. 进入window的任务计划程序，查看是否存在刚刚创建的任务，任务名就是刚才设置的文件名。

![1](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/navicat/6.jpg)

