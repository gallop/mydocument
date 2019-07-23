# 关于preflight request的知识

## 背景
不知道大家有没有发现，有时候我们在调用后台接口的时候，会请求两次，其实第一次发送的就是preflight request(预检请求)，那么这篇文章将讲一下，为什么要发预检请求，什么时候会发预检请求，预检请求都做了什么

## 为什么要发预检请求
我们都知道浏览器的同源策略，就是出于安全考虑，浏览器会限制从脚本发起的跨域HTTP请求，像XMLHttpRequest和Fetch都遵循同源策略。
浏览器限制跨域请求一般有两种方式：
- 浏览器限制发起跨域请求
- 跨域请求可以正常发起，但是返回的结果被浏览器拦截了

一般浏览器都是第二种方式限制跨域请求，那就是说请求已到达服务器，并有可能对数据库里的数据进行了操作，但是返回的结果被浏览器拦截了，那么我们就获取不到返回结果，这是一次失败的请求，但是可能对数据库里的数据产生了影响。  
为了防止这种情况的发生，规范要求，对这种可能对服务器数据产生副作用的HTTP请求方法，浏览器必须先使用OPTIONS方法发起一个预检请求，从而获知服务器是否允许该跨域请求：如果允许，就发送带数据的真实请求；如果不允许，则阻止发送带数据的真实请求。

>也就是说所有跨域的非简单请求都需要preflight request预检  
关于预检的介绍可以查看[preflight request介绍](https://www.jianshu.com/p/b55086cbd9af)

>注意：使用GET HEAD POST的方法，如果有自定义的header 也属于非简单请求，如果有跨域访问，也需要preflight request

“需预检的请求”要求必须首先使用OPTIONS方法发起一个预检请求到服务区，以获知服务器是否允许该实际请求。“预检请求”的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

## 部署大耳朵项目时出现Redirect is not allowed for a preflight request的解决办法

##### 重定向preflight 请求
任何非 2xx 状态码都认为 preflight 失败， 所以 preflight 不允许重定向。所以当一个跨域非简单请求通过中间服务器又需要重定向时，浏览器控制台会出现Redirect is not allowed for a preflight request的错误。

>问题场景描述：  
&emsp;&emsp;大耳朵服务器用nginx提供http服务，tomcat 提供服务端服务，tomcat的8080端口不对外开放，通过nginx做方向代理，有三个域名，分别如下:  
- www.mygallop.cn 是http服务，提供大耳朵后台管理的访问；  
- media.mygallop.cn http服务，提供图片、mp3等媒体的访问；
- m.mygallop.cn https服务，对外由nginx提供hppts服务，对内反向代理指向内网的tomcat 8080 的http服务（内网tomcat 不配置https）  

>当www.mygallop.cn的后台管理系统（vue开发），操作获取数据需要请求https://m.mygallop.cn/bigears/admin 操作，如果session 超时或无权限，后端（tomcat上运行）服务会重定向到http://m.mygallop.cn/bigears/auth/401(因为tomcat未配置https，所以这里重定向的是http),http://m.mygallop.cn/bigears/auth/401 对后台管理系统www.mygallop.cn是跨域的非简单请求，但因为服务端对外只提供https服务，所以当请求http://m.mygallop.cn/bigears/auth/401时，nginx会重定向为https://m.mygallop.cn/bigears/auth/401,  
此时问题来了，因为是跨域的非简单请求，所以需要preflight request，但在preflight request时，nginx又会重定向为https服务，但preflight request是不允许重定向的，所以会报Redirect is not allowed for a preflight request

#### 解决的办法是在中间服务器nginx 将http服务重定向为https服务时，判断OPTIONS请求，直接返回200，具体配置如下：

```
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
>注意：因为是跨域访问，这里重定向时需要加上header：  
Access-Control-Allow-Origin  
Access-Control-Allow-Methods  
Access-Control-Allow-Headers




##### 重定向简单请求
对于简单请求浏览器会跳过 preflight 直接发送真正的请求。 该请求被重定向后浏览器会直接访问被重定向后的地址，也可以跟随多次重定向。 但重定向后请求头字段origin会被设为"null"（被认为是 privacy-sensitive context）。 这意味着响应头中的`Access-Control-Allow-Origin`需要是*或null（该字段不允许多个值）。
