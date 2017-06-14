---
title: SDLPAL98 仙剑奇侠传98柔情版移植3DS
date: 2017-06-07 23:42:03
tags: [SDL,游戏开发]
categories: 开发
toc: true
---

仙剑奇侠传98柔情版 3DSL 安装测试
### 准备工作

#### 源码：[SDLPAL](https://github.com/sdlpal/sdlpal)

#### 下载仙剑游戏：
   [官方游戏ZIP](http://download.baiyou100.com/Pal98rqp.zip) 
   解压缩至TF卡路径： ```TF:/3ds/sdlpal/```

#### 设备：
	
	3DS A9HL 一台 
	3ds安装FBI用于安装CIA
	3ds安装Homebrew Lancher。dsp dump解决游戏运行无声音问题。
	3ds安装bootNtr3.5。 3.5内置锁定3ds cpn L2 cache功能。解决游戏运行因降频而卡顿的问题	
<!-- more --> 
#### 相关资源:  
[HBL官网](http://smealum.github.io/3ds/)

[FBI Github](https://github.com/Steveice10/FBI.git)

[相关CIA—提取码—yfts](链接:http://pan.baidu.com/s/1o8BdOvC)

### 游戏简介
XianJian QiXia Zhuan 仙劍奇俠傳 (also known as The Legend of Sword and Fairy and “PAL”) is an action RPG created by Taiwan's Softstar Entertainment Inc. The game is considered by many as simply one of the best Chinese RPG ever created. Since its first release in 1995 (DOS Version), >many new versions and sequels (including a prequel) were created. However, none could replace the breath-taking and tragic tale it was born from. Here, we meet our protagonist Li Xiaoyao; an aspiring martial artist and the lovely Zhao Ling'er, a charismatic Nuwa descendant.​
Now this game has been ported to Nintendo 3DS! In both Chinese and non-official English! This game is also on titledb. Compatible with both o3DS and n3DS.


### 解决游戏运行无声音的问题

	下载DspDump.3dsx  、 boot.3dsx  、hblauncher_loader.cia
	
	1.将下载好的DspDump.3dsx放入内存卡根目录的“3ds”文件夹内，boot.3dsx放内存卡根目录，
		hblauncher_loader.cia放内存卡
	2.利用FBI安装hblanuncher_loader.cia。
	3.打开3ds 联网 ，连接至wifi保证网络通畅
	4.进入hblauncher，找到DspDump后按A进入，提示完成后按Start键
	5.没有声音可继续尝试以上步骤

### 解决游戏运行卡顿的问题
	安装bootNtr ,打开锁定CPU L2 Cache即可