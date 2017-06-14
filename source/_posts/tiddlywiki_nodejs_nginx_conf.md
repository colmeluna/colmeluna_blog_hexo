---
title: tiddlywiki Nginx下的部署
date: 2017-06-07 23:42:03
tags: [Nginx]
categories: 开发
toc: true
---

tiddlywiki启动命令

`tiddlywiki --server 8080 $:/core/save/all text/plain text/html zx zx 127.0.0.1`
`nohup tiddlywiki --server 8080 $:/core/save/all text/plain text/html zx zx 127.0.0.1 &`

Nginx设置反向代理
```
location /wiki/ {
  proxy_pass http://127.0.0.1:8080/;
  proxy_set_header        Host             $host;
  proxy_set_header        X-Real-IP        $remote_addr;
  proxy_set_header        X-Forwarded-For  $proxy_add_x_forwarded_for;
}
```
    
   MarkdownPhotos/Res/test.jpg
   
   
   $:/config/tiddlyweb/host
   $protocol$//$host$/wiki/
   
   Use the camera to import photos and the new file will replace the old one
   
1. Load Tiddlywiki on the phone  browser
2. Use the import tools  and select file with phone camera and take a photo and save.

Html5 call the  carmera generated file name is "**image.jpg**" always.
so..this problem will made us to only save the last picture in my wiki repo.  
