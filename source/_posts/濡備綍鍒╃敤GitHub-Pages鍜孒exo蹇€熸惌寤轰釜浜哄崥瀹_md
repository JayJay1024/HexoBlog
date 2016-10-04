---
title: 如何利用GitHub Pages和Hexo快速搭建个人博客
date: 2016-09-03 13:14:17
tags: [Hexo,yilia]
categories: 攻城狮那些事儿
---

## 为什么是GitHub Pages：
GitHub Pages提供建立静态的站点，非常适合个人主页、博客等不需要太多动态交互、不太需要数据库支持的网站，而且GitHub上有很多模板和主题，不需要太专业的知识就可以快速搭建一个网站，同时可以用Git管理我们的代码，上传到GitHub托管，最主要还是。。。。。。GitHub Pages免费。

<!--more-->

## 两种模式：
1. User/Organization 个人或公司站点
 - 每个GitHub账户只能建一个
 - 资源命名必须为：username/username.github.io
 - 远程仓库的master分支被用来构建和发布页面
2. Project Pages 项目站点
 - ph-pages分支用于构建和发布页面
 - 多个项目可以建立多个站点

## 系统环境配置：
 - 安装Git：
   ```
   sudo apt-get install git
   ```
 - 安装Hexo：
   ```
   sudo npm install -g hexo-cli
   ```

> Note:
> - 安装Hexo出现“this failure might be due to the use of legacy binary node”，执行一下”sudo apt-get install nodejs-legacy“然后重新安装
> - 也可以使用Jekyll，Hexo和Jekyll都是生成静态网页的工具，只是听说Hexo渲染比Jekyll好，主题多、切换方便

## 搭建步骤：

```
mkdir GitHubPages
cd GitHubPages
hexo init HexoBlog
cd HexoBlog
hexo generate
hexo server
```

此时用浏览器打开*http://localhost:4000/*可以看到一篇内置的Blog了

> Note:
> - hexo generate/g：生成静态文件，会在当前目录下生成一个新的叫做public的文件夹
> - hexo server/s：启动本地web服务，用于博客的预览
> - hexo deploy/d：部署博客到远端（比如github, heroku等平台）
> - hexo clean：清理，包括public文件夹

## 部署到GitHub Pages：

- 在GitHub上建立站点：
![github_0](http://ocww00tfq.bkt.clouddn.com/image/jpg/github_0)
![github_1](http://ocww00tfq.bkt.clouddn.com/image/jpg/github_1)
![github_2](http://ocww00tfq.bkt.clouddn.com/image/jpg/github_2)

- 配置本地站点的_config.yml：
![_config_deply](http://ocww00tfq.bkt.clouddn.com/image/jpg/_config_deply)

- 部署到GitHub Pages：
```
cd GitHubPages/HexoBlog
hexo d
```
> 踩坑提醒：需要提前安装一个扩展：
> npm install hexo-deployer-git --save


- 部署成功如下，此时在浏览器输入*http://lucienlau.github.io*可以访问页面了
![hexo_deploy_success](http://ocww00tfq.bkt.clouddn.com/image/jpg/hexo_deploy_success)

## 新建博客：

```
hexo new "blogName"
```
> **Note**:这种方式建立的页面会显示在主页上，新建博客文章也是用的这种方式，默认会在站点的source/_post目录下生成"blogName.md"文件

```
hexo new page "pageName"
```
> **Note**:这种方式建立的是一个新的页面，会在站点source文件夹下生成一个"pageName"文件夹，可以用这种方式建立一个新的"about"页面，然后把about链接链到这里

## MarkDown编辑器：

这种方式搭建的博客，很多时候都是用markdown写博文，这里介绍两款比较好用的markdown编辑器
- [haroopad](http://pad.haroopress.com/user.html)：左边编辑右边预览，有markdown语法的提示，支持Windows、Linux、Mac等平台，这篇博文就是用haroopad完成的
- [StackEdit](https://stackedit.io/)：一款在线的markdown编辑器，有markdown语法提示，能导出为pdf、md、html格式文件，支持同步到google drive

## 主题设置：
hexo init生成的默认主题是landscape，以设置[yilia](https://github.com/litten/hexo-theme-yilia)主题为例（[更多主题](https://github.com/hexojs/hexo/wiki/Themes)）：
- 主题下载：
```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
- 配置：
打开站点下的_config.yml文件，找到theme标签，改为yilia

> **Note**:yilia主题issue[请看这里](https://github.com/litten/hexo-theme-yilia/issues)

## 文章摘要：

在需要显示摘要的地方添加：
```
以上是摘要
<!--more-->
以下是余下全文
```
more以上是文章摘要，在主页显示，more以下内容点击"more"链接才显示（如果不添加摘要，主页将会显示文章的全部内容）

## 网站图标：
制作一个ico图标，命名为favicon.ico并放到工程根目录即站点source目录下。可以在[Faviconer](http://www.faviconer.com/)制作ico图标，国内有[比特虫](http://www.bitbug.net/)

## 域名绑定：
- 打开终端，ping lucienlau.github.io，得到ip
- 申请域名并添加域名解析，记录类型选择A，记录值填刚得到的ip
- 在站点source文件夹下建立CNAME文件，写上自己的域名，然后部署到GitHub Pages
- 几分钟后，应该可以通过自己的域名访问博客了

## 设置自己的评论系统：
由于这是静态网站，评论系统需要用第三方的，以[多说](http://duoshuo.com/)为例：
- 注册多说并安装
![duoshuo_0](http://ocww00tfq.bkt.clouddn.com/image/jpg/duoshuo_0)
![duoshuo_1](http://ocww00tfq.bkt.clouddn.com/image/jpg/duoshuo_1)

- 打开主题下的_config.yml配置文件，找到"duoshuo"标签，改为"test1"，原来的可能是"true"，其实不是"true"或"false"，是作者的就叫"true"

## 网站访问统计：
评论有“多说”，计数有“[不蒜](http://busuanzi.ibruce.info/)”。不蒜子目前还没开放注册，不过仍然可以使用，只需两行代码。比如要在页脚靠左显示访问情况，则修改主题下的layout/_partial/footer.ejs文件（[更多设置](http://ibruce.info/2015/04/04/busuanzi/)）
![footer_left](http://ocww00tfq.bkt.clouddn.com/image/jpg/footer_left)

## 设置博客音乐：
- 登录网易云音乐web版，选择歌曲并按提示生成外链（有的歌有版权，不能生成外链，想选一首周杰伦的，发现由于有版权保护不能生成外链）
![music163_0](http://ocww00tfq.bkt.clouddn.com/image/jpg/music163_0)
![music163_1](http://ocww00tfq.bkt.clouddn.com/image/jpg/music163_1)

- 选择需要插入的位置，比如页脚靠右，则修改主题下的layout/_partial/footer.ejs
![footer_right](http://ocww00tfq.bkt.clouddn.com/image/jpg/footer_right)


## 使用图床：
图床其实就是存放图片等的地方，GitHub提供的空间有限，可以把一些图片等放到别的地方，以[七牛云](http://www.qiniu.com/)为例，注册七牛云，上传图片，七牛云会为每张图片提供一个外链，然后在需要显示图片的地方插入链接即可

## 参考链接：
- *[http://sunwhut.com/2015/10/30/buildBlog/](http://sunwhut.com/2015/10/30/buildBlog/)*
- *[http://jiji262.github.io/2016/04/15/2016-04-15-hexo-github-pages-blog/](http://jiji262.github.io/2016/04/15/2016-04-15-hexo-github-pages-blog/)*
- *[http://ibruce.info/2013/11/22/hexo-your-blog/](http://ibruce.info/2013/11/22/hexo-your-blog/)*
- *[http://fupinyou.com/2016/02/19/%E4%BD%BF%E7%94%A8hexo-github%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/](http://fupinyou.com/2016/02/19/%E4%BD%BF%E7%94%A8hexo-github%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)*
- *[http://blog.csdn.net/hitwhylz/article/details/42646197](http://blog.csdn.net/hitwhylz/article/details/42646197)*
- *[http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html](http://www.cnblogs.com/purediy/archive/2013/03/07/2948892.html)*

