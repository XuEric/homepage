---
layout: master
title: 在Github上建立Blog
---
# {{ page.title }} #

## 大致步骤： ##

1. 申请Github帐号
2. 在客户端上安装git工具
3. Fork一个Blog模版
4. 克隆到本地
> git clone https://github.com/XuEric/homepage.git
5. 编辑
6. 提交到本地
> git commit -am 'init'
7. 推送到服务
> git push origin gh-pages

<p>{{ page.date | date_to_string }}</p>
