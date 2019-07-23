# 大耳朵服务器nginx conf 的配置记录

>服务器nginx配置需求：  
- www.mygallop.cn 是http服务，提供大耳朵后台管理平台的访问；  
- media.mygallop.cn http服务，提供图片、mp3等媒体的访问；
- m.mygallop.cn https服务，对外由nginx提供hppts服务，对内反向代理指向内网的tomcat 8080 的http服务（内网tomcat 不配置https）

## 1、www.conf配置
>www.conf的配置提供 www.mygallop.cn的http服务

配置如下：
```
server {
    listen       80;
    server_name  www.mygallop.cn;

    charset utf-8;
    #access_log  /var/log/nginx/host.access.log  main;
    root /opt/webapp;
    location / {
          #root /opt/webapp;
	      #root /html;
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,x-bigears-admin-token,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        index index.html index.htm;
    }
    #location ^~ /bigears/(.*)\.(css)$ {
    #    add_header Content-Type text/css;
    #    try_files $uri $uri/ /bigears/index.html last;
    #}
    #location ^~ /bigears/(.*)\.(js)$ {
    #    add_header  Content-Type text/javascript;
    #    try_files $uri $uri/ /bigears/index.html last;
    #}
    location /bigears {
        #alias  /opt/app/bigears;
        #index index.html index.htm;
        try_files $uri $uri/ /bigears/index.html last;
    }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}

```

## 2、tomcat.conf
>tomcat.conf 的配置提供https://m.mygallop.cn 的对外htpps服务，对内配置反向代理指向内网的tomcat服务

配置如下：
```
server {
    listen 443 ssl;
    server_name m.mygallop.cn;

    ssl_certificate /etc/nginx/conf.d/ssl/1_m.mygallop.cn_bundle.crt;
    ssl_certificate_key /etc/nginx/conf.d/ssl/2_m.mygallop.cn.key;
    ssl_session_timeout 50m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #请按照这个协议配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#请按照这个套件配置
    ssl_prefer_server_ciphers on;

    charset utf-8;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
       # add_header Access-Control-Allow-Origin *;
        proxy_pass http://172.17.0.12:8080;
    }
    location /bigears {
        proxy_pass http://172.17.0.12:8080;
        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_connect_timeout   90;
        proxy_send_timeout      90;
        proxy_read_timeout      90;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;#获取代理者的真实ip
        proxy_redirect          off;
    }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}
server {
    listen 80;
    server_name m.mygallop.cn; #填写绑定证书的域名
    location / {
        #add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,PATCH,OPTIONS;
        add_header Access-Control-Allow-Headers 'DNT,x-bigears-admin-token,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        #预检请求preflight request不允许重定向，直接返回200
        if ($request_method = 'OPTIONS') {
	          return 200;
	    }
        rewrite ^(.*)$ https://$host$1 permanent; #把http的域名请求转成https
    }

}

```

## 3、media.conf的配置
> media.conf的配置提供后台管理平台及微信小程序端对图片、mp3、mp4等媒体的访问

配置如下：

```
server {
    listen 80 default;
    listen [::]:80 default_server;
    server_name _;
    return 403;
}
server {
    listen       80;
    server_name  media.mygallop.cn;

    charset utf-8;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root /var/webapp;
        index index.html index.htm;
    }
    location ~ .*\.(gif|jpg|png|jpeg|bmp|mp3|mp4)$ {
        root /var/webapp;
        expires 2d;

    }

   # location ~ ^/bigears/ {
   #     root /var/webapp/;
   #     index index.html index.htm;
   # }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}

```

>说明：  
server {  
    listen  80  default;  
    listen [::]:80 default_server;  
    server_name _;  
    return 403;  
}  
上面的配置是禁止直接用ip访问服务器的nginx服务。
