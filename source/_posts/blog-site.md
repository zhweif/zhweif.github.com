---
title: 利用Hexo和GitHub Pages 快速搭建个人博客
date: 2021-12-13 14:50:51
author: 锋哥
tags:
- Hexo
- blog
categories:
- Hexo
thumbnail:
blogexcerpt: 如何快速搭建自己的个人博客网站? 不用购买域名和服务器,只需要一个GitHub账号,使用Hexo生成静态文件,部署到GitHub上面就可以搞定了.
---

## GitHub准备

### 创建账号

如果没有GitHub账号先去注册一个,注册流程很简单跟着提示走就好了,[GitHub网址](https://github.com/)

> 注意在注册流程中,用户名(username)不要随便填,最好想一个自己喜欢的,因为后续访问的域名会包含用户名(username.github.io),
> 假如你的用户为zhangsan,那么访问的网址就是zhangsan.github.io

### 创建仓库

#### 新建仓库

![](https://github.com/zhweif/blog-img/raw/main/hexo/blog-site-create/github-new.jpg)
![](https://github.com/zhweif/blog-img/raw/main/hexo/blog-site-create/repository-message.jpg)

然后点击 create repository 按钮,创建仓库就完成了!

#### 设置pages

进入到仓库里面,点击Setting->Pages 设置相关信息:

![](https://github.com/zhweif/blog-img/raw/main/hexo/blog-site-create/repository-settings-pages.jpg)

以上GitHub的准备工作就做完了.

## 使用Hexo工具生成静态网页

[Hexo](https://hexo.io/) 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

安装比较简单,参考[官方文档](https://hexo.io/zh-cn/docs/)即可.

### init

本地选择一个文件夹打开cmd窗口执行初始化命令:

``` 
hexo init <folder>
```

> 注意: 指定的文件夹要是一个空的文件夹

初始化完成后,指定文件夹的结构如下:

```
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

每个文件的具体内容参考[官方文档](https://hexo.io/zh-cn/docs/).

### 配置

#### 主题

1. 在[官网](https://hexo.io/themes/)选择一个主题,将它的代码从GitHub clone到本地,
2. 然后将整个文件复制到hexo初始化的那个文件夹下面的themes 文件夹下面
3. 然后再_config.yal中配置(注意不是主题里面的_config)
```
theme: 主题名称
```

其余的配置参考[官方文档](https://hexo.io/zh-cn/docs/).
