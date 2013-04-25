---
layout: post
title: "Octopress环境配置 Ubuntu12.04"
date: 2013-04-24 07:57
comments: true
categories: 
---

**Octopress**环境配置起来实在是麻烦，估计是不熟悉ruby环境的原因。

目前没碰到一个可以从头跟到尾就可以使用的教程。所以还是自己写一遍顺便整理下。



####1.安装

**基本软件包，包括git还有一些基本的编译环境。**
```
sudo apt-get update
sudo apt-get install bash curl git-core -y
sudo apt-get install build-essential bison openssl libreadline6 libreadline6-dev zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake rvm install 1.9.2 -C --with-iconv-dir=$HOME/.rvm/usr -y
```
**安装rvm**
```
bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
```

**使rvm成为function**
```
echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # Load RVM function' >> ~/.bashrc
source .bashrc
```

**安装octopress环境**
```
rvm pkg install openssl
rvm pkg install iconv
rvm install ruby-1.9.3-p392 -C --with-iconv-dir=$HOME/.rvm/usr
rvm use 1.9.3 --default
rvm reload
rvm rubygems latest
gem install bundler
```

**下载octopress**
```
git clone git://github.com/imathis/octopress.git octopress

```
进入octopress文件夹后提示
```
You are using '.rvmrc', it requires trusting, it is slower and it is not compatible with other ruby managers,
you can switch to '.ruby-version' using 'rvm rvmrc to [.]ruby-version'
or ignore this warnings with 'rvm rvmrc warning ignore /home/macrov/octopress/.rvmrc',
'.rvmrc' will continue to be the default project file in RVM 1 and RVM 2,
to ignore the warning for all files run 'rvm rvmrc warning ignore all.rvmrcs'.

********************************************************************************
* NOTICE                                                                       *
********************************************************************************
* RVM has encountered a new or modified .rvmrc file in the current directory,  *
* this is a shell script and therefore may contain any shell commands.         *
*                                                                              *
* Examine the contents of this file carefully to be sure the contents are      *
* safe before trusting it!                                                     *
* Do you wish to trust '/home/macrov/octopress/.rvmrc'?                        *
* Choose v[iew] below to view the contents                                     *
********************************************************************************
y[es], n[o], v[iew], c[ancel]>
```
输入y回车。

安装octopress环境
```
gem install bundler
bundle install
```

安装octopress默认主题。
```
rake install
```

####2.写作

添加新文章
```
rake new_post["title"]
```
然后编写、生成、预览。
```
rake generate
rake preview
```
预览会启动一个webserver，可以打开浏览器，地址栏输入localhost:4000查看你的blog。

写做里面会有很多小技巧，单独写一篇。


####3.部署

现在开始部署到github pages上。
输入
```
rake setup_github_pages
```
按照提示输入你的github pages仓库地址，比如我的：
不知道是不是我的比较早，名字还是com结尾，现在都已经转到.io域名，但没试过用io能不能找到仓库。
```
Enter the read/write url for your repository
(For example, 'git@github.com:your_username/your_username.github.io)
Repository url: 'git@github.com:macrov/macrov.github.com
```
这里需要你自己把ssh的密钥对配置好，并且把公玥正确粘贴到github上。

然后运行
```
rake deploy
```
就会push到你github的pages仓库上。

你就可以用yourname.github.com访问你的博客了。

####4.环境保存

github pages会使用master分支中的文件当作网站内容，然后octopress是工作在source分支，你的文章和部署的主题都保存在source文件夹，这些东西要保存好，以便以后多个设备编辑或者换环境。不参与octopress开发就直接保存在source分支然后一起push到github。

不保存好原稿你以后可能会很痛苦，所以这部一定要做。
（我就是中间段了好久，工作也换了，电脑环境也没有了，因为没有保存原稿，想重新开始写就要费很大力气把以前的文章从html再导成markdown，最后我放弃，然后以前的文章就都没了。）

```
git add .
git commit -m "init commit"
git push 
```


####5.维护已经存在的octopress

clone你之前的github pages仓库
```
git clone git@github/your_username/your_username.github.com
cd your_username.github.com
git checkout source
bash //加载rvm
```
然后就可以重新rake new_post, rake generate, rake deploy了。（前提是你有按照环境保存把原稿都加到仓库里）

