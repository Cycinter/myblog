---
title: 24k纯小白Hugo+Github博客部署教程（一）博客主体
date: 2024-12-20T11:26:53-05:00
lastmod: 2024-12-25T11:26:53-05:00
author: Jules
cover: /img/2cover20241225.jpg
categories:
  - tools
tags:
  - 博客
  - 教程
draft: false
---
踩过许多坑，也想给别人铺路。超详细教程，15min完成部署。也有视频版

<!--more-->

## 📖系列介绍

本系列分为以下几个部分。纯纯小白看了好多篇攻略，踩了不少坑，有的因为github网站更新，有的有几小步可能默认大家知道，但是我完全不知道怎么配置，一个个试过、搜过、被chatgpt欺骗过，花了很长时间完成。每一个我强调的地方都是踩过坑的hhh此篇介绍详细从零搭建，VSCode, git bash, Nodejs都是新装的。
![image.png](https://23fee6a.webp.li/20241225175045995.png)

注：
- VSCode：装Hugo前安装，可编辑toml，yaml文件。
- git bash：推到github前安装，与github对接（此电脑为新装，几年前用过已失忆，为了建博客找回自己的Github才回忆起来之前用过😂）
- Node.js：PicGo安装插件前，页面会提示装，此篇用不到，后续图床篇会用到。

**相关链接**：
- B站视频教程：
https://www.bilibili.com/video/BV1H5CiYHEQR/?spm_id_from=333.999.0.0&vd_source=6c2436a2c690c581b1d0c127e9f28a37
- 教程命令行：
https://github.com/Cycinter/JulesTutorial/blob/master/HugoBlog/HugoBlogTutorial-cn.txt
- 教程workflow文件 (.yaml):
https://github.com/Cycinter/JulesTutorial/blob/master/HugoBlog/hugo.yaml

## 📑0. 部署总览

本文介绍以下1、2、3步骤，实现博客的主体搭建。总览挺重要的，我看了好多篇教程，刚开始因为不知道搭建的全貌，如总体要做什么，每一步是为什么，每部应该有什么，觉得很没头绪，故整理如下：
1. 使用hugo在本地建一个博客，应用主题，发布内容。结果：可使用local查看自己的博客并更新
2. github库建立：新建2个github库，一个作为source，内容来源于本地博客的推送；另一个为username.github.io（自己的用户名），作为github pages。使用github actions通过workflow文件自动部署。也可以用1个库实现，本教程使用2个库，需要token对接。
3. github对接与部署：在本地编辑hugo.yaml文件，放入本地博客文件夹中。将本地博客push到github source库，完成自动部署。结果：可以通过访问username.github.io查看博客，更新博客。
4. 以上是博客搭建内容的主干，完成几完成部署。其他几篇博客会介绍：
	1. 图床搭建。cloudflare + PicGo实现博客支持添加图片（博客中需要粘贴图片的url的md格式）
	2. 域名购买与对接。实现可通过域名访问博客。
	3. 基于主题支持的google analytics和valine博客评论搭建
	4. obsidian沉浸式写博客。发博客在obsidian中完成全部过程。无需打开powershell、git和PicGo。

## 📍1. Hugo建立本地博客

### 0. 安装

下载安装VSCode
https://code.visualstudio.com/
可用于编辑toml，yaml文件。
### 1.1 scoop 安装hugo（win版）

- 搜索并打开powershell
![image.png](https://23fee6a.webp.li/20241225175119330.png)

- 输入指令
```
scoop install hugo-extended
```
### 1.2 创建Hugo本地博客

#### 1.2.1 创建博客目录

- **安装路径**
在powershell中：更改路径为你想安装的位置，cd 后修改成你要的路径
``` 
cd E:\OneDrive\blog
```

- **创建博客目录**
创建博客目录，jinspr是我博客名，按你的更改。
```
hugo new site jinspr
```
结果：该目录下会出现此为文件和博客文件。

- **初始化git**
初始化git, jinspr是我博客名，按你的更改
```
cd jinspr
git init
```
结果：博客文件内出现.git文件夹
#### 1.2.2 博客主题安装调试

我在hugo官方选的主题：https://themes.gohugo.io/
如果为了快速跑通，建议先随便选一个。安装主题后需要做一件简单的关于自己博客的配置，主体文档里一般有介绍。
例如我选择的：https://themes.gohugo.io/themes/hugo-theme-dream/
这个主题文档写的很详细。
该主题文档中使用命令是(在刚刚E:\OneDrive\blog\jinspr路径下cd)
```
cd themes
git clone https://github.com/g1eny0ung/hugo-theme-dream.git dream
```

不过我目前使用的是我小小修改的（稍微看懂一些html就想调一下头像大小啥的😔）。为了不和原主题冲突，我用的不是`clone`指令，用了`git submodule` 并用的自己fork出来的路径
所以不同于以上，我使用的指令，同时命名了这个主体为dream，装在themes下
```
git submodule add https://github.com/cycinter/hugo-theme-dream.git themes/dream
```
结果：博客文件夹中themes文件夹出现主题dreams文件夹

- **toml文件指定主题**
博客根目录下hugo.toml文件编辑，复制作者主题中的example后编辑。https://github.com/g1eny0ung/hugo-theme-dream/blob/master/hugo.example.toml
其他参数慢慢看文档：https://g1en.site/hugo-theme-dream/
最重要修改，在toml文件加入第四行，确认theme名与自己主题文件名相同
![image.png|500](https://23fee6a.webp.li/20241225175149732.png)

- **本地查看站点**
在powershell中输入指令，确保在博客路径下
```
hugo server
```

结果：
![image.png](https://23fee6a.webp.li/20241225175218841.png)


- 点击链接或在浏览器输入，站点显示成功
 http://localhost:1313/
![image.png](https://23fee6a.webp.li/20241225175238353.png)


- **主题细节调试**
按主题文档填入基本信息。
![image.png](https://23fee6a.webp.li/20241225175315307.png)

在对应位置粘贴头像图片，这里头像的位置指的的根目录/static/img/avatar.jpg（我之前连这个位置都查很久）。


- **再看查看站点**
刷新发现已更新，也可使用浏览器隐私模式查看。
![image.png|1000](https://23fee6a.webp.li/20241225175254394.png)

### 1.3 发布文章
- 发文章
在powershell博客路径下，输入指令
```
hugo new content posts/test1.md
```
即发布了名为test1的文章，打卡对应路径下的md进行修改

- 看站点
文章默认是draft草稿模式，可使用以下指令查看含草稿的站点
```
hugo server -D
```
![image.png|600](https://23fee6a.webp.li/20241225175348642.png)

至此，本地博客建完。
## 🌎2. Github 库建立与设置
### 2.1 token创建
因为此种部署方式建了2个库，跨库部署需要token。点头像，设置：
![image.png](https://23fee6a.webp.li/20241226121253127.png)

找developer settings
![image.png](https://23fee6a.webp.li/20241225175601450.png)

创建token
![image.png|600](https://23fee6a.webp.li/20241225182526952.png)


token名，即Note要记住，后需要粘贴在ymal文件中。
日期随需要选，Select scopes中选择repo全部、workflow、admin:repo_hook
![image.png|600](https://23fee6a.webp.li/20241225182541572.png)


点击创建后，要复制好token，后面再回来点不会再显示了。

### 2.2 source库创建与设置
- 创建一个新库
命名myblog。这是个完全同本地博客文件的库。
![image.png|600](https://23fee6a.webp.li/20241225182605029.png)

- 库设置
库设置settings-secrets and variables-actions-secrets-new repository secret-输入上面第一步创建key的名字和key
![image.png|600](https://23fee6a.webp.li/20241225182616885.png)

### 2.1 pages库创建
此库地址为后续访问博客地址，注意库名为github的用户名.github.io
![image.png|600](https://23fee6a.webp.li/20241225175401933.png)


- 库设置
全默认，什么都不用改，我的默认设置是这样的
![image.png|600](https://23fee6a.webp.li/20241225175514683.png)



## 🔗3. 库对接与部署

### 3.1 本地yaml文件
要实现自动部署，需要使用这个工作流文件。
在本地的博客目录下新建.github/workflows/hugo.yaml
粘贴以下内容，修改：
这个文件是用于部署工作流，使得后续推到source库的public可以自动部署到pages。

```yaml
name: GitHub Pages

on:
  push:
    branches:
      - main # Set a branch to deploy

jobs:
  deploy:
    runs-on: ubuntu-20.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0 # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 0.126.3
          # 是否启用 hugo extend
          extended: false

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          # github_token: ${{ secrets.GITHUB_TOKEN }} # 该项适用于发布到源码相同repo的情况，不能用于发布到其他repo
          personal_token: ${{ secrets.ACTION_ACCESS_TOKEN }}
          external_repository: <用户名>/<用户名>.github.io # 指定目标仓库
          publish_dir: ./public
          publish_branch: main # 指定目标分支
          cname: xxx.com # 如果你有自定义域名

```

- **需要改的内容**
1. branches:
      main # Set a branch to deploy 要保证自己的库也是这个分支
2. hugo-version:
	在powershell中用以下命令行确认自己的hugo版本
```
	hugo version
```
![image.png](https://23fee6a.webp.li/20241226121529466.png)

我最后输入0.139.4
3. extended: false我刚刚安装hugo时候是hugo extended，所以我是true
4. personal_token: ${{ secrets.ACTION_ACCESS_TOKEN }}中，把替换成刚刚复制的token名，其他不动，我的这行为：personal_token: ${{ secrets.JINSPRBLOG }}
5. external_repository: Cycinter/cycinter.github.io修改为我们的pages库名
6. publish_branch: main 分支确认下，一会有用。
7. cname: jinspr.com # 如果你有自定义域名改，没有前面加# 

### 3.2 库推送

- 下载git
https://git-scm.com/downloads

- 搜索打开git
![image.png|500](https://23fee6a.webp.li/20241225182651719.png)

建议通过下面的指令解释，浅浅了解下git的一些指令。我前面不熟悉，老是被gpt指错路，如推送错的库名或分支什么的，了解用到的即可。

- 更改路径
```
cd /e/OneDrive/blog/jinspr/
```
这是我的本地博客路径，改成你的

- 更改分支
以下显示默认分支是master，与我们上面yaml文件的不同
![image.png|500](https://23fee6a.webp.li/20241225182721353.png)

输入指令更改
```
git branch -M main
```

- 对接远程库
```
git remote add source https://github.com/Cycinter/myblog.git
git remote add pages https://github.com/Cycinter/cycinter.github.io.git
```
以上表示第一个库名为source，第二个为pages

- 验证远程库配置正确，输入
```
git remote -v
```

显示此为正确：
![image.png|600](https://23fee6a.webp.li/20241225182752371.png)

- 添加文件到暂存区
```
git add .
```

- 将暂存区中的更改提交到本地的仓库，并添加消息，引号中消息根据内容自定义
```
git commit -m "Initial commit with Hugo site"
```

- 上传远程代码并合并，上传到source库的main分支
```
git push source main
```

结果：
查看github库
![image.png|500](https://23fee6a.webp.li/20241225182812315.png)
![image.png|500](https://23fee6a.webp.li/20241225182831868.png)


### 3.3 部署成功

浏览器地址栏输入cycinter.github.io
部署成功
![image.png](https://23fee6a.webp.li/20241225182856214.png)



