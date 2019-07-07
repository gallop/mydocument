# nginx快速安装
> nginx 官方网站有，地址如下：
> http://nginx.org/en/linux_packages.html#RHEL-CentOS

大概步骤如下：
## 1、Install the prerequisites:
```
sudo yum install yum-utils

```

## 2、To set up the yum repository, create the file named /etc/yum.repos.d/nginx.repo with the following contents:
```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

```
>我的centos版本是7.2的，所以我的baseurl 改成如下：  
>baseurl=http://nginx.org/packages/centos/7/$basearch/  
>另外可以把gpgcheck=0，免去安装包验证

## 3、To install nginx, run the following command:
```
sudo yum install nginx
```

When prompted to accept the GPG key, verify that the fingerprint matches 573B FD6B 3D8F BC64 1079 A6AB ABF5 BD82 7BD9 BF62, and if so, accept it.
