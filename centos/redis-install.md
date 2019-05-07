# redis 安装和配置

### 安装步骤如下：
##### 0. 安装依赖包
```
yum install -y gcc-c++
```

##### 1. 解压redis-4.0.14.tar.gz
```
[root@localhost software_init]# tar -zxvf redis-4.0.14.tar.gz

```

##### 2. 安装
```
[root@localhost software_init]# cd redis-4.0.14
[root@localhost redis-4.0.14]# make
[root@localhost redis-4.0.14]# make install PREFIX=/usr/local/redis
```

##### 3. 移动配置文件到安装目录下
```
[root@localhost redis-4.0.14]# cp redis.conf /usr/local/redis/bin/
```

##### 4. 修改配置
```
[root@localhost redis-4.0.14]# cd /usr/local/redis/bin
[root@localhost bin]# vi redis.conf
```

>配置后台启动 ：   
>把旧值：daemonize no  
>改为新值：daemonize yes
>
>设置外网可访问：  
>把旧值：bind 127.0.0.1  
>改为新值：bing 0.0.0.0
>
>设置密码 ：   
>找到默认是被注释的这一行：# requirepass foobared  
>去掉注释，把 foobared 改为你想要设置的密码，比如我打算设置为：123456，所以我改为：  
>requirepass 123456

##### 5. 其他基本操作
- 启动：`/usr/local/redis/redis-server redis.conf`  
- 关闭：`redis-cli -h 127.0.0.1 -p 6379 shutdown` //如果设置密码，需要加 -a 密码
- 关闭命令也可以用这个：  
  `pkill redis  //停止redis`
- 查看是否启动：`ps -ef | grep redis`
- 进入客户端：`redis-cli`
- 进入客户端输入密码：`auth "123456"`
- 退出客户端：`quit` //关闭连接
- 关闭客户端：`redis-cli shutdown`
- 开机启动配置：`echo "/usr/local/redis/redis-server redis.conf" >> /etc/rc.local`
- 开放防火墙端口：
  - 添加规则：`iptables -I INPUT -p tcp -m tcp --dport 6379 -j ACCEPT`
  - 保存规则：`service iptables save`
  - 重启 iptables：`service iptables restart`
- 卸载redis：  
```
rm -rf /usr/local/redis //删除安装目录
rm -rf /usr/bin/redis-* //删除所有redis相关命令脚本
rm -rf /root/software_init/redis-4.0.14 //删除redis解压文件夹
```
