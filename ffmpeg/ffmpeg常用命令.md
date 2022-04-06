```shell
// 去掉视频中的音频
ffmpeg -i input.mp4 -vcodec copy -an output.mp4
// -an: 去掉音频；-vcodec:视频选项，一般后面加copy表示拷贝
 
// 提取视频中的音频
ffmpeg -i input.mp4 -acodec copy -vn output.mp3
// -vn: 去掉视频；-acodec: 音频选项， 一般后面加copy表示拷贝
 
// 音视频合成
ffmpeg -y –i input.mp4 –i input.mp3 –vcodec copy –acodec copy output.mp4
// -y 覆盖输出文件
 
//剪切视频
ffmpeg -ss 0:1:30 -t 0:0:20 -i input.mp4 -vcodec copy -acodec copy output.mp4
// -ss 开始时间; -t 持续时间
 
// 视频截图
ffmpeg –i test.mp4 –f image2 -t 0.001 -s 320x240 image-%3d.jpg
// -s 设置分辨率; -f 强迫采用格式fmt;
 
// 视频分解为图片
ffmpeg –i test.mp4 –r 1 –f image2 image-%3d.jpg
// -r 指定截屏频率
 
// 将图片合成视频
ffmpeg -f image2 -i image%d.jpg output.mp4
 
//视频拼接
ffmpeg -f concat -i filelist.txt -c copy output.mp4
 
// 将视频转为gif
ffmpeg -i input.mp4 -ss 0:0:30 -t 10 -s 320x240 -pix_fmt rgb24 output.gif
// -pix_fmt 指定编码
 
// 将视频前30帧转为gif
ffmpeg -i input.mp4 -vframes 30 -f gif output.gif
 
// 旋转视频
ffmpeg -i input.mp4 -vf rotate=PI/2 output.mp4
 
// 缩放视频
ffmpeg -i input.mp4 -vf scale=iw/2:-1 output.mp4
// iw 是输入的宽度， iw/2就是一半;-1 为保持宽高比
 
//视频变速
ffmpeg -i input.mp4 -filter:v setpts=0.5*PTS output.mp4
 
//音频变速
ffmpeg -i input.mp3 -filter:a atempo=2.0 output.mp3
 
//音视频同时变速，但是音视频为互倒关系
ffmpeg -i input.mp4 -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mp4
 
 
// 视频添加水印
ffmpeg -i input.mp4 -i logo.jpg -filter_complex [0:v][1:v]overlay=main_w-overlay_w-10:main_h-overlay_h-10[out] -map [out] -map 0:a -codec:a copy output.mp4
// main_w-overlay_w-10 视频的宽度-水印的宽度-水印边距


// 截取视频局部
ffmpeg -i in.mp4 -filter:v "crop=out_w:out_h:x:y" out.mp4
// 截取部分视频，从[80,60]的位置开始，截取宽200，高100的视频
ffmpeg -i in.mp4 -filter:v "crop=80:60:200:100" -c:a copy out.mp4
// 截取右下角的四分之一
ffmpeg -i in.mp4 -filter:v "crop=in_w/2:in_h/2:in_w/2:in_h/2" -c:a copy out.mp4
// 截去底部40像素高度
ffmpeg -i in.mp4 -filter:v "crop=in_w:in_h-40" -c:a copy out.mp4


ffmpeg  -i C:\Users\zhaojunli\Desktop\mda-ma7rvnsg4qjih3f7.mp4  -ss 0 -to 38.4 C:\Users\zhaojunli\Desktop\1111.mp4 -y


ffmpeg  -i C:\Users\zhaojunli\Desktop\mda-ma7rvnsg4qjih3f7.mp4 -filter:v "crop=1:264:528:466" -c:a copy C:\Users\zhaojunli\Desktop\222.mp4

ffmpeg –i C:\Users\zhaojunli\Desktop\mda-ma7rvnsg4qjih3f7.mp4 –f image2 -t 30 image-%3d.jpg


264 730
```

