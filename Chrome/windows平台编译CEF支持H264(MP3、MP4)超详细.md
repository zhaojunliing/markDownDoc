# windows平台编译CEF支持H264(MP3、MP4)超详细

编译目标（如何确定目标定版本请查看：[BranchesAndBuilding](https://bitbucket.org/chromiumembedded/cef/wiki/BranchesAndBuilding.md)）

```
CEF Branch：5414
CEF Commit：ca3ebf119e
Chromium Version：109.0.5414.87

具体版本信息可通过cef CHROMIUM_BUILD_COMPATIBILITY.txt 确定
```

## 编译环境准备

### 1 安装或修改VS2022

*VS版本和SDK版本可查看：[BranchesAndBuilding](https://bitbucket.org/chromiumembedded/cef/wiki/BranchesAndBuilding.md)*

1）在“**在工作负荷**”勾选“**使用C++的桌面开发**”

![image-20231204155204969](F:\github\markDownDoc\Chrome\assets\image-20231204155204969.png)

 2）在**“单个组件”**勾选**“Windows 10 SDK (10.0.20348.0)”**

![image-20231204155109659](F:\github\markDownDoc\Chrome\assets\image-20231204155109659.png)

3）安装或修改完成后在**“控制面板”**/**“程序”**/**“卸载程序”**中找到刚才对应的SDK，**“右键”**/**“更改”**

![image-20231204154902232](F:\github\markDownDoc\Chrome\assets\image-20231204154902232.png)

4）找到“Debgging Tools for Windows”，然后勾选

![image-20231204154754101](F:\github\markDownDoc\Chrome\assets\image-20231204154754101.png)

### 2 网络代理工具和代理配置

1）一个稳定的代理, 比如: [V2free](https://w1.ddnsgo.xyz/auth/register?code=7WtA)

2）配置git 代理（[v2rayN](https://github.com/2dust/v2rayN)本地服务地址为http://127.0.0.1:10809）

```
::设置代理（执行一次即可）
git config --global http.proxy http://192.168.10.72:1080
git config --global https.proxy http://192.168.10.72:1080
::取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 3 关闭python关联

需要关闭 win10 系统中的 python 别名（开始菜单→设置→应用→应用执行别名）：

![image-20231204155422417](F:\github\markDownDoc\Chrome\assets\image-20231204155422417.png)

## CEF 源码编译流程

### 1 创建工作目录

```
md E:\chrome\cef\
md C:\chrome\chromium\src\
md C:\chrome\depot_tools\
```

### 2 添加环境变量

```
:: 代理
setx HTTP_PROXY "http://192.168.10.72:1080"
setx HTTPS_PROXY "http://192.168.10.72:1080"

:: 禁止depot_tools自动更新
setx DEPOT_TOOLS_UPDATE "0"

:: 使用本地安装的 Visual Studio 版本
setx DEPOT_TOOLS_WIN_TOOLCHAIN "0"

:: GN 构建工具
setx CEF_USE_GN "1"

:: 使用 VS2019 创建项目
setx GN_ARGUMENTS "--ide=vs2022 --sln=cef --filters=//cef/*"

:: 使用官方构建并添加ffmpeg音视频解码
setx GN_DEFINES "is_official_build=true proprietary_codecs=true ffmpeg_branding=Chrome"

:: 将depot_tools路径("E:\chrome\depot_tools\)添加到用户环境变量Path
for /f "tokens=3,*" %i in ('reg query HKCU\Environment /v Path') do setx Path "E:\chrome\depot_tools\;%i%j"
```

*执行以上命令即可在系统中创建环境变量 (记得不用的时候把这些环境变量删除了)*

### 3 下载源码

1）下载cef

```
cd E:\chrome

:: clone源码并切换至5414分支,提交版本可能大于87，手动回滚
git clone https://bitbucket.org/chromiumembedded/cef.git -b 5414
```

2）下载chromium

```
cd E:\chrome\chromium

:: clone指定tag(109.0.5414.87)的源码, (cef 5414分支 对应chromiun tag: 109.0.5414.87)
:: "--depth 1": 只下载当前版本不需要历史提交记录, 否则耗时严重且浪费储存空间或者意外中断
git clone https://chromium.googlesource.com/chromium/src.git -b 109.0.5414.87 --depth 500
```

3）下载depot_tools

```
cd E:\chrome

:: clone depot_tools工具
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git

cd E:\chrome\depot_tools

:: 临时允许更新
set DEPOT_TOOLS_UPDATE=1
:: 执行update_depot_tools.bat下载内部工具
update_depot_tools.bat
:: 禁止更新
set DEPOT_TOOLS_UPDATE=0

:: checkout, (cef 5414分支 对应depot_tools commit id: ca3ebf119e)
checkout ca3ebf119e
```

注意: cef与chromium和depot_tools对应版本关系在 "cef\CHROMIUM_BUILD_COMPATIBILITY.txt"

![image-20231204153155022](F:\github\markDownDoc\Chrome\assets\image-20231204153155022.png)

### 4 gclient 同步chromium依赖项目

1）创建.gclient文件

```
cd E:\chrome\chromium

:: 通过命令创建.gclient文件
gclient config  https://chromium.googlesource.com/chromium/src.git --unmanaged
```

2）执行gclient sync

```
cd E:\chrome\chromium

gclient sync --nohooks --no-history
```

*此命令执行过程中可能会中途暂停或失败，耐心等待或重复执行此命令直到成功为止*

3）执行gclient runhooks

```
cd E:\chrome\chromium

:: 如果前面已经设置了此环境变量, 可以不用执行
set DEPOT_TOOLS_WIN_TOOLCHAIN=0

gclient runhooks
```

*此命令执行过程中可能会中途暂停或失败，耐心等待或重复执行此命令直到成功为止*

### 5 构建项目

1）复制 E:\chrome\cef 到 E:\chrome\chromium\src

```
xcopy "E:\chrome\cef" "E:\chrome\chromium\src\cef\" /e

复制完手动把cef下的.git文件也复制到E:\chrome\chromium\src\cef\下
```

2）检查环境

```shell
E:\chrome\chromium>python src/build/vs_toolchain.py get_toolchain_dir
vs_path = "D:\\Program Files\\Microsoft Visual Studio\\2022\\Enterprise"
sdk_path = "D:\\Windows Kits\\10"
vs_version = "2022"
wdk_dir = ""
runtime_dirs = "C:\\Windows\\System32;C:\\Windows\\SysWOW64;Arm64Unused"
```

3）构建项目

```
cd E:\chrome\chromium\src\cef

:: 如果前面已经设置了这些环境变量则可以跳过这3条命令
set GN_DEFINES=is_component_build=false //注意这个不要开，不然后面不好打包，插件太多
set CEF_USE_GN=1
//set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*
:: 开启H264支持，有个头文件要修改，自行百度
//set GN_DEFINES=is_official_build=true proprietary_codecs=true ffmpeg_branding=Chrome

set GN_DEFINES=is_official_build=true ffmpeg_branding=Chrome proprietary_codecs=true use_thin_lto=false
::set GN_DEFINES=is_official_build=true use_thin_lto=false proprietary_codecs=true ffmpeg_branding=Chrome media_use_ffmpeg=true media_use_libvpx=true rtc_use_h264=true
set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/* --enable-decoder='rv10,rv20,rv30,rv40,cook,h263,h263i,mpeg4,msmpeg4v1,msmpeg4v2,msmpeg4v3,amrnb,amrwb,ac3,flv' --enable-demuxer='rm,mpegvideo,avi,avisynth,h263,aac,amr,ac3,flv,mpegts,mpegtsraw' --enable-parser='mpegvideo,rv30,rv40,h263,mpeg4video,ac3'

:: 构建项目
call cef_create_projects.bat
```

如果中间出错，可以把对应的命令复制出来单独执行，查看具体报错原因

```shell
信任文件夹
git config --global --add safe.directory E:/chrome/chromium/src

没有找到windows10 SDK
python3 E:/chrome/chromium/src/build/toolchain/win/setup_toolchain.py "D:\Program Files\Microsoft Visual Studio\2022\Enterprise\Common7\IDE" "D:\Windows Kits\10" "C:\Windows\System32;C:\Windows\SysWOW64;Arm64Unused" win x86 environment.x86


python3 E:/chrome/chromium/src/tools/update_pgo_profiles.py --target win32 get_profile_path
```

### 6 编译

```
cd E:\chrome\chromium\src

:: 编译32位的
ninja -C out/Release_GN_x86 cef
:: 编译64位的
ninja -C out/Release_GN_x64 cef
```

### 7 打包

```
:: 设置vcvars 变量,我设置的32位的
set CEF_VCVARS=D:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Auxiliary\Build\vcvars32.bat

cd E:\chrome\chromium\src\cef\tools

:: 打包客户端的
make_distrib.bat --ninja-build --client
:: 打包32位的
make_distrib.bat --allow-partial --sandbox --ninja-build --no-archive --no-symbols --no-docs --minimal
:: 打包64位的
make_distrib.bat --allow-partial --sandbox --ninja-build --no-archive --no-symbols --no-docs --minimal
```

在 E:\chrome\chromium\src\cef\binary_distrib 目录下就可以看到打包过的文件了

### 8 其他

1）如果在构建或编译过程中遇到python gbk编码错误, 找到报错的代码添加 utf-8 编码即可, 比如:

```
      if (environment_block_name != ''):
        env_block = _FormatAsEnvironmentBlock(env)
        with open(environment_block_name, 'w', encoding='utf-8') as f:
          f.write(env_block)
```

 ### 9、测试网址

```url
https://html5test.com/
```

### 附

**取消Git全局代理**

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```

**取消winhttp全局代理**

```
netsh winhttp reset proxy
```

```she
set vs_root = D:\Program Files\Microsoft Visual Studio\2022\Enterprise
set sdk_root = D:\Windows Kits\10
set vs_version = 2022
set sdk_version=10.0.20348
set vc_tools_version=14.32.31326


set WIN_CUSTOM_TOOLCHAIN=1
set CEF_VCVARS=none
set GYP_MSVS_OVERRIDE_PATH=%vs_root%
set SDK_ROOT=%sdk_root%
set PATH=%sdk_root%\bin\%sdk_version%\x64;%vs_root%\VC\Tools\MSVC\%vc_tools_version%\bin\HostX64\x64;%vs_root%\VC\Redist\MSVC\%vc_redist_version%\x64\%vc_redist_crt%;%vs_root%\SystemCRT;%PATH%
set LIB=%sdk_root%\Lib\%sdk_version\um\%arch%;%sdk_root%\Lib\%sdk_version\ucrt\%arch%;%vs_root%\VC\Tools\MSVC\%vc_tools_version%\lib\%arch%;%vs_root%\VC\Tools\MSVC\%vc_tools_version%\atlmfc\lib\%arch%;%LIB%
set INCLUDE=%sdk_root%\Include\%sdk_version%\um;%sdk_root%\Include\%sdk_version%\ucrt;%sdk_root%\Include\%sdk_version%\shared;%vs_root%\VC\Tools\MSVC\%vc_tools_version%\include;%vs_root%\VC\Tools\MSVC\%vc_tools_version%\atlmfc\include;%INCLUDE%



set CEF_VCVARS=D:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Auxiliary\Build\vcvars32.bat

```

