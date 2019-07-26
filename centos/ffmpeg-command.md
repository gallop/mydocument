# ffmpeg 常用命令记录

> 本文部分转载自 [ffmpeg基础使用](https://www.jianshu.com/p/ddafe46827b7)
## 一、常用命令
主要参数:
```
-i 设定输入流
-f 设定输出格式
-ss 开始时间
```

视频参数：
```
-b 设定视频流量(码率)，默认为200Kbit/s
-r 设定帧速率，默认为25
-s 设定画面的宽与高
-aspect 设定画面的比例
-vn 不处理视频
-vcodec 设定视频编解码器，未设定时则使用与输入流相同的编解码器

```

音频参数：
```
-ar 设定采样率
-ac 设定声音的Channel数
-acodec 设定声音编解码器，未设定时则使用与输入流相同的编解码器
-an 不处理音频

```

#### 1. 视频格式转换
**（其实格式转换说法不太准确，但大家都这么叫，准确的说，应该是视频容器转换）**
比如一个avi文件，想转为mp4，或者一个mp4想转为ts。
```
ffmpeg -i input.avi output.mp4
ffmpeg -i input.mp4 output.ts

```

#### 2. 提取音频
比如我有一个“晓松奇谈”，可是我不想看到他的脸，我只想听声音， 地铁上可以听，咋办？
```
ffmpeg -i 晓松奇谈.mp4 -acodec copy -vn output.aac

```
上面的命令，默认mp4的audio codec是aac，如果不是会出错，咱可以暴力一点，不管什么音频，都转为最常见的aac。
```
ffmpeg -i 晓松奇谈.mp4 -acodec aac -vn output.aac
```
> (-vn 不处理视频 )

#### 3. 提取视频
我目测有些IT员工，特别是做嵌入式的，比如机顶盒，想debug一下，没有音频的情况下，播放一个视频几天几夜会不会crash，这时候你需要一个纯视频文件，可以这么干。
```
ffmpeg -i input.mp4 -vcodec copy -an output.mp4
```
> -an 不处理音频

#### 4. 视频剪切
经常要测试视频，但是只需要测几秒钟，可是视频却有几个G，咋办？切啊！
下面的命令，就可以从时间为00:00:15开始，截取5秒钟的视频。
```
ffmpeg -ss 00:00:15 -t 00:00:05 -i input.mp4 -vcodec copy -acodec copy output.mp4

```
-ss表示开始切割的时间，-t表示要切多少。上面就是从开始，切5秒钟出来。
> 注意一个问题，ffmpeg 在切割视频的时候无法做到时间绝对准确，因为视频编码中关键帧（I帧）和跟随它的B帧、P帧是无法分割开的，否则就需要进行重新帧内编码，会让视频体积增大。所以，如果切割的位置刚好在两个关键帧中间，那么 ffmpeg 会向前/向后切割，所以最后切割出的 chunk 长度总是会大于等于应有的长度。

#### 5. 码率控制
码率控制对于在线视频比较重要。因为在线视频需要考虑其能提供的带宽。  
那么，什么是码率？很简单： bitrate = file size / duration  
比如一个文件20.8M，时长1分钟，那么，码率就是：  
biterate = 20.8M bit/60s = 20.8*1024*1024*8 bit/60s= 2831Kbps  
一般音频的码率只有固定几种，比如是128Kbps， 那么，video的就是  
video biterate = 2831Kbps -128Kbps = 2703Kbps。

说完背景了。好了，来说ffmpeg如何控制码率。 ffmpg控制码率有3种选择，-minrate -b:v -maxrate  
- b:v主要是控制平均码率。 比如一个视频源的码率太高了，有10Mbps，文件太大，想把文件弄小一点，但是又不破坏分辨率。 ffmpeg -i input.mp4 -b:v 2000k output.mp4上面把码率从原码率转成2Mbps码率，这样其实也间接让文件变小了。目测接近一半。
- 不过，ffmpeg官方wiki比较建议，设置b:v时，同时加上 -bufsize
-bufsize 用于设置码率控制缓冲器的大小，设置的好处是，让整体的码率更趋近于希望的值，减少波动。（简单来说，比如1 2的平均值是1.5， 1.49 1.51 也是1.5, 当然是第二种比较好） `ffmpeg -i input.mp4 -b:v 2000k -bufsize 2000k output.mp4`
- -minrate -maxrate就简单了，在线视频有时候，希望码率波动，不要超过一个阈值，可以设置maxrate。`ffmpeg -i input.mp4 -b:v 2000k -bufsize 2000k -maxrate 2500k output.mp4`

#### 6. 视频编码格式转换
比如一个视频的编码是MPEG4，想用H264编码，咋办？  
```
ffmpeg -i input.mp4 -vcodec h264 output.mp4

```
相反也一样
```
ffmpeg -i input.mp4 -vcodec mpeg4 output.mp4

```
当然了，如果ffmpeg当时编译时，添加了外部的x265或者X264，那也可以用外部的编码器来编码。（不知道什么是X265，可以Google一下，简单的说，就是她不包含在ffmpeg的源码里，是独立的一个开源代码，用于编码HEVC，ffmpeg编码时可以调用它。当然了，ffmpeg自己也有编码器）
```
ffmpeg -i input.mp4 -c:v libx265 output.mp4
ffmpeg -i input.mp4 -c:v libx264 output.mp4

```

#### 7. 过滤器的使用
##### 7.1 将输入的1920x1080缩小到960x540输出:
```
ffmpeg -i input.mp4 -vf scale=960:540 output.mp4

```
> 如果540不写，写成-1，即scale=960:-1, 那也是可以的，ffmpeg会通知缩放滤镜在输出时保持原始的宽高比。

##### 7.2 为视频添加logo
比如我有一个logo，需要加到视频上，命令如下：
```
ffmpeg -i input.mp4 -i iQIYI_logo.png -filter_complex overlay output.mp4

```
#### 8. 抓取视频的一些帧，存为jpeg图片
比如，一个视频，我想提取一些帧，存为图片，咋办？  
```
ffmpeg -i input.mp4 -r 1 -q:v 2 -f image2 pic-%03d.jpeg

```
> -r 表示每一秒几帧  
-q:v表示存储jpeg的图像质量，一般2是高质量。

如此，ffmpeg会把input.mp4，每隔一秒，存一张图片下来。假设有60s，那会有60张。60张？什么？这么多？不要不要。。。。。不要咋办？？ 可以设置开始的时间，和你想要截取的时间呀。  
```
ffmpeg -i input.mp4 -ss 00:00:20 -t 10 -r 1 -q:v 2 -f image2 pic-%03d.jpeg

```
> -ss 表示开始时间  
-t表示共要多少时间。

如此，ffmpeg会从input.mp4的第20s时间开始，往下10s，即20~30s这10秒钟之间，每隔1s就抓一帧，总共会抓10帧。

## 二、 大耳朵一乐用到的命令

#### 1. 降码率，将mp4文件大小减小，
```
ffmpeg -i 恋爱循环.mp4 -b:v 850k lianai5.mp4

```

#### 2. 截图封面：
```
ffmpeg -ss 00:00:01 -y -i lianai5.mp4 -vframes 1 lianai.jpg
ffmpeg -ss 00:02:06 -y -i 大腕宽面.mp4 -vframes 1 大腕宽面.jpg

```

#### 3. 添加水印
```
ffmpeg -i test5.mp4 -i yile3.png -filter_complex overlay test6.mp4

```

#### 4. 将mp4 转成 m3u8格式
```
ffmpeg -i 恋爱循环.mp4 -c copy -vbsf h264_mp4toannexb test.ts
ffmpeg -i test.ts -c copy -map 0 -f segment -segment_list 恋爱循环-new.m3u8 -segment_time 3 恋爱循环%03d.ts

```
> -segment_time: 为每隔几秒切一个分片
