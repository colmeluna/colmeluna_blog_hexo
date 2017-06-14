---
title: CentOS7 安装nvm
date: 2017-06-10 01:15:11
tags: Node.js
categories: 开发
toc: true
---

### NVM的简介
NVM 用于管理Node.js版本,可在系统环境内切换node.js各个版本。
    
   [Nvm github看这里](https://github.com/creationix/nvm.git)
    
注：windows环境使用nvmw。


### 获取nvm源码

1、直接从 github 找到最新版本通过wget下载到本地
    
    wget https://github.com/cnpm/nvm/archive/v0.23.0.tar.gz

2、或者直接克隆到本地通过
    
    git clone https://github.com/cnpm/nvm.git

<!-- more --> 
### 安装nvm

1、安装nvm非常简单，只要解压后进入目录然后执行安装脚本

`./install.sh`

2、安装结束后执行

`source ~/.bash_profile`

使环境变量生效。

3、重新打开你的终端, 输入
 
 `nvm -v`。

### 使用nvm

通过nvm安装管理nodejs基础操作

列出所有可安装的版本
    
   `nvm list-remote`

安装相应的版本:例安装v6.2.1
    
   `nvm install v6.2.1`

查看一下你当前已经安装的版本:
    
   `nvm ls`

切换版本：
    
   `nvm use v7.5.0`

设置默认版本 
    
   `nvm alias default v6.2.1`

加速nvm（使用淘宝镜像）
	
   `NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node nvm install v4.5.0`

### 可能遇到的问题

nvm command not found 的问题
手动加入环境变量
    
   `vim ~/.bash_profile`
将

```# nvm evn setting
	export NVM_DIR=~/.nvm
	[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"```
	
加到` ~/.bash_profile` 文件内