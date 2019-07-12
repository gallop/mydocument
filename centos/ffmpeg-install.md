# centos7 上ffmpegµ卸载与安装

## 1、源码安装后卸载
进入到解压后的目录
```
make uninstall

```

不过有些程序没有uninstall ， ./configure 后就能在Makefile中看出。
这样你只能亲自删了， 主要有(/，/usr/，/usr/local/ ...) bin ，etc ，lib ，share 等目录。  
还有就是软件生成的一些目录也要注意一下。
```
rpm -e ffmpeg//卸载时只需要写入包名即可，没有任何提示，则说明已经卸载。提示错误的话，说明要解决它的依赖性。

whereis ffmpeg   //查看哪里还有

rm -rf xxx/ffmpeg

```

## 2、 安装ffmpeg

ffmpeg是一个很强大的音视频处理工具，官网是：http://ffmpeg.org/ 官网介绍ffmpeg是：一个完整的、跨平台的解决方案，可以记录、转换和传输音频和视频。ffmpeg既可以播放视频，也提供命令行工具来处理视频，另外还有强大的视频处理库用于开发，下面是以Linux为例介绍ffmpeg的安装流程的简单的命令行对视频进行转码操作，是ffmpeg中最最简单的入门内容.

　　首先去官网下载源码包，这里下载的是最新的ffmpeg-3.2.14.tar.bz2，下载之后上传至Linux准备安装，首先解压安装包：  

#### 2.1 安装gcc
linux默认安装了gcc，如果你确实没有安装gcc，那么你需要安装一下gcc。  
检查是否安装了gcc：
```
gcc --version
# 此时如果输出版本信息，那么你已经安装了gcc，如果提示`command not found`或者`未找到指令`，那么你需要安装gcc。

```
安装gcc：
```
sudo yum install gcc

```

#### 2.2 安装yasm
FFmpeg中有部分汇编代码，因此需要编译该部分代码，安装yasm即可，你可以认为yasm就是一个汇编编译器。  
下载地址是：http://yasm.tortall.net/Download.html 进入后下载yasm-1.3.0.tar.gz的源码包，执行下面命令安装：
```
tar -xvzf yasm-1.3.0.tar.gz
cd yasm-1.3.0/
./configure
make
make install

```

#### 2.3 安装ffmpeg
安装yasm成功之后继续回到ffmpeg解压后的目录，执行下面命令编译并安装：  
创建安装目录
```
sudo mkdir /usr/local/ffmpeg

```

进入解压后的FFmpeg源码目录，准备执行编译配置操作，执行如下操作：
```
sudo ./configure --prefix=/usr/local/ffmpeg

# configure 用于配置安装信息，比如类库的启用与否、安装的二进制程序的路径，这里使用了--prefix来指定了安装的目录。

```

稍等片刻，即可完成配置。注意观察是否有错误信息输出，如果没有，则继续下一步。  
编译：

```
sudo make

# 该指令会根据第一步的configure配置信息来进行编译操作，FFmpeg使用CC来编译源码，该过程会消耗大约３分钟。

```

安装：
```
sudo make install

#　如无意外，很快会在/usr/local/ffmpeg目录中安装文件，文件结构如下：
# bin/
# lib/
# include/
# share/
# 四个目录。

```

#### 2.4 配置ffmpeg

进入安装目录的bin目录，执行 ./ffmpeg -version 查看当前版本的详细信息，默认情况下一般会报libavdevice.so.57: cannot open shared object file: No such file or directory，原因是lib目录未加载到链接到系统库中，系统ld目录列表在/etc/ld.so.conf中，打开文件会发现，里面引用了/etc/ld.so.conf.d/下面所有的.conf文件，比如mariadb-x86_64.conf我们只需要创建一个文件并写入lib路径即可，执行命令： vim /etc/ld.so.conf.d/ffmpeg.conf 然后添加一行内容：
```
/usr/local/ffmpeg/lib

```
之后保存并退出，然后执行 ldconfig 使配置生效，现在再次执行 ./ffmpeg -version 显示就正常了

最后将ffmpeg 添加到系统的path中，就可以在任意地方使用ffmpeg了  
/etc/profile 修改如下：
```
FFMPEG_HOME=/usr/local/ffmpeg
PATH=$PATH:$FFMPEG_HOME/bin

```
之后，执行 source /etc/profile 使配置文件生效。
