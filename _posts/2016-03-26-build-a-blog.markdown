---
layout:     post
title:      "Github Pages + Jekyll 独立博客一小时快速搭建&上线指南"
subtitle:   "分享你的知识和观点"
date:       2016-03-26
author:     "Bebop.C"
header-img: "img/post-build-a-blog.jpg"
tags:
    - Git
    - 建站
---



## 只要一小时？！


> 人生道路上布满了坑，于是有了人生导师。  
美丽的地球上布满了坑，于是有了Google Earth。  
使用Github Pages搭建独立博客的过程中布满了坑，所以有了这篇指南。

我在自己查找资料搭建的过程中发现了许多大大小小的坑，大部分是因为软件版本、技术的升级、网络环境的影响、设计方案的变化、操作顺序的不当而引起，于是想整理一篇快速而准确的攻略以供各种同我一样的小白也能轻松获取知识，搭建自己知识分享的平台。

这篇文章能帮助你一小时快速上线（当然要在网络环境良好的情况下，有时候网络环境不好可能需要自备梯子），如果不想自己折腾，直接fork模版，就可以写文章了，如果想把自己的博客打理得更符合自己的口味儿，大家还需要多多学习和实践。

此篇攻略尽力做到清晰明了，但也必有不足之处，遇到问题欢迎大家多多留言交流提问(´・ω・)ﾉ。

---

**版权声明：欢迎分享，转载请注明出处。**

## 目录


1.  [Why Github Pages + Jekyll](#why-github-pages--jekyll)
2.  [注册Github](#github)
3.  [关于域名](#section-2)
4.  [安装Git环境](#git)
5.  [建立个人GitHub Pages](#github-pages-1)
6.  [写博客](#section-7)
7.  [进阶：安装Jekyll本地编译环境](#jekyll-1)
8.  [进阶：添加评论、分享功能](#section-12)
9.  [扩展阅读](#section-13)


## Why Github Pages + Jekyll

### Git & Github

Git是一种分布式版本控制系统，由大名鼎鼎的Linux操作系统创建人Linus Torvalds编写，当时的目的是用来管理Linux内核的源码。  
Github是全球知名的使用Git系统的一个免费远程仓库（Repository），倡导开源理念，若要将自己的代码仓库私有则需付费。

### GitHub Pages

托管在Github上的项目页面看上去往往是这样：
![what-geek-say](http://img.blog.csdn.net/20160326173234333)
<small class="img-hint">What does the geek say?!</small>

对于非专业人士看到这样的页面有80%只能默默点叉了。

于是GitHub Pages诞生了。它是Github提供给托管项目的开发者一个更个性化展示自己项目的方法，使用GitHub Pages服务可以编写同样是托管在Github上的静态网页。

比如上面Jekyll项目的GitHub Pages长成这样：
![jekyll](http://img.blog.csdn.net/20160326173317122)

Github Pages分为用户、组织、项目三种网站，我们的Blog要用到的是 [User Pages site](https://help.github.com/articles/user-organization-and-project-pages/) ，即用户网站。

### Jekyll

下面是来自 [Jekyll中文文档站](http://jekyll.bootcss.com/) 的介绍：

> 将纯文本转化为静态网站和博客。  
简单 -- 无需数据库、评论功能，不需要不断的更新版本——只用关心你的博客内容。  
静态 -- 只用 Markdown (或Textile)、Liquid、HTML & CSS 就可以构建可部署的静态网站。

我们遵循Jekyll规范编写网站源码，上传到Github上，Github会自动进行编译出最终的网站文件，给你一个新世界！

Tip: 如果本地[安装Jekyll编译环境](#jekyll-1)，便可实时预览网站，不必每修改一点都要经过上传刷新才能看到结果，大大方便了我们的调试。

### Github Pages + Jekyll 方案适合我吗？

Github Pages + Jekyll 方案的优点：

> Github免费托管源文件，自动编译符合Jekyll规范的网站。  
引入版本管理，修改网站更加安全方便。  
支持 [Markdown](http://sspai.com/25137) ，编写具有优美排版的文章。

Github Pages + Jekyll 方案的不足：

> 需要学习一些基础的Git命令。  
若要自己全权制作主题的话需要懂一点网页开发。  
由于生成的是静态网页，若要使用动态功能，如评论功能（下文解决），则要使用第三方服务。

所以，如果你只是想做一个分享见闻心得的博客，这个方案非常适合你。

## 注册Github

到 [Github的官方网站](https://github.com/) 注册账户，记住自己的用户名，后面会常用到。

Tip: 有时网络状况不好可以喝杯牛奶再来。

## 关于域名

### 符合下面条件，可以跳过此部分：  

1.已有域名。  

2.不想使用独立域名，直接使用Github Pages提供的域名访问：

```
http://{username}.github.io         //用你的Github用户名替换网址中的{username}`
```

### 购买域名

之所以把购买域名放在前面，是因为购买和DNS配置过后可能会有服务延迟，为了让大家尽快看到自己的成果，可以先买域名。

大家可以自由选择域名注册商，这里介绍GoDaddy上的购买流程。

进入[GoDaddy中文网站](https://sg.godaddy.com/zh/) （当然英文也可以），在搜索框输入一个霸气的名字，点击“域名搜索”。
![buy-domain-01](http://img.blog.csdn.net/20160326173353302)

如果幸运的话，还没有人注册过这个域名。  
我们只需购买一个 `.com` 域名即可，点击第一个“选择”，再点击“进入购物车”。
![buy-domain-02](http://img.blog.csdn.net/20160326173415233)

出现GoDaddy的增值服务，我们不需要的话，直接点击右上角的“进入购物车”。
![buy-domain-03](http://img.blog.csdn.net/20160326173453781)

到了购买页面，可以自由选择购买期限，默认两年，不同时间优惠不同。  
注意这里右侧有个地方可以添加“促销码”，大家可以用搜索引擎找找，有很多可用的优惠，不同的促销码可能会对支付方式和年份等有所限制（无限制时可以使用支付宝、信用卡、银联银行卡等）。
![buy-domain-04](http://img.blog.csdn.net/20160326173512802)

认真核对好购买项目和金额后，点击“去付款”，进行信息注册和结算。  
**注意！邮箱名字很重要，后期激活和修改密码都必须通过邮箱，请确保无误，可不要让银子白白流走啊。**  
购买完成后去刚才填写的邮箱收信激活。

### 设置DNS

重新登录你刚注册的帐号，点击右上角用户中心里的“我的产品”。
![buy-domain-05](http://img.blog.csdn.net/20160326173540927)

此时已经可以看到我们购买的域名了。

为了防止不可控因素导致GoDaddy的域名服务器无法访问，我们可以把域名解析的任务转给国内稳定的域名服务商。  
这里推荐使用 [DNSPod](https://www.dnspod.cn/) 的服务，稳定且免费。

在“我的产品”界面找到刚才注册的域名，点击“管理DNS”。
![buy-domain-06](http://img.blog.csdn.net/20160326173556891)

在“域名服务器” 处点击“管理”。
![buy-domain-07](http://img.blog.csdn.net/20160326173618318)

按照下图设置好DNSPod的域名服务器。
![buy-domain-08](http://img.blog.csdn.net/20160326173632522)

设置好后进入 [DNSPod](https://www.dnspod.cn/)  
注册帐号后登录，进入“域名解析”，点击“添加域名”。
![set-dns-01](http://img.blog.csdn.net/20160326173650850)

添加好之前购买的域名，点击你的域名进入记录管理。按照下图添加每条记录。
![set-dns-02](http://img.blog.csdn.net/20160326173714834)

前两条A型记录值 `192.30.252.153` 和 `192.30.252.154` 是GitHub的服务器地址，可以在[这里](https://help.github.com/articles/troubleshooting-custom-domains/#dns-configuration-errors)查到。  
NS类型记录是默认不可变的。  
CNAME类型的记录值填写 `{username}.github.io`  
Tip: 用你的Github用户名替换上面的 `{username}`

## 安装Git环境


> 本文主要介绍windows环境下的安装，其他环境可参考[廖大神的教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)。

### 安装git for windows
[进入下载页面](https://git-for-windows.github.io/)  
安装好以后在开始菜单中找到Git Bash并打开。
![setup-git](http://img.blog.csdn.net/20160326173741565)

在打开的命令行窗口（`Shell`）内执行以下命令，设置你的git用户名和邮箱：

```
$ git config --global user.name "{username}"          // 用你的用户名替换{username}
$ git config --global user.email "{name@site.com}"    // 用你的邮箱替换{name@site.com}
```

### SSH配置

为了和Github的远程仓库进行传输，需要进行SSH加密设置。

在刚才打开的Shell内执行：

```
$ ssh-keygen -t rsa -C"{name@site.com}"    // 用你的邮箱替换{name@site.com}
```

可以不输入其他信息，一直敲回车直到命令完成。 
这时你的用户目录（win7以上系统默认在 `C:\Users\你的计算机用户名`）内会出现名为 `.ssh` 的文件夹，点进去能看到 `id_rsa` 和 `id_rsa.pub` 两个文件，其中 `id_rsa` 是私钥，不能让怪人拿走， `id_rsa.pub` 是公钥，无需保密（原理请自行参看密码学.............................................我相信你也不会看）。

接下来用你的浏览器登录Github，点击右上角的“Settings”：
![set-ssh-01](http://img.blog.csdn.net/20160326173806394)

用文字处理软件打开刚才的 `id_rsa.pub` 文件，复制全部内容。  
点击“SSH Keys”，“Add SSH Key”，将复制的内容粘贴在Key中，点“Add Key”确定。
![set-ssh-02](http://img.blog.csdn.net/20160326173821144)
![set-ssh-03](http://img.blog.csdn.net/20160326173829850)

至此SSH配置完毕。

## 建立个人 GitHub Pages

建立基于Jekyll的个人Github Pages有两条路线：  
1.自己学习Jekyll教程和网页设计，设计绝对自我基因的网页。  
2.Fork（*Git系统的创建分支，简单来说是把当前仓库复制一份到你的仓库，你可以进行修改，因为你的仓库是原来仓库的新的分支*）已有的开源博客仓库，在巨人的肩膀上进行符合自我的创作。

建议和我一样的小白们可以从第二条路线学起。

### 挑选“模版”
[Jekyll项目的wiki页面](https://github.com/jekyll/jekyll/wiki/sites)给出了大量优秀的风格各异的网站，这里以 Panxw's blog 为例讲解。

进入wiki页，点击Panxw's blog右边的source链接，进入到作者的模版仓库。
![fork-01](http://img.blog.csdn.net/20160326173920458)

点击右上角的“Fork”。
![fork-02](http://img.blog.csdn.net/20160326173933085)

然后在你的主页里找到你刚才Fork的分支，点进去：
![fork-03](http://img.blog.csdn.net/20160326173943320)

点击“Settings”，将“Repository name”改为 `{你的Github用户名}.github.io`，点击“Rename”。
![fork-04](http://img.blog.csdn.net/20160326173955662)

此时你会发现已经可以通过 `http://{你的Github用户名}.github.io` 访问你fork下来的网站啦！

### 绑定域名

接下来让你能用之前购买的域名来访问你的网站。

在你的代码仓库中找到 `CNAME` 文件，点击它：
![fork-05](http://img.blog.csdn.net/20160326174010242)

点击右方的铅笔图标，把文件内容改成你购买的域名，比如 `www.awesome.com`，这样你就可以用自己的域名访问你的Github Pages啦！
![fork-06](http://img.blog.csdn.net/20160326174023709)

## 写博客

### 同步仓库

我们不可能一切编写都像刚才更改CNAME那样在网页上直接进行，那样会十分受限及效率低下，我们需要把托管在Github上的仓库同步到本地计算机来。

再次打开Git Bash，输入以下命令切换到你想放置本地代码仓库的位置：

```
$ cd {本地路径}     // 比如：cd e:/workspace
```

clone（克隆）你自己的远程仓库：

```
$ git clone https://github.com/{username}/{username}.github.io.git     // 用你的Github用户名替换{username}
```

结果如下：
![clone](http://img.blog.csdn.net/20160326174041711)

这时所有远程仓库里的源码都拷贝到 `e:/workspace/{username}.github.io` 这个文件夹里来了。  
Tip: 如果clone失败有可能是网络原因，可以过一个时段再试，或者寻找梯子。

### 撰写博文

打开本地仓库的 `_posts` 文件夹，你的所有博文都将放在这里，写新博文只需要新建一个标准文件名的文件，在文件中编写文章内容。
比如我们fork的模版中 `_posts` 文件夹里有一篇 `2016-03-23-hello-world.markdown`，你的文件命名也要严格遵循 `年-月-日-文章标题.文档格式` 这样的格式，**尤其要注意月份和日期一定是两位数**。

推荐使用Markdown语言写文章，windows下推荐MarkdownPad这个软件编写Markdown文本。

最开始写可以直接模仿别人的博文语法，更多Markdown语法可参考 [认识与入门Markdown](http://sspai.com/25137)。

### 简单Git命令

> 这里只介绍快速修改上传博客的方法，详细的Git学习可以参考文末给出的扩展阅读。

当你使用Git Bash对你的本地仓库进行操作时，先用 `cd` 命令将你的工作目录设置到你要操作的本地仓库

```
$ cd {你刚才clone下来的项目文件夹路径}
```

每当你对本地仓库里的文件进行了修改，只需在Bash中依次执行以下三个命令即可将修改同步到Github，刷新网站页面就能看到修改后的网页：

```
$ git add .
$ git commit -m "statement"   //此处statement填写此次提交修改的内容，作为日后查阅
$ git push origin master
```

到此你已经可以发表文章到你的个人博客啦！！如有更多需求，可以继续看下面的进阶部分。

## 进阶：安装Jekyll本地编译环境

每次修改了本地文件，都要至少经过三个命令和服务器延迟刷新才能看到修改的效果，是不是有点疼？

如果你觉得“疼！” 那么这部分就是你的解药。

### 环境安装

> 本文主要介绍windows环境下的安装，Mac和Linux环境下可以使用自带的包管理器进行安装，可参考[这篇文章](http://www.cnblogs.com/daguo/p/4097263.html)。


1.安装 [Ruby](http://rubyinstaller.org/downloads/)
![install-ruby-01](http://img.blog.csdn.net/20160326174105835)

**注意：这里一定要勾选添加到环境变量PATH！**
![install-ruby-02](http://img.blog.csdn.net/20160326174117476)

2.安装 [RubyGems](https://rubygems.org/pages/download)

windows下下载ZIP格式较为方便，下好后解压到本地任意路径，下面以 `{unzip-path}` 代替你解压的路径。
打开windows的cmd终端（按win+R快捷键打开“运行”，输入cmd，确定），输入命令:

```
$ cd {unzip-path}  //如果你没有解压在C盘，windows的终端切换到其他盘需要写为 cd /d {unzip-path}
$ ruby setup.rb
```

3.安装Jekyll

在终端里输入

```
$ gem install jekyll
```

4.安装jekyll-paginate

在终端里输入

```
$ gem install jekyll-paginate
```

如遇到以下错误，说明网络不通：

```
ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
Errno::ECONNRESET: An existing connection was forcibly closed by the remote host.
```

这时候有三种解决方法：
1. 等天气好的时候再来
2. 架梯子
3. 更换为[国内镜像服务器](https://ruby.taobao.org/)

### 开启本地实时预览

上一小节的安装都完成以后，在终端中输入命令

```
$ cd {local repository} // {local repository}替换成你的本地仓库的目录
$ jekyll serve
```

1.如遇到以下错误，在仓库文件 `_config.yml` 中加入一句 `gems: [jekyll-paginate]` 即可。

```
Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in your configuration file. 
```

2.如遇到以下错误

```
jekyll 3.1.2 | Error:  Permission denied - bind(2) for 127.0.0.1:4000
```

说明有程序在占用这个本地端口，这时候输入命令

```
$ netstat -ano
```

可以看到如下进程与所占用端口的对应情况，找到本地地址为 `127.0.0.1:4000` 的记录，看到该条记录的PID为6668 (当然你的和我的不一样)。
![jekyll-serve-fail-01](http://img.blog.csdn.net/20160326174152852)

输入命令

```
$ tasklist /svc /FI "PID eq 6668"
```

该进程的名称就会显示出来:
![jekyll-serve-fail-02](http://img.blog.csdn.net/20160326174207587)

打开windows的任务管理器，结束它：
![jekyll-serve-fail-03](http://img.blog.csdn.net/20160326174221211)

再次运行 `jekyll serve` 就可以了。

如果一切顺利，通过在浏览器地址栏输入 `http://localhost:4000/` 回车就已经可以看到自己网站的模样啦。  
只要 `jekyll serve` 服务开着，你的本地仓库文件有任何更新，本地网站刷新都能马上看到，欧耶！

### 编写符合Jekyll规范的网站

如要自己修改fork下来的主题，可以参考以下资料：  
[使用Github Pages建独立博客](http://beiyuu.com/github-pages/)  
[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)  
[Jekyll中文文档](http://jekyll.bootcss.com/)

## 进阶：添加评论、分享功能

常见的第三方评论系统有： [Disqus](https://disqus.com/)，[多说](http://dev.duoshuo.com/)。

简单来说是在html文件中嵌入Javascript代码，注册网站后都有较好的指导，并不困难。


## 扩展阅读
[1][Github Pages](https://pages.github.com/)  
[2][Git教程 - 廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)  
[3][Jekyll中文文档](http://jekyll.bootcss.com/)  
[4][认识与入门Markdown](http://sspai.com/2513)  
[5][Ruby和Gem](http://blacktha.com/2015/07/06/tech/Ruby/)  
[6][使用Github Pages建独立博客](http://beiyuu.com/github-pages/)  
[7][搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)

