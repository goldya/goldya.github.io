Github+Hexo 搭建博客过程（TM加码）

## 1、 介绍

Github：先理解Git，Git是一个分布式版本管理系统，简单理解为项目开发中代码的管理工具。而Github是基于Git开发的一个项目托管平台。Github提供Github pages服务，Github Pages可以被认为是用户编写的、托管在github上的静态网页。本意是用于介绍自己托管在Github上的项目，常用来搭建个人博客。可以免费托管网站、绑定自己的域名、支持静态脚本、理想的Geek写作环境。

Hexo：Hexo是一款基于Node.js开发的静态博客框架。生成速度快、简单。支持markdown，支持多进程。

## 2、 搭建环境

下载Git、Node.js

Git：https://git-scm.com/   (必选)

Node.js：https://nodejs.org/en/
(由于hexo是基于Node.js的第三方模块，所以必须安装Node.js环境。)

Notepad++：https://notepad-plus-plus.org/
（文本编辑器，用于修改一些二进制配置文件，可选）

## 3、搭建Hexo本地博客

Hexo搭建的过程非常简单，只需要在你喜欢的文件夹中，进入Git Bash，
输入以下3条命令:

	npm install -g hexo 安装hexo依赖包
	hexo init初始化hexo
	hexo generate 生成静态页面

做到这里，其实博客已经创建成功了，打开本地服务，来看看博客长什么样子。
（hexo博客都有默认的主题，后期可以自己修改。）

	hexo server 打开本地服务
浏览器输入 localhost:4000
（如果4000端口被占，还可以自己设置博客服务的端口，如 hexo server 8080， 8080为设定的端口）
如果没有意外的话，已经可以看到博客啦~

	hexo new "new page"	//创建新文章

然后在source/_posts/下找到新创建的md文件，直接编辑md文件，保存，部署，就可以看到新博客了。

## 4、 TM加码

由于hexo每次部署都会根据themes文件下的js文件重新生成html，放在public中。所以在博客的html文件中加码的话会被直接覆盖。于是在hexo的加码方式是：

1、编辑文件themes/lanscape/_config.yml，添加一行配置代码：

    tagmanager： true

2、新建文件themes/lanscape/layout/_partial/tagmanager.ejs，内容如下：

	<% if (theme.tagmanager) { %> //这里tagmanager名称需要三份一致
	// 申请好的TM代码（或者其他统计代码）
	<% } %>

3、编辑themes/lanscape/layout/_partial/head.ejs文件，在</head>之前添加代码：

	<%- partial('tagmanager') %>


4、再部署发布，试试看能不能看到数据~

## 5、 部署到Github

首先需要注册一个Github账号： https://github.com/

根据自己的用户名创建仓库，用来存放博客，仓库名固定为： 用户名.github.io

需要加入ssh密钥，之后再使用github就再不用输用户名密码了

##### ssh加入方法：

打开gitbash：

先检测电脑是否已经存在ssh_key：
	
	ls -al ~/.ssh	

如果没有存在，则重新生成：

	ssh-keygen -t rsa -C "your_email@example.com"

一路回车

根据提示在对应的位置找到保存ssh_key的地方，找到id_rsa.pub，复制全部内容。

打开创建好的仓库，点击setting->deploy keys-> Add deployer key, 把复制好的全部内容放在key内，取个title，保存。（还需要输入一次github密码）

##### 部署gituhb

打开hexo的全局配置文件，在本地创建博客的根目录下,

	deploy: 
		type: git
		repository: git@github.com:name/name.github.io.git
		branch: master
	//注意每一冒号后面都要带空格

在gitbash中敲入命令：

	npm install hexo-deployer-git --save

由于第一次使用git，有可能需要先设置git使用者的名字和邮箱：

	git config --global user.name [username]
	git config --global user.email [email]

设置完之后，开始部署:

	hexo generate //将markdown文件生成静态页面
	hexo deploy	//将静态页面自动push到github上

每次修改后，都需要重新部署。

至此，打开你的name.github.io 地址，查看博客吧~



## 6、 优化博客

其实到这一步，博客的整体搭建都完成了，加代码的方式也介绍了。博客搭建了，就要用起来咯。如果我在家里写博客，我在公司也想写博客，怎么办？

github上master分支存放的是html页面文件，是每次部署后都会重新生成的，对于博客的多端来说，没有太大的意义。而只要再创建个分支，专门来存放md文件和博客的一些必要文件，就可以在每次需要修改博客时保持多端统一啦。

（git特性：无限创建分支，分支间独立，互不干扰）

##### 实现博客多端

假设完成上面步骤的是电脑A，而现在需要在电脑B写博：

电脑A：

在博客根目录进入git bash：

	git init //初始化仓库
	git checkout -b hexo //创建hexo分支并切换到hexo

可以找到.gitignore里已经有默认的忽略文件，所以直接提交到git就可以了
	
	git status  // 查看git状态，可以看到hexo的源文件
	git add .  
	git commit -m "commit message" // 把文件提交到git中


这个时候，提交仍在本地，需要提交到远程仓库，也就是你的github上。

	git remote add origin git@github.com:name/name.github.io.git // 关联远程仓库
	git push origin hexo // 相当于在远程仓库创建了hexo分支，联系着本地hexo分支。 


push成功后，博客源文件	就已经在github上了。在电脑B的操作，就是把github上的仓库clone到本地，重新安装hexo依赖环境，即可实现博客多端。

电脑B：

	git clone -b hexo git@github.com:name/name.github.io.git
	npm install -g hexo
	
如果本地localhost能够正常看到博客，就说明电脑B的博客也搭建好啦。

在以后的写博过程中，只要保证本地和远程的进度一致，修改本地后，提交修改到远程，多端就不会有冲突的情况了。

	git pull //拉取远程最新修改
	// 写博操作
	// add commit 
	// git push origin hexo
	// 完成
