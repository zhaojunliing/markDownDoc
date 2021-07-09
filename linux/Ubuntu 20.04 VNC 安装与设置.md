Ubuntu 20.04 VNC 安装与设置

VNC是一个远程桌面协议。按照本文的说明进行操作可以实现用VNC对Ubuntu 20.04进行远程控制。一般的VNC安装方式在主机没有插显示器的时候是无法使用的。下面的操作可以在主机有显示器和没有显示器时都能够正常工作。

首先安装x11vnc

```bash
sudo apt-get install x11vnc -y
```

设置vnc密码

```
sudo x11vnc -storepasswd /etc/x11vnc.pass 
```

创建`x11vnc`自启动服务

创建 `/etc/systemd/system/x11vnc.service`，并写入以下内容

```
[Unit]
Description=Start x11vnc at startup.
After=multi-user.target
[Service]
Type=simple
ExecStart=/usr/bin/x11vnc -auth /run/user/1000/gdm/Xauthority -forever -loop -noxdamage -repeat -rfbauth /etc/x11vnc.pass -rfbport 5900 -shared -capslock -nomodtweak
[Install]
WantedBy=multi-user.target
```

启动x11vnc服务

```bash
sudo systemctl enable x11vnc
sudo service x11vnc start
```

此时如果远程的主机上接了显示器，那么就可以在局域网通过VNC进行访问了。

为了能够保证远程主机无论是否有显示器，我们都能通过VNC进行远程访问，我们还要做下面的修改

创建默认的xorg.conf文件

```bash
sudo Xorg :1 -configure
```

此时程序会生成 `/root/xorg.conf.new`文件
我的默认文件内容如下

```
Section "ServerLayout"
	Identifier     "X.org Configured"
	Screen      0  "Screen0" 0 0
	InputDevice    "Mouse0" "CorePointer"
	InputDevice    "Keyboard0" "CoreKeyboard"
EndSection

Section "Files"
	ModulePath   "/usr/lib/xorg/modules"
	FontPath     "/usr/share/fonts/X11/misc"
	FontPath     "/usr/share/fonts/X11/cyrillic"
	FontPath     "/usr/share/fonts/X11/100dpi/:unscaled"
	FontPath     "/usr/share/fonts/X11/75dpi/:unscaled"
	FontPath     "/usr/share/fonts/X11/Type1"
	FontPath     "/usr/share/fonts/X11/100dpi"
	FontPath     "/usr/share/fonts/X11/75dpi"
	FontPath     "built-ins"
EndSection

Section "Module"
	Load  "glx"
EndSection

Section "InputDevice"
	Identifier  "Keyboard0"
	Driver      "kbd"
EndSection

Section "InputDevice"
	Identifier  "Mouse0"
	Driver      "mouse"
	Option	    "Protocol" "auto"
	Option	    "Device" "/dev/input/mice"
	Option	    "ZAxisMapping" "4 5 6 7"
EndSection

Section "Monitor"
	Identifier   "Monitor0"
	VendorName   "Monitor Vendor"
	ModelName    "Monitor Model"
EndSection

Section "Device"
        ### Available Driver options are:-
        ### Values: <i>: integer, <f>: float, <bool>: "True"/"False",
        ### <string>: "String", <freq>: "<f> Hz/kHz/MHz",
        ### <percent>: "<f>%"
        ### [arg]: arg optional
        #Option     "Accel"              	# [<bool>]
        #Option     "AccelMethod"        	# <str>
        #Option     "Backlight"          	# <str>
        #Option     "CustomEDID"         	# <str>
        #Option     "DRI"                	# <str>
        #Option     "Present"            	# [<bool>]
        #Option     "ColorKey"           	# <i>
        #Option     "VideoKey"           	# <i>
        #Option     "Tiling"             	# [<bool>]
        #Option     "LinearFramebuffer"  	# [<bool>]
        #Option     "HWRotation"         	# [<bool>]
        #Option     "VSync"              	# [<bool>]
        #Option     "PageFlip"           	# [<bool>]
        #Option     "SwapbuffersWait"    	# [<bool>]
        #Option     "TripleBuffer"       	# [<bool>]
        #Option     "XvPreferOverlay"    	# [<bool>]
        #Option     "HotPlug"            	# [<bool>]
        #Option     "ReprobeOutputs"     	# [<bool>]
        #Option     "XvMC"               	# [<bool>]
        #Option     "ZaphodHeads"        	# <str>
        #Option     "VirtualHeads"       	# <i>
        #Option     "TearFree"           	# [<bool>]
        #Option     "PerCrtcPixmaps"     	# [<bool>]
        #Option     "FallbackDebug"      	# [<bool>]
        #Option     "DebugFlushBatches"  	# [<bool>]
        #Option     "DebugFlushCaches"   	# [<bool>]
        #Option     "DebugWait"          	# [<bool>]
        #Option     "BufferCache"        	# [<bool>]
	Identifier  "Card0"
	Driver      "intel"
	BusID       "PCI:0:2:0"
EndSection

Section "Screen"
	Identifier "Screen0"
	Device     "Card0"
	Monitor    "Monitor0"
	SubSection "Display"
		Viewport   0 0
		Depth     1
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     4
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     8
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     15
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     16
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     24
	EndSubSection
EndSection
```

将此文件复制至 `/usr/share/X11/xorg.conf.d/xorg.conf`

这个文件保证在主机上插有显示器的时候，系统界面能在显示器上正常显示。下面我们再给系统添加一个虚拟显示器，当主机没有显示器的时候系统就使用这个虚拟显示器。

安装虚拟显卡驱动

```bash
sudo apt install xserver-xorg-video-dummy
```

在这个文件的最后添加下面的内容

```
Section "Monitor"
  Identifier "Monitor1"
  HorizSync   1.0 - 2000.0
  VertRefresh 1.0 - 200.0
  # Add 16:9 modes, others are automatically detected.
  Modeline "1280x720" 74.48 1280 1336 1472 1664 720 721 724 746
  Modeline "1920x1080" 172.80 1920 2040 2248 2576 1080 1081 1084 1118
EndSection


Section "Device"
  Identifier "Card1"
  Driver "dummy"
  VideoRam 256000
EndSection

Section "Screen"
  DefaultDepth 24
  Identifier "Screen1"
  Device "Card1"
  Monitor "Monitor1"
  SubSection "Display"
    Depth 24
    Modes "1920x1080"
  EndSubSection
EndSection
```

这样我们就创建了一个使用虚拟显卡的虚拟显示器。为了让虚拟的显示器和真实显示器都能工作，我们需要把最上面的 ServerLayout 进行更改

```
Section "ServerLayout"
       Identifier     "X.org Configured"
    Screen      0  "Screen0" 0 0
	Screen      1  "Screen1" 0 0
EndSection
```

现在重新启动远程电脑，就可以在没有显示器的情况下远程了。

以上的方法还有一个问题。就是在用户没有登录的其情况下是没办法远程的。所以需要把用户设置成自动登录。

