# centos7 关闭防火墙和selinux

### 0. 防火墙

```
临时关闭防火墙  

systemctl stop firewalld

永久防火墙开机禁用

systemctl disable firewalld

临时打开防火墙

systemctl start firewalld

防火墙开机启动

systemctl enable firewalld

查看防火墙状态

systemctl status firewalld
```

### 1. 设置selinux
##### 查看
```
[root@dev-server ~]# getenforce
Disabled
[root@dev-server ~]# /usr/sbin/sestatus -v
SELinux status:                 disabled
```
##### 临时关闭
```
##设置SELinux 成为permissive模式
##setenforce 1 设置SELinux 成为enforcing模式
setenforce 0
```
##### 永久关闭
```
vi /etc/selinux/config
```
>注意：  
/etc/selinux/config文件夹下有一个  
SELINUX=disabled  和  
SELINUXTYPE=targeted  
千万不要改错地方了！！！

将SELINUX=enforcing改为SELINUX=disabled   
设置后需要重启才能生效
