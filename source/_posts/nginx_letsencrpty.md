---
title: CentOS7 Nginx Let's Encrpty 
date: 2017-06-10 01:15:11
tags: Nginx
categories: 开发
toc: true
---

### 关于let's encrpty 
  
   Let's encrpty是一个免费的，自动化的，开放的证书颁发机构。于2015年三季度推出的数字证书认证，将通过旨在消除当前手动创建和安装证书的复杂过程的自动化流程，为安全网站提供免费的SSL/TLS证书。
     [Let's encrypt 维基百科](https://zh.wikipedia.org/wiki/Let%27s_Encrypt)
     [GitHub代码库](https://github.com/letsencrypt)
     [官方文档Let's encrypt](https://letsencrypt.org/getting-started/)

官方推荐使用 [Certbot](https://certbot.eff.org/) 进行证书的颁发安装。所以下一步安装官方工具certbot。

### certbot安装。 

下文以：
    * webroot：example/html/blog 
    * 域名：example.com 
    * 邮箱：example@example.com 
    * / 根路径
为例：


##### ①创建目录
`mkdir letsencrypt`
`cd letsencrypt`
##### ②通过git下载certbot代码库
`git clone https://github.com/certbot/certbot.git`
##### ③执行安装命令 注意替换example
`certbot certonly --webroot -w /example/html/blog -d example.com -d www.example.com -m example@example.com --agree-tos`

<font color=#999 size=2 face="黑体">--agree-tos官方说明
【The first time you run the command, it will make an account, and ask for an email and agreement to the Let's Encrypt Subscriber Agreement; you can automate those with --email and --agree-tos】
</font>

##### ④安装完毕 提示
```IMPORTANT NOTES:
  - Congratulations! Your certificate and chain have been saved at
    /etc/letsencrypt/live/[xxx.xxx.xxx]/fullchain.pem. Your cert will
    expire on 2017-03-20. To obtain a new or tweaked version of this
    certificate in the future, simply run certbot again. To
    non-interactively renew *all* of your certificates, run "certbot
    renew"
  - If you like Certbot, please consider supporting our work by:
 
    Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
    Donating to EFF:                    https://eff.org/donate-le
   ```

##### ⑤证书位置
`/etc/letsencrypt/live/example.com/`

##### ⑥生成DHE参数
```
mkdir /etc/ssl/private/ -p
cd /etc/ssl/private/
openssl dhparam 2048 -out dhparam.pem
```
<font color=#999 size=2 face="黑体"> [关于DHE出处：](https://www.oschina.net/translate/strong_ssl_security_on_nginx)
-- 所有的nginx版本在往Diffiel-Hellman输入参数时依赖OpenSSL。
不幸的时，这就意味着Ephemeral Diffiel-Hellman（DHE）会使用OpenSSL的这一缺陷，包括一个1024位的交换密钥。由于我们正在使用一个2048位的证书，DHE客户端比非ephemeral客户端将使用一个更弱的密钥交换。--
</font>


### Nginx配置

#####  nginx 配置文件
根据nginx安装路径 配置nginx.conf文件 例:`/etc/nginx/conf.d/excample.conf`
```
#http 80

server {
listen 80;
server_name example.com www.example.com;
  #80端口请求重定向https
  rewrite ^ https://$server_name$request_uri? permanent;
}

#https 443

server {
  listen 443 ssl;
  server_name example.com www.example.com;
  charset utf-8;
  
  # ^修改站点根路径^
  root /example/html/blog;
  
  index index.html index.htm;

  # ^ssl_certificate  ssl_certificate_key填前步骤certbot生成文件路径^
  # ^fullchain.pem不修改^
  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
  
  ssl_session_timeout 1d;
  ssl_session_cache shared:SSL:50m;
  ssl_session_tickets on;
  
  # ^此处填写前步骤生成的DHE参数路径^
  ssl_dhparam /etc/ssl/private/dhparam.pem;

  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  
  # ^ssl_ciphers值: https://wiki.mozilla.org/Security/Server_Side_TLS 网站查询复制粘贴至下方ssl_ciphers^
  ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256';
  
  ssl_prefer_server_ciphers on;
}
```

### centos7防火墙开启端口

`firewall-cmd --zone=public --add-port=443/tcp --permanent`
`sudo firewall-cmd --reload`