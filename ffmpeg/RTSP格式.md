# 大华、海康、宇视、华为等厂家摄像头RTSP拉流地址

## 1、大华

**大华摄像机RTSP地址规则为：**

rtsp://[username]:[password]@[ip]:[port]/cam/realmonitor?channel=1&subtype=0
**说明：**

username: 用户名。例如admin。
password: 密码。例如admin123。
ip: 为设备IP。例如 192.168.1.101。
port: 端口号默认为554，若为默认可不填写。
channel: 通道号，起始为1。例如通道2，则为channel=2。
subtype: 码流类型，主码流为0（即subtype=0），辅码流为1（即subtype=1）。

**例如：**

rtsp://admin:admin123@192.168.1.101/cam/realmonitor?channel=1&subtype=1

## 2、海康

**海康摄像机RTSP地址规则为：**

rtsp://[username]:[password]@[ip]:[port]/[codec]/[channel]/[subtype]/av_stream

**说明：**
username: 用户名。例如admin。
password: 密码。例如admin123。
ip: 为设备IP。例如192.168.1.104。
port: 端口号默认为554，若为默认可不填写。
codec：有h264、MPEG-4、mpeg4这几种。
channel: 通道号，起始为1。例如通道1，则为ch1。
subtype: 码流类型，主码流为main，辅码流为sub。

**例如：**

rtsp://admin:admin123@192.168.1.104/h264/ch1/subtype/av_stream

## 3、宇视

**宇视摄像机RTSP地址规则为：**

rtsp://[username]:[password]@[ip]:[port]/media/video1/2/3

**说明：**
username: 用户名。例如admin。
password: 密码。例如admin123。
ip: 为设备IP。例如 192.168.1.107。
port: 端口号默认为554，若为默认可不填写。
video: 1代表主码流、2辅码流、3第三码流

**例如：**

rtsp://admin:admin123@192.168.1.107/media/video2

## 4、华为

**华为摄像机RTSP地址规则为：**

rtsp://[username]:[password]@[ip]:[port]/LiveMedia/[channel]/Media1/2

**说明：**
username: 用户名。例如admin。
password: 密码。例如admin123。
ip: 为设备IP。例如192.168.1.110。
port: 端口号默认为554，若为默认可不填写。
channel: 通道号，起始为1。例如通道1，则为ch1。
Media:1代表主码流、2辅码流

**例如：**

rtsp://admin:admin123@192.168.1.110/LiveMedia/ch1/Media2