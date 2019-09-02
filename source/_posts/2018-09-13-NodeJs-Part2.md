---
title: 好玩的Nodejs —— 安装和配置 Node.js
date: 2018-09-13 08:33:54
tags: Nodejs
categories: 
	- Nodejs
---



## 安装和配置 Node.js

下载安装还是挺简单的，点击[NodeJs](http://nodejs.org)，找到download 和 与计算机对应的操作系统进行下载安装即可。

### 编译源代码

Node.js 从 0.6 版本开始已经实现了源代码级别的跨平台，因此我们可以使用不同的编译命令将同一份源代码的基础上编译为不同平台下的原生可执行代码。在编译之前，要先获取源码包。建议访问http://nodejs.org，点击Download链接，然后选择Source Code，下载正式发布的源码包。如果你需要开发中的版本，可以通过
https://github.com/joyent/node/zipball/master 获得，或者在命令行下输入 git clone git://github.com/joyent/node.git 从git获得最新的分支。

#### 在 Windows系统中编译

Node.js 在 Windows 下只能通过 Microsoft Visual Studio 编译，因此你需要首先安装 VisualStudio 或者免费的 Visual Studio Express。你还需要安装 Python 2（2.5以上的版本，但要小于3.0），可以在http://python.org/取得。安装完 Python 以后请确保在 PATH 环境变量中添加python.exe 所在的目录，如果没有则需要手动在“系统属性”中添加。一切准备好以后，打开命令提示符，进入 Node.js 源代码所在的目录进行编译：

> C:\Users\byvoid\node-v0.6.12>vcbuild.bat release
> ['-f', 'msvs', '-G', 'msvs_version=2010', '.\\node.gyp', '-I', '.\\common.gypi', '--depth=.','-Dtarget_Project files generated.
>
> C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\Microsoft.CppBuild.targets(1151,5):warning MSB8012: http_parser.vcxproj -> C:\Users\byvoid\node-v0.6.12\Release\http_parser.libjs2c, and also js2c_experimental
>
> node_js2c
> ...

直接运行 node.exe 即可进入 Node.js 的交互模式，在系统  PATH 环境变量中添加node.exe文件所在的目录，这样就可以在命令行中运行  node 命令了，剩下的工作就是手动安装 npm 了。



### 安装 Node 包管理器

Node 包管理器（npm）是一个由 Node.js 官方提供的第三方包管理工具.npm 是一个完全由 JavaScript 实现的命令行工具，通过 Node.js 执行，因此严格来讲它不属于 Node.js 的一部分。我们在 Windows、Mac 上安装包和源代码包时会自动同时安装 npm。

http://npmjs.org/提供了 npm 几种不同的安装方法，通常你只需要执行以下命令：

> curl http://npmjs.org/install.sh | sh

如果安装过程中出现了权限问题，那么需要在 root 权限下执行上面的语句，或者使用 sudo 。

> curl http://npmjs.org/install.sh | sudo sh

其他安装方法，譬如从 git 中获取 npm 的最新分支，可以参考 http://npmjs.org/doc/README.html上的说明。



另外还可以上[菜鸟编程](http://www.runoob.com/nodejs/nodejs-install-setup.html)查看安装配置方法，简单明了。