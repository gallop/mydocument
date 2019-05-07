# centos7系统vmware-tools安装

> 安装环境说明：
>
> vmware版本：VMware® Workstation 14 Pro <br>
>  centos版本：CentOS-7-x86_64-Minimal-1810

## 详细的配置步骤如下：

### 0. 安装vmware-tools需要编译的工具软件
```
# yum install perl gcc  kernel-headers kernel-devel   ///因为编译需要，要装这几个包
```
### 1. 安装vmware-tools

```
挂载光驱：

# mkdir /mnt/cdrom       ///创建挂载目录
# mount /dev/cdrom /mnt/cdrom    ///将光驱挂载到/mnt/cdrom目录
# cd /mut/cdrom
# cp VMwareTools-10.1.15-6627299.tar.gz /tmp ///复制VMware-tools到/tmp目录
# cd /tmp   ///切换到tmp目录下
# tar -vzxf VMwareTools-10.1.15-6627299.tar.gz ///解压tar包
```

环境好了，下面开始安装VMware-tools
```
# cd vmware-tools-distrib  ///进入解压出来的目录

# ./vmware-install.pl    ///运行vmware-install.pl文件开始安装。
```
会提示若干次yes or no 直接一路回车就行，直到最后出现“Enjoy——the VMware team”的字样，VMwareTools安装完成。

这里安装中一直报这个提示
>The path "" is not a valid path to the 3.10.0-957.el7.x86_64 kernel headers.

解决方案：  
1.按ctrl+z停止安装  
2.安装 kernel-devel
```
# yum install kernel-devel-$(uname -r)  
```

3.重新运行./vmware-install.pl安装

### 2. 卸载：

到刚才解压的目录/tmp/vmware-tools-distrib/bin 目录或者 /usr/bin

```
# cd /tmp/vmware-tools-distrib/bin

# ./vmware-uninstall-tools.pl    ///运行卸载程序

# rm -rf /etc/vmware-caf     ///递归强制 删除残留目录

# rm -rf /etc/vmware-tools   ///递归强制 删除残留目录

#  rm -rvf /usr/lib/vmware-tools     ///递归强制 删除残留目录

```

### 3. 启动

```
# /usr/bin/vmware-user start ///手动启动
```
