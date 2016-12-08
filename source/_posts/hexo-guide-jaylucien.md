---
title: hexo博客搭建过程记录
---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=30431370&auto=0&height=66"></iframe>
hexo是一个静态博客框架，本篇文章记录了yilia主题的hexo博客搭建和配置过程，本地环境基于MacOS，Windows和Linux环境大同小异，在此特别感谢hexo和yilia等主题的作者，传送门：
Hexo：https://hexo.io/
yillia：https://github.com/litten/hexo-theme-yilia

<!--more-->

#### 1. 安装NodeJs
Mac OS官网下载：https://nodejs.org/dist/v6.9.1/node-v6.9.1.pkg

#### 2. 创建代码仓库备份博客
以Github为例
##### 2.1 Github上创建仓库
具体操作移步https://github.com/，这里仓库名hexo.backup为例
##### 2.2 生成SSH密钥
命令行执行：
```bash
$ cd ~
$ ssh-keygen -t rsa -C "youremail@example.com"
```
如果默认跳过用户密码配置，在~/.ssh目录下会生成公钥文件`id_rsa.pub`，输出并复制公钥内容
```bash
$ cat ~/.ssh/id_rsa.pub
```
#####2.3 Github登记公钥内容
右上角头像 -> Settings -> SSH and GPG keys -> new SHH key
填写相应的表单提交即可

#### 2.4 本地配置Github用户名和邮箱
```bash
git config --global user.name "yourname"
git config --global user.email "youremail"
```

#### 2.5 验证
```bash
$ git clone git@github.com:yourname/hexo.backup.git
```
如果成功，则在当前目录下生成了hexo.backup目录

#### 3. 安装Hexo模块
首先帮npm换个国内源，默认国外源很慢
```bash
$ npm config set registry https://registry.npm.taobao.org
```
输出当前源
```bash
$ npm config get registry
```
安装hexo
```bash
$ cd hexo.backup
$ npm install hexo --save
```
初始化
```bash
$ hexo init
```

部署本地测试
```bash
$ hexo server
```
浏览器输入localhost:4000，如果出现hexo的博客首页，则说明配置完成了

#### 4. 安装和配置主题

hexo的主目录下面，`/themes`目录是存放hexo主题的目录，我们需要的主题放在这里面就好了，例如，本站的是yillia主题，下载方式为：
```bash
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
使得主题生效，hexo根目录进入`_config.yml`
```bash
theme: yilia
```
再次运行`hexo server`，就发现博客主题变了，更详细的主题配置请参考
https://github.com/litten/hexo-theme-yilia

#### 5. 将hexo部署到github page上
首先在github上创建一个仓库，名称**必须是**yourusername.github.io，然后这个仓库专门作为静态网页的部署仓库

然后在hexo根目录下载hexo的git模块
```bash
$ npm install hexo-deployer-git --save
```
进入主目录下的`_config.yml`， 配置如下设置
```bash
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

然后在命令行运行
```bash
$ hexo clean
$ hexo generate
$ hexo deploy
```
完成操作后，浏览器输入仓库的地址：
```
yourusername.github.io
```
发现如果出现博客首页，那么说明配置成功，这是github为那些项目介绍推出的一个静态页面，用它来做hexo博客的空间是OK的，但有时国内速度很慢，如果需要达到较好的访问体验，建议租用服务器来做静态页面部署的空间，选择有很多，当然要考虑你的需求做出选择，太专业的服务器当然是用不上的，然后还有一些域名的购买，DNS解析等工作，然后就可以开心地访问自己的博客了，例如本站的域名是jaylucien.pw。

但是，这里面有个坑还是要注意的，一般服务器服务的提供商，经常将域名的管理放在自己公司，也就是说，你买的域名最好从服务器提供商那里购买，如果服务器和域名的购买不在一个公司，那花费可能会变高了，比如你想从A公司那里租用服务器，但是一看，同样的域名，B公司要比A公司便宜，然后你在B公司购买了域名，心中大喜，然而突然发现还有域名绑定等等工作，要求只能从A公司里面选域名，发现有域名转入的选项，然而又要一笔转入费用，几乎赶上域名购买价格了，这不是坑爹吗。。。只好乖乖在A公司买了新的域名，B的公司买的只能作废不用了。。。对了是jaylucien.me，看到这篇文章有想买二手的可以留言。

回归正题，该如何部署到自己的服务器上呢？前提是你的服务器上已经有专门的静态网站根目录，看下一步。

#### 6. 将hexo部署到服务器上

这个步骤一般是通过ftp传输来完成的，弄清楚自己服务器的网站根目录、ftp用户名和密码，安装ftpsync模块：

```bash
$ npm install hexo-deployer-ftpsync --save
```

进入`_config.yml`，配置
```bash
deploy:
  type: ftpsync
  host: <host> //服务器地址
  user: <user> // ftp用户名
  pass: <password> // ftp密码
  remote: [remote] // 你的网站根目录
  port: [port] // ftp端口，默认21
  ignore: [ignore] // 忽略的文件夹
  connections: [connections]
  verbose: [true|false]
```

然后运行：
```bash
$ hexo clean && hexo generate && hexo deploy
```

打开域名网址，发现有了博客，成功！

#### 7. 备份博客到github
想在实验室和寝室里都能写博客，没地方同步怎么办？

一开始认为，不是只要git push origin master，然后需要的时候git pull不就好了吗？其实不然，虽然我们的hexo根目录下有了.git文件，与github有了关联，但是由于存在.gitignore的原因，导致上传hexo文件时候，我们需要的文件夹是不会被上传的，还有themes/yillia的.git存在，导致主题也不能同步，解决办法：

```bash
$ rm -rf .gitignore
$ rm -rf ./thems/yilia/.git
$ git add .
$ git commit -m "sth"
$ git push origin
```

再到github的backup仓库看，发现文章目录`/source/_post`,npm模块和`./themes/yilia`都是有的，不为空，那么博客算是备份完了，在新的环境上（当然这个环境指的是安装过hexo，nodejs的环境），只要
```bash
$ git pull
```
博客的内容就被同步下来了，当然每次修改完，除了部署到网页，记得
```bash
$ git push origin master
```
嫌麻烦，这些都可以写个脚本代替


#### 8. 写文章

文章目录
```
$ ./source/_post
```
文章用markdown语法写，存成.md文件，具体语法参考官网，编辑器有很多，我的是马克飞象，具体markdown格式对照https://github.com/JayGo/hexo.backup上的`source/_post`目录下的文章，兼容html语法，所以可以插入音乐外链等小玩意儿。

好了，搭建教程在此介绍完毕。