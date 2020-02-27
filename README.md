# how-to-build-a-google-mirror-site
google mirror site build notes

# 环境
机房：Azure 云主机-北美地区
系统：Ubuntu 18.04

# 安装 nginx
`sudo apt install nginx`

# 增加 nginx 配置
`sudo vim /etc/nginx/sites-enabled/google.conf`

```shell
server {
    server_name 40.76.45.xxx;
 
    location / {
        proxy_pass https://www.google.com/;
 
        proxy_redirect https://www.google.com/ /;
        proxy_cookie_domain google.com 40.76.45.xxx;
 
        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header Cookie "PREF=ID=047808f19f6de346:U=0f62f33dd8549d11:FF=2:LD=zh-CN:NW=1:TM=1325338577:LM=1332142444:GM=1:SG=2:S=rE0SyJh2W1IQ-Maw";
        # 这里设置cookie，这里是别人给出的一段，必要时请放上适合你自己的cookie
        # 设置这个可以避免一些情况下的302跳转，如果google服务器返回302 redirect，那么浏览器被跳转到google自己的域名，那就没的玩了
 
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 
        sub_filter  http://www.google.com http://40.76.45.xxx;
        sub_filter  https://www.google.com http://40.76.45.xxx;
        # 这里替换网页中的链接，因为我们的镜像站是http的，所以上面顺便把协议也一起替换了
 }
}

```

> 注：请手动更改配置中的 ip地址 为自己主机的ip地址，或者使用域名代替

# 重启nginx
`sudo systemctl restart nginx`

