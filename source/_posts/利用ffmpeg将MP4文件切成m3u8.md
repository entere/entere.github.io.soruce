---
title: 利用ffmpeg将MP4文件切成m3u8
date: 2016-09-20 22:00:21
tags: [ffmpeg, m3u8]
---

利用ffmpeg将MP4文件切成m3u8需要经过两个步骤：

<!-- more -->

1.把MP4文件转成ts

```
ffmpeg -i test.mp4 -codec copy -bsf h264_mp4toannexb test.ts
```

2.把ts转成m3u8

```
ffmpeg -i test.ts -c copy -map 0 -f segment -segment_list playlist.m3u8 -segment_time 2 test%03d.ts
```

使用FFMPEG

这里指FFMPEG提供的命令行（CLI）工具ffmpeg，其使用方法如下（方括号表示可选项，花括号表示必选项目）：

```
ffmpeg [global options] {[infile options]['-i' 'infile'] ...} {[outfile options] 'outfile' ...}
```
参数选项由三部分组成：可选的一组全局参数、一组或多组输入文件参数、一组或多组输出文件参数，其中，每组输入文件参数以‘-i’为结束标记；每组输出文件参数以输出文件名为结束标记。

基本选项
 
能力集列表

```
-formats：列出支持的文件格式。
-codecs：列出支持的编解码器。
-decoders：列出支持的解码器。
-encoders：列出支持的编码器。
-protocols：列出支持的协议。
-bsfs：列出支持的比特流过滤器。
-filters：列出支持的滤镜。
-pix_fmts：列出支持的图像采样格式。
-sample_fmts：列出支持的声音采样格式。
常用输入选项
-i filename：指定输入文件名。
-f fmt：强制设定文件格式，需使用能力集列表中的名称（缺省是根据扩展名选择的）。
-ss hh:mm:ss[.xxx]：设定输入文件的起始时间点，启动后将跳转到此时间点然后开始读取数据。
对于输入，以下选项通常是自动识别的，但也可以强制设定。

-c codec：指定解码器，需使用能力集列表中的名称。
-acodec codec：指定声音的解码器，需使用能力集列表中的名称。
-vcodec codec：指定视频的解码器，需使用能力集列表中的名称。
-b:v bitrate：设定视频流的比特率，整数，单位bps。
-r fps：设定视频流的帧率，整数，单位fps。
-s WxH : 设定视频的画面大小。也可以通过挂载画面缩放滤镜实现。
-pix_fmt format：设定视频流的图像格式（如RGB还是YUV）。
-ar sample rate：设定音频流的采样率，整数，单位Hz。
-ab bitrate：设定音频流的比特率，整数，单位bps。
-ac channels：设置音频流的声道数目。
常用输出选项
-f fmt：强制设定文件格式，需使用能力集列表中的名称（缺省是根据扩展名选择的）。
-c codec：指定编码器，需使用能力集列表中的名称（编码器设定为”copy“表示不进行编解码）。
-acodec codec：指定声音的编码器，需使用能力集列表中的名称（编码器设定为”copy“表示不进行编解码）。
-vcodec codec：指定视频的编码器，需使用能力集列表中的名称（编解码器设定为”copy“表示不进行编解码）。
-r fps：设定视频编码器的帧率，整数，单位fps。
-pix_fmt format：设置视频编码器使用的图像格式（如RGB还是YUV）。
-ar sample rate：设定音频编码器的采样率，整数，单位Hz。
-b bitrate：设定音视频编码器输出的比特率，整数，单位bps。
-ab bitrate：设定音频编码器输出的比特率，整数，单位bps。
-ac channels：设置音频编码器的声道数目。
-an 忽略任何音频流。
-vn 忽略任何视频流。
-t hh:mm:ss[.xxx]：设定输出文件的时间长度。
-to hh:mm:ss[.xxx]：如果没有设定输出文件的时间长度的画可以设定终止时间点。

```