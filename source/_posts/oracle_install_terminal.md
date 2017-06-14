---
title: oracle11g 非图形化静默安装
date: 2017-06-04 23:42:02
tags: Oracle
categories: 开发
toc: ture
---
oracle11g 非图形化静默安装

> “注意安装前检查最新的安装包与当前系统版本兼容性。避免踩坑。 ”


## 前置工作

### 安装思路

* 通过SSH远程连接云主机，上传oracle11g安装包，在centos6.5上无图形化界面静默安装oracle11g。

### 准备
<!-- more --> 
* 本地环境windows7+ssh远程连接工具xShell  
* 远程云主机CentOS6.5 64位系统  
* 安装包文件linux.x64_11gR2_database_1of2.zip、linux.x64_11gR2_database_2of2.zip

### 云主机
#### 内存：
    * 最小内存1G，推荐2G或2G以上。1G相对卡机，2G有所改善。
#### 交换空间: 
    * 1GB跟2GB物理内存之间的，设定swap大小为物理内存的1.5倍
    * 2GB跟16GB物理内存之间的，设置swap大小与物理内存相等
    * 16GB物理内存以上的，设置swap大小为16GB
#### 硬盘:
    * 要求空间至少5-6GB

## 安装步骤：


#### 安装依赖包　　　　　　　
`yum -y install binutils compat-libstdc++-33 compat-libstdc++-33.i686 elfutils-libelf elfutils-libelf-devel gcc gcc-c++ 
    glibc glibc.i686 glibc-common glibc-devel glibc-devel.i686 glibc-headers ksh libaio libaio.i686 libaio-devel libaio-devel.i686 
    libgcc libgcc.i686 libstdc++ libstdc++.i686 libstdc++-devel make sysstat unixODBC unixODBC-devel`

#### 设定swap空间　

在/home/下生成swap文件 设定大小2GB

    dd if=/dev/zero of=/home/swapfile bs=1M count=2048

设定使用/home/swapfile交换文件

    mkswap /home/swapfile

启用交换分区

    swapon /home/swapfile

编辑文件/ect/fstab 

    vi /etc/fstab

为了引导系统时启用交换文件，文件最下方插入
    
    /home/swapfile swap swap defaults 0 0
 

#### 添加oracle用户

 创建oinstall、dba组 将oracle用户加入组 修改并设定oracle用户密码

    groupadd oinstall
    groupadd dba
    useradd -g oinstall -G dba oracle
    passwd oracle
 
#### 修改内核参数  
 
 编辑文件/etc/sysctl.conf　　

    vi /etc/sysctl.conf

 配置文件内加入 修改以下参数。如果没有可以自己添加，如果默认值比参考值大，则不需要修改。

    fs.aio-max-nr = 1048576
    fs.file-max = 6815744
    kernel.shmall = 2097152
    kernel.shmmax = 536870912
    kernel.shmmni = 4096
    kernel.sem = 250 32000 100 128
    net.ipv4.ip_local_port_range = 9000 65500
    net.core.rmem_default = 262144
    net.core.rmem_max = 4194304
    net.core.wmem_default = 262144
    net.core.wmem_max = 1048586
  
 执行命令sysctl使其自检并生效

    sysctl -p
 

#### 修改用户资源限制 

 1.修改/etc/security/limits.conf配置文件

    vi /etc/security/limits.conf

 2.配置文件下方加入

    oracle              soft    nproc  2047
    oracle              hard    nproc  16384
    oracle              soft    nofile  1024
    oracle              hard    nofile  65536
    oracle              soft    stack   10240

 3.修改/etc/pam.d/login配置文件

    vi /etc/pam.d/login  
  
 4.配置文件内加入

    session required /lib/security/pam_limits.so 
    session required pam_limits.so
 

#### 创建安装目录

 创建安装目录  /usr/local/oracle     /usr/local/oraInventory     /usr/local/oradata  并赋予组用户及权限
    
    mkdir -p /usr/local/oracle /usr/local/oraInventory /usr/local/oradata/
    chown -R oracle:oinstall /usr/local/oracle /usr/local/oraInventory /usr/local/oradata/
    chmod -R 775 /usr/local/oracle /usr/local/oraInventory /usr/local/oradata/
  

#### 创建oraInst.loc文件

 创建/etc/oraInst.loc文件

    vi /etc/oraInst.loc

 文件内加入以下内容
    
    inventory_loc=/usr/local/oraInventory
    inst_group=oinstall

 保存退出后执行以下命令。设定该文件的用户组及权限。

    chown oracle:oinstall /etc/oraInst.loc
    chmod 664 /etc/oraInst.loc
 

#### 上传安装包

 解压缩命令（没有安装Unzip请自行安装）
    
    cd /home
    unzip linux.x64_11gR2_database_1of2.zip
    unzip linux.x64_11gR2_database_2of2.zip

 待解压完毕后会生成文件夹/home/database 修改其用户组及权限此处直接使用777

    chmod 777 /home/database
    chown -R oracle.oinstall /home/database
 

#### db_install.rsp配置。

 1、该文件为静默安装的模板文件，安装流程会按照模板配置信息进行相关安装配置。该文件默认存放在解压后的安装包内，也就是本例中/home/database/response下，
　　 将oracle静默安装所需应答文件全部拷贝至 /usr/local/oracle文件夹下

    cp /home/database/response/* /usr/local/oracle/

 2、修改安装所需的所有应答文件的所属组及权限

    chown  oracle:oinstall /usr/local/oracle*.rsp
    chmod 755 /usr/local/oracle/*.rsp

 3、配置db_install.rsp文件

    vi /usr/local/oracle/db_install.rsp 

 4、文件内修改相应的参数配置如下：

    oracle.install.option=INSTALL_DB_SWONLY     　　　　　　　　//安装类型,只装数据库软件
    ORACLE_HOSTNAME=db             　　　　　　　　　　　　　　　 //主机名称（命令hostname查询）
    UNIX_GROUP_NAME=oinstall       　　　　　　　　　　　　　　　 // 安装组
    INVENTORY_LOCATION=/usr/local/oraInventory  　　　　　　　　//INVENTORY目录（**不填就是默认值,本例此处需修改,因个人创建安装目录而定）
    SELECTED_LANGUAGES=en,zh_CN            　　　　　　　　 　　// 选择语言
    ORACLE_HOME=/usr/local/oracle/product/11.2.0/db_1  　　　　// oracle_home *路径根据目录情况注意修改 本例安装路径/usr/local/oracle
    ORACLE_BASE=/usr/local/oracle                       　　　 // oracle_base *注意修改
    oracle.install.db.InstallEdition=EE          　　　　　　　 // oracle版本
    oracle.install.db.isCustomInstall=false      　　　　　　　 //自定义安装，否，使用默认组件
    oracle.install.db.DBA_GROUP=dba              　　　　　　　 //dba用户组
    oracle.install.db.OPER_GROUP=oinstall        　　　　　　　 //oper用户组
    oracle.install.db.config.starterdb.type=GENERAL_PURPOSE   //数据库类型
    oracle.install.db.config.starterdb.globalDBName=orcl      //globalDBName
    oracle.install.db.config.starterdb.SID=orcl  　　　　　　　 //SID（**此处注意与环境变量内配置SID一致）
    oracle.install.db.config.starterdb.memoryLimit=81920      //自动管理内存的内存(M)
    oracle.install.db.config.starterdb.password.ALL=oracle    //设定所有数据库用户使用同一个密码
    SECURITY_UPDATES_VIA_MYORACLESUPPORT=false       　　　　　 //（手动写了false）
    DECLINE_SECURITY_UPDATES=true　　　　　　　　　　　　　　　　　// **注意此参数 设定一定要为true
 

#### 设置oracle用户环境

 由root切换至创建好的oracle用户

    su - oracle
 修改该用户的用户配置文件

    vi .bash_profile

 文件内加入并修改至以下内容
    
    export ORACLE_BASE=/usr/local/oracle
    export ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_1
    export ORACLE_SID=orcl    
    export ORACLE_OWNER=oracle
    export PATH=$PATH:$ORACLE_HOME/bin:$HOME/bin

 保存退出后执行source命令立即生效。

    source .bash_profile
  

#### 在oracle用户下开始安装。

 执行命令

/home/database/./runInstaller -silent -force -ignorePrereq -responseFile /usr/local/oracle/db_install.rsp

 参数说明  
    
    /home/database是安装包解压后的路径，此处根据安装包解压所在位置做修改，因人而异。
    runInstaller 是主要安装脚本
    -silent 静默模式
    -force 强制安装
    -ignorePrereq忽略warning直接安装。
    -responseFile读取安装应答文件。
 

#### 查看安装进度
xshell另起窗口并以root登陆。通过 
    
    watch -d -n 2 'du -sh /usr/local/oracle' 
监测oracle安装目录是否变化。或者直接tail -f命令监测安装log日志。
  

#### 等待安装
编译直至出现以下内容，在新创建的root窗口内执行以下提示内的两个脚本，
    
    /usr/oracle/oraInventory/orainstRoot.sh
    /usr/oracle/product/11.2.0/db_1/root.sh

##### 提示信息

-------------------------------------------------------------------

　　To execute the configuration scripts:

　　1. Open a terminal window

　　2. Log in as "root"

　　3. Run the scripts

　　4. Return to this window and hit"Enter" key to continue

 　　Successfully Setup Software.

-------------------------------------------------------------------
 
#### 安装完毕 