---
layout: mypost
title: 将GitHub Page绑定阿里云服务器
categories: Blog
---

## 申请一个Github Page

- 点击右上角铃铛旁 "+"号
- New repository
- 在Repository name中填写github_id.github.io 
- 点击绿色按钮Create repository
- 去[Jekyll](http://jekyllthemes.org/)下载模板上传即可在github_id.github.io访问自己主页



## 更改GitHub Page域名

> 条件

​	购买一个域名并且备案

> 设置相关操作

1. 进入刚刚创建的github_id.github.io点击Settings

2. 找到最下GitHub Pages的 Custom domain 里填上备案的域名

3. 在购买域名服务商的DNS解析新增两条记录

   a. CNAME @ github_id.github.io

   b. CNAME www github_id.github.io

至此即可通过购买的域名访问GitHub Page



