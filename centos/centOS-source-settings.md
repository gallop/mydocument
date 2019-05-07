# centos7 源设置
## 修改官方源
>国内常用源配置方法（该源和官方源是一样的，只是因为服务器在国内会起到加速作用而已）：

- 163 源：http://mirrors.163.com/.help/centos.html
- 阿里源：http://mirrors.aliyun.com/help/centos
- sohu：http://mirrors.sohu.com/help/centos.html

>CentOS 7 替换过程（这里以 163 源为例）：

- 备份官网源：  
`sudo mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.20190505.backup`
- `cd /etc/yum.repos.d/`
- 下载对应版本 repo 文件, 放入 /etc/yum.repos.d/
- 下载源文件：  
   - CentOS7：`sudo wget http://mirrors.163.com/.help/CentOS7-Base-163.repo`
- `sudo mv CentOS7-Base-163.repo CentOS-Base.repo`
- 导入key：`rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7`
- `sudo yum clean all`
- `sudo yum makecache`
- `sudo yum update -y`
